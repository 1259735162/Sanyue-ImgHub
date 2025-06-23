// AdminToggleDark.vue
// 组件功能：管理端的暗黑/明亮模式切换按钮
<template>
    <!-- 暗黑/明亮模式切换按钮的容器 -->
    <div
      id="themeToggle"
      @click="handleToggleClick"
    >
      <!-- SVG 图标，根据 isDark 切换样式 -->
      <svg
        class="theme_toggle_svg"
        :class="{ 'dark': isDark }"
        width="1.3em"
        height="1.3em"
        viewBox="0 0 24 24"
        fill="none"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke="currentColor"
      >
        <!-- 遮罩，用于切换月亮/太阳效果 -->
        <mask id="themeMask">
          <rect x="0" y="0" width="100%" height="100%" fill="white"></rect>
          <circle
            class="theme_toggle_circle1"
            fill="black"
            :cx="isDark ? '50%' : '100%'"
            :cy="isDark ? '23%' : '0%'"
            :r="isDark ? '9' : '5'"
          ></circle>
        </mask>
        <!-- 主体圆形（月亮/太阳） -->
        <circle
          class="theme_toggle_circle2"
          cx="12"
          cy="12"
          :r="isDark ? '9' : '5'"
          mask="url(#themeMask)"
        ></circle>
        <!-- 太阳光芒，仅在明亮模式下显示 -->
        <g class="theme_toggle_g" stroke="currentColor" :opacity="isDark ? 0 : 1">
          <line x1="12" y1="1" x2="12" y2="3"></line>
          <line x1="12" y1="21" x2="12" y2="23"></line>
          <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
          <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
          <line x1="1" y1="12" x2="3" y2="12"></line>
          <line x1="21" y1="12" x2="23" y2="12"></line>
          <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
          <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
        </g>
      </svg>
    </div>
</template>
  
<script>
export default {
  name: 'ToggleDark', // 组件名称
  data() {
    return {
      // 是否为暗黑模式，取自全局 store
      isDark: this.$store.getters.useDarkMode,
    };
  },
  methods: {
    // 点击切换按钮时触发，切换暗黑/明亮模式
    handleToggleClick() {
      this.isDark = !this.isDark;
      // 更新全局 store 的暗黑模式状态
      this.$store.commit('setUseDarkMode', this.isDark);
      // 标记用户自定义切换
      this.$store.commit('setCusDarkMode', true);
    },
  }
};
</script>

<style scoped>
/* 按钮容器样式 */
#themeToggle {
  border: 0;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 2rem;
  height: 2rem;
  border-radius: 0.375rem;
  transition-property: background-color, border-color, color;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 150ms;
}

/* 遮罩圆动画 */
.theme_toggle_circle1 {
  transition: cx 0.5s ease-in-out, cy 0.5s ease-in-out, r 0.5s ease-in-out;
}

/* 主体圆动画和颜色 */
.theme_toggle_circle2 {
  transition: all 0.5s ease-in-out;
  fill: var(--admin-theme-toggle-bg-color);
}

/* SVG 图标旋转动画 */
.theme_toggle_svg {
  transition: transform 0.5s cubic-bezier(0.68, -0.55, 0.27, 1.55);
  transform: rotate(90deg);
  color: var(--admin-theme-toggle-color);
}

/* 暗黑模式下 SVG 旋转角度不同 */
.dark.theme_toggle_svg {
  transform: rotate(40deg);
}

/* 太阳光芒渐变动画 */
.theme_toggle_g {
  transition: opacity 0.5s ease-in-out;
}
</style>