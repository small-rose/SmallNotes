---
layout: default
title: Chatgpt-on-wechat
parent: Git
nav_order: 20
---

# Chatgpt-on-wechat
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }



1. TOC
{:toc}

---


代码仓库： https://github.com/zhayujie/chatgpt-on-wechat


## 微信公众号-接接入AI


前置准备:

1）微信公众号1个

2）云服务器

3）nginx环境

4）docker环境


### docker 部署

(1) 配置 docker-compose.yml

执行以下命令下载 docker-compose.yml：

```bash
wget https://open-1317903499.cos.ap-guangzhou.myqcloud.com/docker-compose.yml
```

接着打开文件，修改或添加所需的配置，如 OPEN_AI_API_KEY 和 GROUP_NAME_WHITE_LIST 等必填配置，其他配置参考 配置说明。

(2) 启动容器

在 docker-compose.yml 所在目录下执行以下命令启动容器：

```bash
sudo docker compose up -d
```
运行 sudo docker ps 能查看到 NAMES 为 chatgpt-on-wechat 的容器即表示运行成功。
提示

> 如果 docker-compose 是 1.X 版本 则需要执行 sudo docker-compose up -d 来启动容器
  该命令会自动去 docker hub 拉取 latest 版本的镜像，latest 镜像会在每次项目 release 新的版本时生成

最后运行以下命令可查看容器运行日志，扫描日志中的二维码即可完成登录：

```bash
sudo docker logs -f chatgpt-on-wechat
```
(3) 停止容器

执行以下命令可以关闭服务：
```bash
sudo docker compose down
```

注意：如果修改了 docker-compose.yml 中的配置，需要关闭容器后再重新启动才能生效，只执行 docker restart 是不起作用的。

(4) 修改公众号配置

因为默认是接微信的，不是接公众号的，需要修改额外的配置。

因为我是走nginx访问80/443转本地端口9002，然后9002映射容器端口80。

所以这里我指定了端口，然后因为捣鼓的时候没注意公众号相关配置，所以就多映射了一个config.json文件

nginx 的 wx.conf

```text
server {

    listen  80;
    server_name wx.zhangxiaocai.cn;
    rewrite ^(.*)$  https://$host$1 permanent;
}

server {
    listen       443 ssl;
    server_name  wx.zhangxiaocai.cn;

    ssl_certificate      /etc/nginx/ssl/wx.zhangxiaocai.cn_bundle.crt;
    ssl_certificate_key  /etc/nginx/ssl/wx.zhangxiaocai.cn.key;
    
    ssl_session_cache  shared:SSL:10m;
    ssl_session_timeout  10m;   
    ssl_protocols TLSv1.1 TLSv1.2;

    location / {
        proxy_pass http://localhost:9002 ;
	    client_max_body_size 500M;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   50x.html;
    }
}
```

docker-compose.yml 文件配置

```yaml
ersion: '2.0'
services:
  chatgpt-on-wechat:
    image: zhayujie/chatgpt-on-wechat
    container_name: chatgpt-on-wechat
    security_opt:
      - seccomp:unconfined
    volumes:
      - /opt/docker/config.json:/app/config.json
    ports:
      - 9002:80     
    environment:
      OPEN_AI_API_KEY: ''
      MODEL: ''
      PROXY: ''
      SINGLE_CHAT_PREFIX: '["", "阿蓝"]'
      SINGLE_CHAT_REPLY_PREFIX: '"[阿蓝] "'
      GROUP_CHAT_PREFIX: '["@bot"]'
      GROUP_NAME_WHITE_LIST: '["ChatGPT测试群", "ChatGPT测试群2"]'
      IMAGE_CREATE_PREFIX: '["画", "看", "找"]'
      CONVERSATION_MAX_TOKENS: 1000
      SPEECH_RECOGNITION: 'False'
      CHARACTER_DESC: '你是基于大语言模型的AI智能助手，旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。'
      EXPIRES_IN_SECONDS: 3600
      USE_GLOBAL_PLUGIN_CONFIG: 'True'
      USE_LINKAI: 'True'
      LINKAI_API_KEY: 'Link_****T9g'
      LINKAI_APP_CODE: 'iQ****G9'
```

映射的config.json

```text
{
  "channel_type": "wechatmp",
  "model": "",
  "open_ai_api_key": "YOUR API KEY",
  "claude_api_key": "YOUR API KEY",
  "text_to_image": "dall-e-2",
  "voice_to_text": "openai",
  "text_to_voice": "openai",
  "proxy": "",
  "hot_reload": false,
  "single_chat_prefix": [""],
  "single_chat_reply_prefix": [""],
  "group_chat_prefix": [
    "@bot"
  ],
  "group_name_white_list": [
    "ChatGPT测试群",
    "ChatGPT测试群2"
  ],
  "image_create_prefix": [
    "画"
  ],
  "speech_recognition": true,
  "group_speech_recognition": false,
  "voice_reply_voice": false,
  "conversation_max_tokens": 2500,
  "expires_in_seconds": 3600,
  "character_desc": "你是基于大语言模型的AI智能助手，旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。",
  "temperature": 0.7,
  "subscribe_msg": "感谢您的关注！\n这里是AI智能助手，可以自由对话。\n支持语音对话。\n支持图片输入。\n支持图片输出，画字开头的消息将按要求创作图片。\n支持tool、角色扮演和文字冒险等丰富的插件。\n输入{trigger_prefix}#help 查看详细指令。",
  "use_linkai": true,
  "linkai_api_key": "Link_*************ntEdT9g",
  "linkai_app_code": "iQ****G9",
  "wechatmp_app_id": "wx*****3cbe",
   "wechatmp_app_secret": "53***********9f7f",
   "wechatmp_aes_key": "dkA**************la9X5a",
   "wechatmp_token": "sr_token_",
   "wechatmp_port": 80
}
```

比较重要的配置是这几个

```text
{
  "channel_type": "wechatmp",    
  "single_chat_prefix": [""], 
  "wechatmp_app_id": "wx73f9******d1e48",     
  "wechatmp_app_secret": "weca******est",  
  "wechatmp_aes_key": "",
  "wechatmp_token": "tok**est",
  "wechatmp_port": 80,
}
```

重启就可以了。

公众号的配置服务器地址默认是 

- http://IP:80/wx
- https://IP:443/wx

我使用的域名，也有ssl证书。

## 微信

整体步骤跟上面一样。不需要配置公众号，然后启动成功之后需要微信扫描登录作为AI交流使用。

部署方式也多样化[Link.AI文档](https://docs.link-ai.tech/cow/quick-start)



