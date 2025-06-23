// SysConfigTabs.vue
// 组件功能：系统设置页面的左侧标签页导航
<template>
<div class="sidebar-container">
    <!-- 系统设置左侧菜单栏 -->
    <el-menu
    :default-active="activeIndex"
    class="el-menu-vertical"
    :collapse="isCollapse"
    @select="handleSelect"
    >
        <!-- 上传设置菜单项 -->
        <el-menu-item index="upload" class="menu-item">
            <font-awesome-icon icon="cloud-upload" style="width: 18px;"></font-awesome-icon>
            <span slot="title">上传设置</span>
        </el-menu-item>
        <!-- 安全设置菜单项 -->
        <el-menu-item index="security" class="menu-item">
            <font-awesome-icon icon="shield" style="width: 18px;"></font-awesome-icon>
            <span slot="title">安全设置</span>
        </el-menu-item>
        <!-- 网页设置菜单项 -->
        <el-menu-item index="page" class="menu-item">
            <font-awesome-icon icon="pager" style="width: 18px;"></font-awesome-icon>
            <span slot="title">网页设置</span>
        </el-menu-item>
        <!-- 其他设置菜单项 -->
        <el-menu-item index="others" class="menu-item">
            <font-awesome-icon icon="cog" style="width: 18px;"></font-awesome-icon>
            <span slot="title">其他设置</span>
        </el-menu-item>
    </el-menu>

    <!-- 折叠/展开按钮 -->
    <div class="toggle-button" @click="toggleCollapse">
        <font-awesome-icon :icon="isCollapse ? 'angle-double-right' : 'angle-double-left'"></font-awesome-icon>
    </div>
</div>
</template>

<script>
export default {
name: 'SysConfigTabs', // 组件名称
props: {
    activeIndex: {
        type: String,
        default: 'upload'
    },
    isCollapse: {
        type: Boolean,
        default: false
    }
},
data() {
    return {
        isCollapse: false // 是否折叠菜单
    };
},
methods: {
    // 切换菜单折叠/展开
    toggleCollapse() {
        this.isCollapse = !this.isCollapse;
        this.$emit('update:isCollapse', this.isCollapse);
    },
    // 检查是否为移动端，自动折叠菜单
    checkMobile() {
        const isMobile = window.innerWidth <= 768; // 假设移动端宽度小于等于768px
        this.isCollapse = isMobile;
        this.$emit('update:isCollapse', this.isCollapse);
    },
    // 处理菜单项选择
    handleSelect(index) {
        this.$emit('update:activeIndex', index);
    },
},
mounted() {
    this.checkMobile();
    window.addEventListener('resize', this.checkMobile);
},
beforeDestroy() {
    window.removeEventListener('resize', this.checkMobile);
}
};
</script>

<style scoped>
/* 侧边栏容器样式 */
.sidebar-container {
    display: flex;
    flex-direction: column;
    position: fixed;
    top: 30vh;
    left: 0;
    border-right: 1px dashed var(--admin-syscfg-tabs-border-color);
}

/* 菜单样式 */
.el-menu-vertical {
    background: none;
    border: none;
    width: 64px;
    min-height: 300px;
}
.el-menu-vertical:not(.el-menu--collapse) {
    width: 130px;
    min-height: 300px;
}

.menu-item {
    gap: 10px;
    border-radius: 0 20px 20px 0;
}

/* 折叠/展开按钮样式 */
.toggle-button {
    padding: 10px;
    text-align: center;
    cursor: pointer;
}

.toggle-button i {
    font-size: 20px;
}
</style>