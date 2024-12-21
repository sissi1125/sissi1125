# 一次从hash路由转成history路由的经历

前端应用从使用 Hash 路由切换到使用 History API 路由，并将 URL 结构从 https://xxx.xxx/index.html 变为 https://xxx.xxx/xxx
## 背景

技术栈：react-router-dom + vite

用的腾讯云COS
项目是作为一个桶下面的子目录存储部署的

![alt text](image.png)


## 项目内改造

确保前端框架使用的是 History 模式。
在 React Router 中，使用 BrowserRouter 代替 HashRouter。


本地运行后，url 地址将由 ‘#’变为了‘/’。且刷新回退一切正常，开发模式没有任何问题，但是如果将项目发布后放到站点服务器上，那么页面刷新后将无法正常显示。所以除了在开发阶段配置 history 模式外，在站点服务器上也要增加额外的配置。



## 配置改造
因为 History API 路由会导致浏览器直接请求服务器上的路径（如 /xxx），你需要确保服务器能处理这些请求并返回 index.html，以方便前端应用处理路由。

我用的是腾讯云COS，需要配置静态托管，COS 将会处理静态网站请求，并在找不到文件时返回 index.html，以便前端应用处理路由。

1. 登录腾讯云控制台：
    - 进入腾讯云官网并登录你的账户。
2. 进入 COS 控制台：
    - 在左侧导航栏中，选择“对象存储 COS”。
3. 选择存储桶：
    - 在存储桶列表中，选择你要配置的存储桶。
4. 配置静态网站：

    - 在存储桶详情页，找到“基础配置”或“静态网站”选项。
    - 启用静态网站托管。
    - 设置默认首页文档（通常为 index.html）。
    - 设置错误文档（也可以是 index.html），以确保所有未匹配的路径都返回 index.html。
5. 保存配置：
    - 确认并保存你的配置。


但是问题是： 
腾讯云 COS 的静态网站配置是以存储桶为单位的。每个存储桶可以单独配置其静态网站托管设置，包括默认首页和错误文档。 
但是如【背景】中提到的，我的项目是作为同一个桶下面的子目录存在的，那么修改这个桶相当于会影响到这个桶下面的其他子文件夹的项目。

不过，开启静态托管只是


静态网站托管通常会读取根目录下的 index.html。如果你想在访问 https://sit-console.xverse.cn/lbvr-console-cloud 时返回 lbvr-console-cloud 目录下的 index.html，你可以尝试以下方法：

使用 URL 重写规则：

如果你的 COS 支持 URL 重写规则，可以配置规则将 /lbvr-console-cloud 重定向到 /lbvr-console-cloud/index.html。
在 CDN 中配置：

如果你使用 CDN，可以在 CDN 中配置路径重写规则，将 /lbvr-console-cloud 重定向到 /lbvr-console-cloud/index.html。
确保目录权限正确：

确保 lbvr-console-cloud 目录下的文件权限设置为公共可读。

成功！
/lbvr-console-cloud*  重写成 /lbvr-console-cloud/index.html