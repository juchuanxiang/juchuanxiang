# Swiper 作业三 · 完成记录

> 姓名：鞠传祥 · 课程：高级网页设计 · 日期：2026-11-10

---

## 任务一 · 提示词工程师（迭代 2 轮）

### 第 1 轮提示词（故意留缺口）

> 帮我写一个 Swiper 11 的初始化配置，挂载在 `#wySw4` 上，变量名 `jcx_sw4`。要求：① 无缝循环；② 每 3.2 秒自动翻页；③ 底部圆点分页器可以点击跳转，分页器元素是 `#wyPag4`；④ 初始效果为 cube 立方体效果。

### 第 1 轮 AI 回答

```js
const jcx_sw4 = new Swiper('#wySw4', {
  loop: true,                        // ① 无缝循环
  effect: 'cube',                    // ④ 初始效果 cube
  autoplay: {
    delay: 3200                      // ② 每 3.2 秒自动翻页
  },
  pagination: {
    el: '#wyPag4',                   // ③ 圆点分页器挂载元素
    clickable: true                  // ③ 圆点可点击跳转
  }
});
```

**验证结果**：刷新页面后轮播能循环、能自动翻页、圆点能点，**但用手拖动一次之后自动播放就停了**——不满足"手动操作后不停止"。
一句话结论：提示词漏掉了"手动操作后自动播放继续"这个约束，AI 就按默认值 `disableOnInteraction: true` 生成了。

### 第 2 轮提示词（补全约束）

> 帮我写一个 Swiper 11 的初始化配置，挂载在 `#wySw4` 上，变量名 `jcx_sw4`。要求：① 无缝循环；② 每 3.2 秒自动翻页，**并且用户手动拖动或点击之后，自动播放不能停，要继续**；③ 底部圆点分页器可以点击跳转，分页器元素是 `#wyPag4`；④ 初始效果为 cube 立方体效果。

### 第 2 轮 AI 回答

```js
const jcx_sw4 = new Swiper('#wySw4', {
  loop: true,                        // ① 无缝循环
  effect: 'cube',                    // ④ 初始效果 cube
  speed: 800,
  autoplay: {
    delay: 3200,                     // ② 每 3.2 秒自动翻页
    disableOnInteraction: false      // ② 手动操作后自动播放不停止
  },
  pagination: {
    el: '#wyPag4',                   // ③ 圆点分页器挂载元素
    clickable: true                  // ③ 圆点可点击跳转
  }
});
```

**验证结果**（截图见页面实际运行效果）：拖动一轮松手后，约 3.2 秒轮播继续自动翻页，cube 立方体翻转正常，圆点点击可跳转到指定张。
一句话结论：把"手动操作后不停止"翻译成 Swiper 的参数语言 `disableOnInteraction: false` 写进提示词后，AI 一次生成到位。

### 答辩抽问准备

| 可能的提问 | 我的回答 |
| --- | --- |
| 两轮提示词差在哪？ | 第 2 轮多了"手动操作后自动播放不停止"这一约束，对应参数 `disableOnInteraction: false` |
| 为什么不能只写"自动播放"？ | Swiper 默认用户一交互就停自动播放（`disableOnInteraction` 默认 true），不写清楚 AI 不会主动加 |
| 无缝循环是哪个参数？ | `loop: true`，Swiper 会克隆首尾 slide 实现无缝 |
| 圆点可点击的关键是什么？ | `pagination` 里既要 `el` 指定元素，又要 `clickable: true`，漏一个都点不了 |
| 提示词工程的心得体 | 把"人话需求"逐条翻译成组件的参数语言，约束写得越明确，AI 返工越少 |

---

## 任务二 · creative 电影墙仿写

参考官方示例 `swiper/demo/257-effect-creative.html`，为演示四新增第 7 个按钮「creative 电影墙」，核心配置（见 swiper-task.html 底部 `jcx_createSw4`）：

```js
creativeEffect: {
  limitProgress: 2,
  prev: {
    shadow: true,
    translate: ['-120%', 0, -300],
    rotate: [0, 0, -10],
    scale: 0.7,
    opacity: 0.6
  },
  next: {
    shadow: true,
    translate: ['120%', 0, 0],
    rotate: [0, 0, 10],
    scale: 1.15
  }
}
```

### 用自己的话解释 prev / next 的 transform 各控制什么

- **`prev` 控制的是"当前这张离开时变成什么样"**：翻页瞬间，正在展示的这张 slide 会朝着 prev 描述的状态过渡——`translate: ['-120%', 0, -300]` 让它向左移出屏幕并向后退（z 轴 -300 产生纵深），`rotate: [0, 0, -10]` 让它带一个逆时针 10 度的倾斜角，`scale: 0.7` 让它缩小到七成，`opacity: 0.6` 让它变淡。合起来就是"上一张缩小、左移、带角度地退场"。
- **`next` 控制的是"下一张进场前站在哪"**：还没轮到的 slide 停在 next 描述的位置上待命——`translate: ['120%', 0, 0]` 让它站在屏幕右侧之外，`scale: 1.15` 让它以放大 15% 的姿态准备，`rotate: [0, 0, 10]` 带一个顺时针 10 度角。翻页时它从这个状态渐变回正常大小和位置，看起来就是"下一张放大着从右侧进场"。
- 一句话总结：**prev 决定旧照片的退场姿势，next 决定新照片的入场起点**，中间的过渡动画由 Swiper 按 speed 自动补间。

---

## 任务三 · 综合创作

### ① 第六件展品：爱达·魔都号

- 图片：AI 生成（16:9 横版），保存在 `swiper-imgs/adora_1.jpg`
- 4 个演示台（#wySw1 ~ #wySw4）各新增一张结构完整的 slide（img + wy-cap 标题 + 简介）
- 展品文案：爱达·魔都号 —— 2024 年 1 月首航 · 首艘国产大型邮轮

### ② 课堂没讲过的 Swiper 能力：keyboard 键盘控制

加载在演示四 `#wySw4` 上（课堂讲了 loop / autoplay / pagination / navigation / effect，没讲键盘模块）：

```js
keyboard: {
  enabled: true,        // 开启键盘控制
  onlyInViewport: true  // 只在轮播进入可视区域时响应，避免抢全局按键
}
```

验证：点击演示四轮播区域后，按键盘 ← / → 方向键即可前后翻页，与自动播放、圆点、效果切换互不冲突。
