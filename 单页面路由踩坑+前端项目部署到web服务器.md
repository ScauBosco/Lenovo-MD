### 单页面路由的问题

*问题描述：*

​	现在无论是react还是vue都是SPA单页面项目，换言之代码打包完以后就只有一个index.html。项目dev阶段在本地跑，配置好路由，功能正常。但是build阶段打包以后就只有一个index.html文件，导致的结果就是只有首页能正常范围，如果点击跳转history.push到某个子页面或许还有效。但是在浏览器输入配好路由的url访问就不行了。关键问题是，没有文件匹配你这个url。

*解决思路*

​	就是让所有的路由都匹配到首页index.html文件，换言之无论url输入什么路由都会返回index.html文件，具体路由渲染交给浏览器，这是spa应用自带的功能，我们不用管。这种操作说法有几种，重定向、rewrite都一个意思。

 	1. nginx
 	 `try_files $uri $uri/ /index.html;`
 	2. serve --single

*待实践方案*

​	开启hash路由



### 文件服务器常用有两种

1. nginx

2. node

   *以下为两种的使用*

### nginx配置安装

1. 直接[下载](https://nginx.org/en/download.html)稳定版本

2. 解压打开，cd到文件夹执行`start nginx`即可

3. 使用思路，react+umi3项目执行`npm run build`以后生成的dist文件夹

4. dist文件夹直接拉到nginx文件夹的html目录下。

5. nginx文件夹的在conf目录找到nginx文件，打开配置

   ```nginx
   // 在nginx.conf中找到server项，加入以下代码
   server {
       // 服务端口
       listen       8082;
       server_name  localhost;
       location / {         
           // dist文件夹地址
           root   D:\applicationginx-1.24.0\html\dist;
           index  index.html index.htm;
           // 单页面路由这一句是最关键的 所有的路由请求都返回首页
   		// react根据浏览器路径渲染相应的组件 我们不用管
   		// 不加这句，通过直接输入url访问就会找不到资源文件
           try_files $uri $uri/ /index.html;
       }
   }
   ```



### node文件服务器 `npm i -g serve`

*这是另一种启动web服务器的方式*

1. 同样是build出dist文件夹

2. cd进去

3. serve --single
   #### 这里注意--single很关键，不然也会无法通过直接输入url访问到相应组件

4. 或者不cd直接在dist外层文件夹`serve -s build`.*建议第一种方式*



### 路由补充 部署到服务器上路由会发生变化

+ 需要区分dev和prod环境，需要在umirc.ts或者config/config.ts中设置文件资源(umi.js和umi.css)路径(前缀).
  ```ts
  import { defineConfig } from '@umijs/max';
  
  const isProd = process.env.NODE_ENV === 'production';
  
  export default defineConfig({
    // 其他配置
    publicPath: isProd
      ? // 'http://localhost:3000/':'/',
        'https://example.com/xxx/'
      : '/',
  });
  ```

+ 对于路径匹配的问题，就需要做一个重定向
  ```ts
  export default defineConfig({
    routes: [
      {
        path: '/',
        redirect: '/xxx/home',
        exact: true,
      },
      {
        path: '/home',
        redirect: '/xxx/home',
        exact: true,
      },
      {
        path: '/xxx/home',
        exact: true,
        component: 'HomePage',
      }
    ],
  });
  ```

+ 对于子路由页面标题则可以手动设定。
  ```tsx
  // t为多语言函数
  useEffect(() => {
      document.title = t('home.title');
    }, [t]);
  ```

  