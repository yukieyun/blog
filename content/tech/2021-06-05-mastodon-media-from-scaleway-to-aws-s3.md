---
title: Mastodon媒体库迁移：从Scaleway到AWS S3
author: 云五
type: post
date: 2021-06-06T01:35:59+00:00
description: |
  本次将Mastodon的媒体库从Scaleway的云存储迁移到AWS的S3的记录；最后也简述了在安装Mastodon后直接使用AWS的S3的方法。
  
  免责声明：
  1. 本文不是一个step by step的指导，可能有疏漏，对可能造成的损失概不负责；建议使用前反复阅读文中提到的其他指南文章和文档（写得比本文详尽且负责），在了解原理后再行操作。
  2. 本文的重点在于其他迁移指南里没有提到的AWS S3的使用和可能遇到的问题。
url: /tech/mastodon-media-from-scaleway-to-aws-s3/
categories:
  - 技术
tags:
  - AWS
  - Mastodon
  - S3
  - 长毛象

---
本次将Mastodon的媒体库从Scaleway的云存储迁移到AWS的S3的记录；最后也简述了在安装Mastodon后直接使用AWS的S3的方法。

免责声明：
1. 本文不是一个step by step的指导，可能有疏漏，对可能造成的损失概不负责；建议使用前反复阅读文中提到的其他指南文章和文档（写得比本文详尽且负责），在了解原理后再行操作。
2. 本文的重点在于其他迁移指南里没有提到的AWS S3的使用和可能遇到的问题。

## 背景介绍

在去年年尾轰轰烈烈的Mastodon建站热潮中，我也照着网上的各种小白教程开通了驴肉火烧站https://go5.dev 随后又按照[这篇文章](https://pullopen.github.io/%E7%AB%99%E7%82%B9%E7%BB%B4%E6%8A%A4/2020/07/22/Move-mastodon-media-to-Scaleway.html "这篇文章")迁移了媒体库，免费的服务，不用白不用嘛。

Scaleway的免费服务包括75G免费空间和每个月75G的流量，根据我的火烧站运行8个月（注册用户700+，活跃用户200+，加入两个中继站和fediverse沟通）的经验来看，基本是够用的（因为我瞎折腾付过不到0.1USD的账单）。

