# Next.js 架构

## 目录与路由

> 参考：[项目结构](https://nextjs.org/docs/app/getting-started/project-structure)

这里只说明额外需要关注的点：

- [动态路由](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes#convention)：类似 `[id]` 这样结构
- 使用 `_` 下划线来命名私有路由（不参与到 nextjs 的路由中）：类似 `_lib`
- 使用 `()` 来命名路由组（不参与到 nextjs 的路由中）：类似 `(group1)`
- 使用 `@folder` 来创建平行路由（为同级的 layout.js 提供命名的内容槽）：例如 `@dashboard`、`@chat`
- 使用特殊括号语法创建拦截路由（模态框等覆盖内容）：如 `(.)feed/[id]` 拦截同级, `(..)profile` 拦截上级

## 图像与字体

> 参考：[图像与字体](https://nextjs.org/docs/app/getting-started/images-and-fonts)

### 图像

nextjs 的图像模块做了优化。

如果传入的图像是本地且静态导入的话，不需要提供宽度和高度，nextjs 自动会计算来适配。

如果传入的图像时远程图片的话，需要自行指定宽度和高度的适配，另外需要注意在 next.config.js 中配置第三方信任来源。

#### Next.js 图像底层做了哪些优化？

> 暂时先列在这里，需要后续深入了解学习

1. **自动优化** - 自动进行格式转换、大小调整和压缩
2. **延迟加载** - 默认实现懒加载，仅在进入视口时加载图像
3. **避免布局偏移** - 通过预留空间防止 CLS (Cumulative Layout Shift)
4. **响应式适配** - 可以根据不同设备提供不同尺寸的图像
5. **现代格式** - 自动提供 WebP 等现代格式的图像（如果浏览器支持）

### 字体

为了提高隐私性和性能，nextjs 会优化字体并自动删掉对于字体资源的请求。

## 数据请求

> 参考：[数据请求](https://nextjs.org/docs/app/getting-started/fetching-data)

首先，我们要知道 nextjs 中分为**服务端组件**和**客户端组件**，他们互相配合构成了性能更好体验更好的 nextjs 应用。

其次，关于数据请求不管在哪个部分的组件都可以进行数据获取，而如果需要做数据预取的逻辑则需要在**服务端组件**中处理数据请求的逻辑，然后通过 `props` 的方式传给**客户端组件**去使用。

### 流式渲染 (Streaming)

Next.js 默认支持流式渲染，允许服务器渐进式地发送UI到客户端。这使页面可以部分显示，而不必等待所有数据加载完成。

最佳实践：

- 使用 React Suspense 包裹数据密集型组件或次要内容
- 保持页面主要结构快速加载
- 为流式组件提供合理的加载状态

```jsx
import { Suspense } from 'react';

export default function Page() {
  return (
    <main>
      <h1>产品列表</h1>
      {/* 立即显示的UI */}
      <Suspense fallback={<p>加载产品中...</p>}>
        {/* 数据加载完成后流式传输 */}
        <ProductList /> {/* 这个组件会单独流式传输 */}
      </Suspense>

      {/* nextjs 默认会对所有内容进行流式渲染，另外还可以通过使用 Suspense 来做更加细粒度的流式传输 */}
      <Suspense fallback={<AnotherLoading />}>
        <AnotherSlowComponent /> {/* 这个也会单独流式传输 */}
      </Suspense>
    </main>
  );
}
```
