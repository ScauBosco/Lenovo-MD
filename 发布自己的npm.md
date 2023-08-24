## 如何发布更新自己的npm到[官网](\[www.npmjs.com/]\(https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2F\))

官网链接 [www.npmjs.com/](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2F)

注册npm账号、开发工具关联账号、生成npm一个校验码文本

### 前期准备

#### 注册npm账号、生成npm一个校验码文本

正常注册，完了应该会有一个生成校验码文本的步骤。如果没有的话在Account Settings这里找

![Account Settings.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5c45ec93703f46dd81551f82a87bda87~tplv-k3u1fbpfcp-watermark.image?)

![recovery codes.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0cd3e94f2f604682842dec20fcb90b18~tplv-k3u1fbpfcp-watermark.image?)

会生成一个有5条密码的文本文档，可以使用5次。

#### 开发工具关联账号以及npm包推送

```bash
// 预先把github项目拉下来或者把本地项目推到git上，进行关联
git remote add origin ......
// 项目文件目录下，根据提示填入，生成package.json。
//有一些比如test没有的话直接回车跳过
npm init
// 更新一下git，开始推上npm
git pull
// 查看当前npm镜像站，需要设置为npm源，非淘宝源，不然账号登录不上去
npm config get registry
npm config set registry https://registry.npmjs.org/
// 填入账号名，密码，邮箱，one-time password（来自之前生成的文档）
npm login

// npm包发布到https://registry.npmjs.org/，提示输入Enter OTP，
// 这个也是来自那个文档，不过要换一个密码，不能复用
npm publish

// 根据需求切换回淘宝源
npm config set registry http://registry.npm.taobao.org/

// 就可以继续用pnpm或者npm下载，pnpm要与原来的npm源一致。
// 我用的是pnpm，所以这里我得切换回来
```

### npm包更新

```bash
// git同步好了以后,调整版本号,以下任选其一即可
// patch：补丁号，修复bug，小变动，如 v1.0.0->v1.0.1
npm version patch

// minor：次版本号，增加新功能，如 v1.0.0->v1.1.0
npm version minor

// major：主版本号，不兼容的修改，如 v1.0.0->v2.0.0
npm version major

npm publish
```

### 淘宝源更新

因为淘宝镜像拉取npm有一定的时间间隔，没那么及时，可以手动同步

去淘宝的镜像网站<https://npmmirror.com/package/networks-assistant>

搜索你的包，并点击同步，等待即可

![taobao.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a9d3f158e7d4c9a8229611cb399ab67~tplv-k3u1fbpfcp-watermark.image?)

同步成功后，pnpm/npm 就能拉到最新的

### 结尾

简单的包发布就到这里，如果是ts，还需要写个index.d.ts文件。当中的Two-Factor Authentication原理没怎么了解。按上述方法对于第一次提交的够用了。后续可能还需要考虑打包导出成esm、commonjs不同模块的版本。
我的目录只有这几个文件

![networks-assistant.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a631306997684e8e8153f81be7c47a52~tplv-k3u1fbpfcp-watermark.image?)







