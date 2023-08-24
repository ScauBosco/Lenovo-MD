### umi4中使用recoil简单方法不走弯路

#### 放下执念，伪入口也是入口

很多同学执着于找umi4的入口文件，企图在最最最外层包上`<RecoilRoot>`，在注入`{children}`或者`<Outlet />`时发现报注入失败的错。然后怀疑到底是入口文件没找对还是注入的方式有问题。转而寻找类似于umi-plugin-recoil之类的插件发现不支持umi4，咬咬牙翻看umi4手册注册插件的api打算手鲁一个。其实一开始就是包在Layout布局文件中，感觉是能用的，但“我是一个特别固执的人，如果你们也和我一样，那就真的是”-- 太逊啦！

```tsx
**/layout/index.tsx**
import { Outlet } from '@umijs/max';
import { RecoilRoot } from 'recoil';
import './index.less';
import MyLayout from './layout';

export default function Index() {
  return (
    <RecoilRoot>
      <MyLayout>
        <Outlet />
      </MyLayout>
    </RecoilRoot>
  );
}
```

这里有个细节点要注意，如果想直接在layout中就用到，最好把layout抽出来，才能在被<RecoilRoot>包裹的上下文中使用。不然即使在return的时候返回形如<RecoilRoot><Layout /></RecoilRoot>也不行。

#### 总结

并不是反对从根源解决问题，以上确实不是治标治本的方案。但还是要分情况，实用主义，有问题再说。毕竟连umi4都是别人的东西，在这种集成脚手架半黑盒子上，对着别人的文档钻研技术感觉没太必要，当然也是现阶段能力有限。坐等大佬的解决方案，主打一个拿来主义。by the way，因为chatGPT没有umi4年代的数据，回答的真的很辣鸡，浪费时间。

