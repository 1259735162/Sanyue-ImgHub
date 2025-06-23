// SysCogPage.vue
// 组件功能：系统设置页面的“页面设置”部分
<template>
    <!-- 页面设置主容器，loading 时显示加载动画 -->
    <div class="page-settings" v-loading="loading">
        <!-- 一级设置：页面设置 -->
        <div class="first-settings">
            <h3 class="first-title">页面设置</h3>
            <!-- 动态渲染所有页面设置项 -->
            <el-form :model="settings" label-width="120px">
                <el-form-item v-for="(setting, index) in settings.config" :key="index">
                    <template #label>
                        {{ setting.label }}
                        <!-- 如果有 tooltip，显示提示图标 -->
                        <el-tooltip v-if="setting.tooltip" :content="setting.tooltip" placement="top" raw-content>
                            <font-awesome-icon icon="question-circle" style="margin-left: 5px; cursor: pointer;"/>
                        </el-tooltip>
                    </template>
                    <el-input v-model="setting.value" :disabled="setting.fixed" :placeholder="setting.placeholder"></el-input>
                </el-form-item>
            </el-form>
        </div>

        <!-- 保存按钮 -->
        <div class="actions">
            <el-button type="primary" @click="saveSettings">保存设置</el-button>
        </div>
    </div>
</template>

<script>
import fetchWithAuth from '@/utils/fetchWithAuth';

export default {
data() {
    return {
        settings: {
            config: [] // 页面设置项数组
        },
        // 加载状态
        loading: false
    };
},
computed: {
},
methods: {
    // 保存设置到后端
    saveSettings() {
        fetchWithAuth('/api/manage/sysConfig/page', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(this.settings)
        })
        .then(() => this.$message.success('设置已保存'));
    }
},
mounted() {
    this.loading = true;
    // 获取当前页面设置
    fetchWithAuth('/api/manage/sysConfig/page')
    .then((response) => response.json())
    .then((data) => {
        this.settings = data;
    })
    .finally(() => {
        this.loading = false;
    });
}
};
</script>

<style scoped>
/* 页面设置主容器样式 */
.page-settings {
    padding: 20px;
    min-height: 500px;
}

.first-settings {
    margin-bottom: 40px;
}

.second-title {
    text-align: start;
    margin-left: 20px;
}

.actions {
    margin-top: 20px;
    text-align: right;
}
</style>