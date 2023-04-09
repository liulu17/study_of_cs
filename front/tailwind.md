1. @apply可以导出样式模版
```vue
<style>
  .btn {
    @apply py-2 px-4 font-semibold rounded-lg shadow-md;
  }
  .btn-green {
    @apply text-white bg-green-500 hover:bg-green-700;
  }
</style>
```
使用方式：
```vue
<template>
<button class="btn btn-green">按钮</button>
</template>
```

## 断点(响应式设计)
1. 移动优先，不要使用sm来定位移动设备
2. 自定义断点
```vue
module.exports = {
  theme: {
    screens: {
      'tablet': '640px',
      // => @media (min-width: 640px) { ... }

      'laptop': '1024px',
      // => @media (min-width: 1024px) { ... }

      'desktop': '1280px',
      // => @media (min-width: 1280px) { ... }
    },
  }
}
```

## 深色模式
1. 使用media启用
2. 在html标签中使用dark class启用

## preflight
表示tailwindcss默认的开箱即用的基础样式，一般像<a>这些标签都定义了初始样式

- 使用 @layer搭配@apply将更改默认的基础样式
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    @apply text-2xl;
  }
  h2 {
    @apply text-xl;
  }
}
```

2. 使用插件方式
```javascript
const plugin = require('tailwindcss/plugin')

module.exports = {
  plugins: [
    plugin(function({ addBase, theme }) {
      addBase({
        'h1': { fontSize: theme('fontSize.2xl') },
        'h2': { fontSize: theme('fontSize.xl') },
        'h3': { fontSize: theme('fontSize.lg') },
      })
    })
  ]
}
```

## 提取组件类
组件类是什么，就是用户自定义的组件中用的class定义。这些定义可能有复用的价值。
1. 使用模板提取
```vue
<!-- In use -->
<VacationCard
  img="/img/cancun.jpg"
  imgAlt="Beach in Cancun"
  eyebrow="Private Villa"
  title="Relaxing All-Inclusive Resort in Cancun"
  pricing="$299 USD per night"
  url="/vacations/cancun"
/>

<!-- ./components/VacationCard.vue -->
<template>
  <div>
    <img class="rounded" :src="img" :alt="imgAlt">
    <div class="mt-2">
      <div>
        <div class="text-xs text-gray-600 uppercase font-bold">{{ eyebrow }}</div>
        <div class="font-bold text-gray-700 leading-snug">
          <a :href="url" class="hover:underline">{{ title }}</a>
        </div>
        <div class="mt-2 text-sm text-gray-600">{{ pricing }}</div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: ['img', 'imgAlt', 'eyebrow', 'title', 'pricing', 'url']
  }
</script>
```

2. 使用@apply 抽取
```
<button class="btn-indigo">
  Click me
</button>

<style>
  .btn-indigo {
    @apply py-2 px-4 bg-indigo-500 text-white font-semibold rounded-lg shadow-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:ring-opacity-75;
  }
</style>
```

3. 使用@layer components提取
```css
@layer components {
  @variants responsive, hover {
    .btn-blue {
      @apply py-2 px-4 bg-blue-500 ...;
    }
  }
}
```
4. 在tailwind.config.css中使用插件
```js
// tailwind.config.js
const plugin = require('tailwindcss/plugin')

module.exports = {
  plugins: [
    plugin(function({ addComponents, theme }) {
      const buttons = {
        '.btn': {
          padding: `${theme('spacing.2')} ${theme('spacing.4')}`,
          borderRadius: theme('borderRadius.md'),
          fontWeight: theme('fontWeight.600'),
        },
        '.btn-indigo': {
          backgroundColor: theme('colors.indigo.500'),
          color: theme('colors.white'),
          '&:hover': {
            backgroundColor: theme('colors.indigo.600')
          },
        },
      }

      addComponents(buttons)
    })
  ]
}
```

## 新增功能类
> 使用css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
  .scroll-snap-none {
    scroll-snap-type: none;
  }
  .scroll-snap-x {
    scroll-snap-type: x;
  }
  .scroll-snap-y {
    scroll-snap-type: y;
  }
}
//响应式
@layer utilities {
  @variants responsive {
    .scroll-snap-none {
      scroll-snap-type: none;
    }
    .scroll-snap-x {
      scroll-snap-type: x;
    }
    .scroll-snap-y {
      scroll-snap-type: y;
    }
  }
}

深色
@layer utilities {
  @variants dark {
    .filter-none {
      filter: none;
    }
    .filter-grayscale {
      filter: grayscale(100%);
    }
  }
}

状态变体
@layer utilities {
  @variants hover, focus {
    .filter-none {
      filter: none;
    }
    .filter-grayscale {
      filter: grayscale(100%);
    }
  }
}
```

> 使用插件
```js
// tailwind.config.js
const plugin = require('tailwindcss/plugin')

module.exports = {
  plugins: [
    plugin(function({ addUtilities }) {
      const newUtilities = {
        '.filter-none': {
          filter: 'none',
        },
        '.filter-grayscale': {
          filter: 'grayscale(100%)',
        },
      }

      addUtilities(newUtilities, ['responsive', 'hover'])
    })
  ]
}
```

## 及时编译
### 新特性

1. 边框每个边的颜色可以指定
2. 透明度简写
3. 支持伪元素变体
4. 内容工具（在元素前后添加内容）
5. 全面的伪类支持
6. 插入式颜色工具(设置光标颜色)
7. 兄弟姐妹选择器变体
8. 简化的变换、滤波器和背景滤波器组合
9. 所有变体都支持了，不需修改配置文件
10. 变体可以组合一起使用，形成栈结构
    
### 改变
1. 使用栈式组合变种而不是覆盖变种
2. 变种是注入到@tailwind variants（从@tailwind screens重命名来）指令中的，而不是@tailwind utilities指令
3. 不用显示写出transform, filter, 和 backdrop-filter