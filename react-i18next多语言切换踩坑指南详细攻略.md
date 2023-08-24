### 网站多语言技术探索与实现

#### 背景

> 网站需要切换多语言

#### 技术库

> i18next和[react-i18next](https://react.i18next.com/legacy-v9/step-by-step-guide#d-let-the-user-toggle-the-language)。i18next提供了所有的翻译功能，而react-i18next则添加了一些额外的React功能，如Hooks、HOCs、render props等
>

#### 使用

> 1. 安装`npm install i18next react-i18next --save`
>
> 2. 导入并初始化，i18n 为单例设计，只需初始化一次。
>
> 3. 配置中英文词汇文档`en.ts`以及`zh.ts`
>
> 4. 封装初始化i18n实例并导出
>
>    ```ts
>    import i18n from 'i18next';
>    import { initReactI18next } from 'react-i18next';
>    import enText from './en'
>    import zhText from './zh'
>    
>    const lng = 'en';
>    i18n
>      .use(initReactI18next) // passes i18n down to react-i18next
>      .init({
>        resources: {
>          en: {
>            translation: enText,
>          },
>          zh: {
>            translation: zhText,
>          },
>        },
>        lng: lng,
>        fallbackLng: lng,
>    
>        interpolation: {
>          escapeValue: false,
>        },
>      });
>      
>    export default i18n;
>    ```
>
> 5. 使用
>    ```tsx
>    import i18n from '@/services/i18n';
>    ...
>    {i18n.t('Welcome')}
>    ...
>    ```
>
> 6. 切换实现
>
>    > + 一开始以为调用`i18n.changeLanguage()`能直接触发页面渲染，修改无果。
>    > + 后尝试使用setState修改language，useEffect 监听language，触发回调函数再修改`i18n.changeLanguage()`，修改无果
>    > + 经查询资料发现可以通过reload页面，主动触发重新渲染，但语言变量状态维护没办法用useState或者Recoil全局状态管理，刷新就没了，于是决定简单粗暴地使用localStorage储存语言变量。以下为最终代码实现
>
>    ```ts
>    // 1.更改语言事件
>    onClick: () => changeLanguage('zh'),
>        
>    // 2.更改语言函数
>    const changeLanguage = (language: string) => {
>        localStorage.setItem('lang', language)
>        window.location.reload()
>      }
>    
>    // 3.这段代码放在入口文件中，达到渲染前初始化的目的
>    // 进入页面即切换语言，利用localStorage储存上次设置语言
>    if(!localStorage.getItem('lang')){
>    	localStorage.setItem('lang', 'en')
>    }
>    i18n.changeLanguage(localStorage.getItem('lang')!);
>    ```
>
> 7. 优化--正确使用方式
>
>    > 在'react-i18next'中有一个useTranslation钩子函数，用于挂载i18n实例到组件中，触发i18n.changeLanguage，组件也会重新渲染。
>    >
>    > 当中有两个坑或者要注意的点。
>    >
>    > 1. 从useTranslation导出的i18n中的changeLanguage不存在，得从自己封装的配置文件中拿到i18n实例。
>    > 2. 同样，从自己封装的配置文件中拿到i18n实例中有i18n.t方法，useTranslation中也有t方法。这里倒是两个都可以，但关键点在于，组件用的时候需要调用useTranslation方法才能把i18n实例挂载到组件，才能在changeLanguage以后触发重新渲染。既然如此就直接使用useTranslationdet，而无需导入自己封装的实例，因为这个方法会默认挂载实例。
>
>    ```tsx
>    import i18n from '@/services/i18n';
>    // 使用自己的封装的i18n触发更新
>    onClick: () => i18n.changeLanguage('en'),
>    // 在组件内部使用useTranslation钩子，把实例挂载到组件
>    const { t } = useTranslation()
>    // 直接使用
>     <p>{t('Welcome')}</p>
>    ```

#### 总结

> 遇到新技术不仅是通过技术博客复制代码看别人怎么用，遇到瓶颈时，既要想到自己的解决办法，确保功能实现。还要下功夫阅读该库的技术获取正确的用法，当然还得动手实践，技术文档也不是全部。



#### ant-design多语言补充

> 需要包一层`<ConfigProvider locale={getLocale}>`,去设定组件的多语言 



### umi4 多语言补充

+ 不能设useLocalStorage: false，默认为true，这样切换才能动态改变。
  ```ts
  locale: {
      // 默认使用 src/locales/en-US.ts 作为多语言文件
      default: 'en-US',
      baseSeparator: '-',
      // useLocalStorage: false,
      title: true,
      antd: true,
    },
  ```
  
  需要注意的是，当启用了 `locale` 配置项后，需要在项目的 `src/locales` 目录下创建对应语言的 JSON 文件，例如 `en-US.json`、`zh-CN.json` 等。在这些 JSON 文件中，可以定义该语言下各个界面的翻译文本。
  
+ 如何使用？为了迎合其他库使用t函数的使用习惯，封装钩子函数便利使用。
  ```ts
  import { useIntl } from 'umi';
  
  export function useMyIntl() {
    const intl = useIntl();
    const t = (id: string) => {
      return intl.formatMessage({
        id,
      });
    };
    return t;
  }
  ```

  使用
  ```ts
  // 以前使用的地方不用改，导入改成这个
  import { useTcsIntl } from '@/utils/useTcsIntl';
  
  `${t('Dashboard')}`
  ```

  