但今年Scaleway服务屡次不稳定，起初以为只有荷兰区服务不行，就照着[椒盐豆豉老师的博客里写的迁移指南](https://blog.douchi.space/?p=1301 "椒盐豆豉老师的博客里写的迁移指南")迁移到了巴黎区。没想到没过两周，巴黎区也不稳定起来，疑似Scaleway调整了rate limiter的设置，导致外站图片拉取时缩略图上传失败。

中间还伴随着疑似此问题导致的传图故障，让我决定迁移到更稳定的服务上。在直接给Mastodon的服务器硬盘扩容和使用AWS S3之间犹豫了一下，决定采用后者。

## AWS的S3服务和准备工作

AWS S3的定价可看这个链接：https://aws.amazon.com/s3/pricing/ 注意不止是storage要计费，包括后面的requests也是一大项。本站运行八个月，累积媒体库50G左右，照增速来看，3-5年内可能增至400-500G。requests数我没有太注意观测所以目前不好说，纯靠猜的应该不会超过5 USD per month；以后增长的话，一段时间内应该也不至于致贫。

（具体花费多少等我用两个月后再更新。）

1. 注册了AWS账号，在 Service里选择S3创建bucket，bucket名建议使用字母（和数字），不要包含其他符号。之后遇到第一个问题：AWS默认不允许root用户直接进行各种数据写入操作，需要另行创建IAM用户来操作。

2. 在 Service里选择IAM，创建IAM用户。创建时请注意保存Access Key ID和Access Secret，因为你只有一次机会看到Access Secrets；如果没有保存下来的话，就只能删掉这个Key ID，重新生成一次。

3. 创建IAM用户并为用户设置用户组后，比较关键的一步是为用户组新增权限，直接在AWS巨长的权限列表里搜索S3，选择AmazonS3FullAccess即可。简单地说，这一步是创建一个用户（组），用于以后所有对S3的操作。

4. 在S3中创建一个bucket，和默认设置相比需要更改的选项是“Block public access (bucket settings)”，把这一项的勾去掉，否则后面无法使用rclone或其他工具进行写入操作；创建时忘记改的话，创建后在permissions里改。至于地区选择看个人需求吧，我选了法国区。

## 迁移过程

迁移使用的工具是[rclone](https://rclone.org/s3/ "rclone") 请预先随意创建两个bucket，测试一下使用，以免造成无法挽回的损失。

因为Scaleway疑似加强了rate limiter的限制，又或者是法国区最近性能不佳，最初rclone copy时特别慢，后来速度稳定后大概是1 MB/s，也就是说3.6GB/h（荷兰区->aws法国，法国区->aws法国），可以靠这个预估一下迁移时间。

为了保证用户的使用体验，我采取的步骤是：
1. 更改Mastodon的.env.production和nginx设置，先把媒体库链接强行切换至AWS S3，这样用户可以传图、看新图，但不能看老图（包括头像、表情包等）；虽然不能看老图，但比一直卡着不能看图传图感觉好一点；
2. 使用rclone copy将Scaleway的数据复制到AWS S3。

使用rclone的步骤：
1. 使用rclone config加入源存储和目标存储的配置，这一步比较长，请仔细阅读上面的rclone 文档链接，配置好后可使用rclone lsd your_remote_config:your_bucket_name 来测试是否配置正确，配置正确的话能看到你的bucket里的目录列表；
2. 使用rclone copy迁移数据（请勿使用rclone sync，因为copy是复制所有数据，sync则会删除目标存储里源存储里不存在的内容）。

rclone copy命令：
```bash
rclone copy --progress your_source_config:your_source_bucket_name your_destination_config:your_destination_bucket_name
```

## .env.production和nginx里的配置修改

.env.production的最后加入S3配置（迁移的话修改这一部分）
```
S3_REGION=your_region
S3_ENABLED=true
S3_BUCKET=your_bucket_name
AWS_ACCESS_KEY_ID=your_aws_access_key_id
AWS_SECRET_ACCESS_KEY=your_aws_access_secret
S3_PROTOCOL=https
S3_HOSTNAME=your_mastodon_media_host
S3_ENDPOINT=https://s3.$S3_REGION.amazonaws.com
```

其中：
- S3_REGION是你的S3所在区，比如我选了欧洲法国区，代号是eu-west-3；
- S3_HOSTNAME是你的Mastodon的媒体库域名，我的Mastodon站点域名是go5.dev，媒体库域名就是media.go5.dev
- AWS_ACCESS_KEY_ID和AWS_SECRET_ACCESS_KEY分别填入创建IAM用户时的key ID和access secret
- 如果你直接搜索Mastodon AWS S3，网上有一个英文指南，里面把S3_ENDPOINT写错了

在nginx里，修改以前媒体库服务器的配置。
```
location /your_bucket_name/ {
       proxy_cache mastodon_media;
       proxy_cache_revalidate on;
       proxy_buffering on;
       proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
       proxy_cache_background_update on;
       proxy_cache_lock on;
       proxy_cache_valid 1d;
       proxy_cache_valid 404 1h;
       proxy_ignore_headers Cache-Control;
       add_header X-Cached $upstream_cache_status;
       add_header 'Access-Control-Allow-Origin' '*';
       proxy_pass https://your_bucket_name.s3.your_s3_region.amazonaws.com/;
}
```
即修改your_bucket_name和your_s3_region这两处。

## 安装后Mastodon后直接迁移至AWS S3

写这篇博客时迁移还未完成，但刷新图速度已经感受到明显差异了，Jeff的服务不是盖的。如果不想反复迁移遭罪，建议直接上AWS S3。

方法么……很简单，请直接参照本文开始时引用的pullopen的迁移到Scaleway的文章，将里面的Scaleway的各种endpoint和region直接改成AWS S3的地址即可。

Scaleway和AWS S3之间的迁移是比较平滑的，因为Scaleway提供的Object Storage本身就是S3 Compatible的，和AWS S3是同一原理。
