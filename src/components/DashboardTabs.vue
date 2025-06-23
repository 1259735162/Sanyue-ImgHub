// DashboardTabs.vue
// 组件功能：管理端顶部导航标签栏和下拉菜单，支持页面切换和主题切换
<template>
    <!-- 顶部导航栏容器 -->
    <div class="tabs">
        <!-- 当前页面标题，点击可刷新页面 -->
        <span class="title" @click="refreshDashboard">
            <font-awesome-icon :icon="iconName" class="fa-images"></font-awesome-icon>
            {{ titleName }}
        </span>
        <!-- 下拉菜单，切换不同管理页面 -->
        <el-dropdown 
            @command="handleTabClick" 
            class="tabs-dropdown" 
            role="navigation" 
            @visible-change="handleDropdownVisible" 
            popper-class="tabs-dropdown-popper"
        >
            <span class="tabs-dropdown-link">
                <font-awesome-icon icon="bars" class="tabs-arrow"></font-awesome-icon>
            </span>
            <template #dropdown>
                <el-dropdown-menu>
                    <!-- 各个页面的菜单项，当前页面不显示 -->
                    <el-dropdown-item command="dashboard" v-if="activeTab !== 'dashboard'">
                        <font-awesome-icon icon="images" style="margin-right: 5px;"></font-awesome-icon>
                        图片管理
                    </el-dropdown-item>
                    <el-dropdown-item command="customerConfig" v-if="activeTab !== 'customerConfig'">
                        <font-awesome-icon icon="user-cog" style="margin-right: 5px;"></font-awesome-icon>
                        用户管理
                    </el-dropdown-item>
                    <el-dropdown-item command="systemConfig" v-if="activeTab !== 'systemConfig'">
                        <font-awesome-icon icon="cogs" style="margin-right: 5px;"></font-awesome-icon>
                        系统设置
                    </el-dropdown-item>
                    <el-dropdown-item command="">
                        <font-awesome-icon icon="upload" style="margin-right: 5px;"></font-awesome-icon>
                        上传页面
                    </el-dropdown-item>
                </el-dropdown-menu>
            </template>
        </el-dropdown>
        <!-- 主题切换按钮 -->
        <AdminToggleDark/>
    </div>
</template>

<script>
import AdminToggleDark from './AdminToggleDark.vue';

export default {
    name: 'DashboardTabs', // 组件名称
    props: {
        // 当前激活的标签页
        activeTab: {
            type: String,
            default: 'dashboard'
        }
    },
    components: {
        AdminToggleDark // 主题切换按钮组件
    },
    computed: {
        // 根据当前标签页返回标题
        titleName() {
            if (this.activeTab === 'dashboard') {
                return '图片管理';
            } else if (this.activeTab === 'customerConfig') {
                return '用户管理';
            } else if (this.activeTab === 'systemConfig') {
                return '系统设置';
            } else {
                return '上传页面';
            }
        },
        // 根据当前标签页返回图标
        iconName() {
            if (this.activeTab === 'dashboard') {
                return 'images';
            } else if (this.activeTab === 'customerConfig') {
                return 'user-cog';
            } else if (this.activeTab === 'systemConfig') {
                return 'cogs';
            } else {
                return 'upload';
            }
        }
    },
    methods: {
        // 刷新页面
        refreshDashboard() {
            location.reload();
        },
        // 处理下拉菜单点击，切换路由
        handleTabClick(tab) {
            this.$router.push(`/${tab}`);
        },
        // 下拉菜单展开/收起时，切换箭头样式
        handleDropdownVisible(isVisible) {
            const arrow = document.querySelector('.tabs-dropdown-link');
            if (isVisible) {
                arrow.classList.add('rotate-up'); // 添加旋转类
            } else {
                arrow.classList.remove('rotate-up'); // 移除旋转类
            }
        }
    }
}
</script>

<style scoped>
/* 页面标题样式 */
.title {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 1.5em;
    font-weight: bold;
    cursor: pointer;
    transition: color 0.3s ease;
    color: var(--admin-container-color);
}

.title:hover {
    color: var(--admin-purple) /* 使用柔和的淡紫色 */
}

/* 顶部导航栏整体布局 */
.tabs {
    display: flex;
    justify-content: center;
    gap: 12px;
}
.tabs-dropdown {
    display: flex;
    align-items: center;
}
/* 下拉菜单按钮样式 */
.tabs-dropdown-link {
    cursor: pointer;
    font-size: 1.5em;
    transition: all 0.3s ease;
    color: var(--admin-container-color);
}

.tabs-dropdown-link.rotate-up {
    color: var(--admin-purple); /* 使用柔和的淡紫色 */
}
/* el-dropdown有关的样式在index.html中定义 */
</style>

<style>
/* 下拉菜单弹窗样式 */
.el-dropdown__popper.el-popper.tabs-dropdown-popper {
    border-radius: 12px;
    border: none;
    background-color: var(--tabs-dropdown-popper-bg-color);
    backdrop-filter: blur(10px);
    box-shadow: var(--tabs-dropdown-popper-shadow);
}
.el-dropdown__popper.el-popper.tabs-dropdown-popper .el-dropdown-menu {
    border: none;
    background: none;
}
.el-dropdown__popper.el-popper.tabs-dropdown-popper .el-dropdown-menu__item {
    border: none;
    background: none;
    font-size: 16px;
    font-weight: bold;
    transition: font-size 0.3s ease;
}
.el-dropdown__popper.el-popper.tabs-dropdown-popper .el-dropdown-menu__item:hover {
    font-size: 18px;
}
</style>