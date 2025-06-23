// SysCogSecurity.vue
// 组件功能：系统设置页面的"安全设置"部分，包括认证、访问、上传等安全相关配置
<template>
    <!-- 安全设置主容器，loading 时显示加载动画 -->
    <div class="security-settings" v-loading="loading">
        <!-- 一级设置：认证管理 -->
        <div class="first-settings">
            <h3 class="first-title">认证管理</h3>
            <!-- 用户端认证设置 -->
            <h4 class="second-title">用户端认证</h4>
            <el-form 
                :model="authSettings.user" 
                :rules = "userPassRules"
                ref = "userPassForm"
                label-width="120px"
            >
                <el-form-item label="上传密码" prop="authCode">
                    <el-input v-model="authSettings.user.authCode" type="password" show-password @input="handleUserPassInput" autocomplete="new-password"/>
                </el-form-item>

                <el-form-item label="确认密码" prop="confirmNewUserPassword" v-if="showUserPassConfirm">
                    <el-input v-model="authSettings.user.confirmNewUserPassword" type="password" show-password autocomplete="new-password"/>
                </el-form-item>
            </el-form>
            <!-- 管理端认证设置 -->
            <h4 class="second-title">管理端认证</h4>
            <el-form 
                :model="authSettings.admin"
                :rules = "adminPassRules"
                ref = "adminPassForm"
                label-width="120px"
            >
                <el-form-item label="用户名" prop="adminUsername">
                    <el-input v-model="authSettings.admin.adminUsername" autocomplete="new-password"/>
                </el-form-item>
                <el-form-item label="密码" prop="adminPassword">
                    <el-input v-model="authSettings.admin.adminPassword" type="password" show-password @input="handleAdminPassInput" autocomplete="new-password"/>
                </el-form-item>

                <el-form-item label="确认密码" prop="confirmNewAdminPassword" v-if="showAdminPassConfirm">
                    <el-input v-model="authSettings.admin.confirmNewAdminPassword" type="password" show-password autocomplete="new-password"/>
                </el-form-item>
            </el-form>
        </div>

        <!-- 一级设置：上传管理 -->
        <div class="first-settings">
            <h3 class="first-title">上传管理</h3>
            <!-- 图像审查设置 -->
            <h4 class="second-title">图像审查
                <el-tooltip content="目前仅支持 moderatecontent.com 渠道" placement="top">
                    <font-awesome-icon icon="question-circle" style="margin-left: 5px; cursor: pointer;"/>
                </el-tooltip>
            </h4>
            <el-form :model="uploadSettings.moderate" label-width="120px">
                <el-form-item label="审查渠道">
                    <el-input v-model="uploadSettings.moderate.channel"/>
                </el-form-item>
                <el-form-item label="API Key">
                    <el-input v-model="uploadSettings.moderate.apiKey"/>
                </el-form-item>
            </el-form>
        </div>

        <!-- 一级设置：访问管理 -->
        <div class="first-settings">
            <h3 class="first-title">访问管理</h3>
            <!-- 域名过滤设置 -->
            <h4 class="second-title">域名过滤</h4>
            <el-form :model="accessSettings" label-width="120px">
                <el-form-item>
                    <template #label>
                        放行域名
                        <el-tooltip content="留空默认全部放行，多个域名请用英文逗号分隔" placement="top">
                            <font-awesome-icon icon="question-circle" style="margin-left: 5px; cursor: pointer;"/>
                        </el-tooltip>
                    </template>
                    <el-input v-model="accessSettings.allowedDomains"/>
                </el-form-item>
                <el-form-item>
                    <template #label>
                        白名单模式
                        <el-tooltip content="开启后，仅白名单文件可被访问" placement="top">
                            <font-awesome-icon icon="question-circle" style="margin-left: 5px; cursor: pointer;"/>
                        </el-tooltip>
                    </template>
                    <el-switch v-model="accessSettings.whiteListMode"/>
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
        authSettings: {
            user: {}, // 用户端认证设置
            admin: {} // 管理端认证设置
        },
        uploadSettings: {
            moderate: {} // 图像审查设置
        },
        accessSettings: {}, // 访问管理设置
        // 加载状态
        loading: false,

        // 修改密码相关
        oriUserPassword: '', // 原上传密码
        oriAdminPassword: '', // 原管理端密码

        showUserPassConfirm: false, // 显示用户密码确认框
        showAdminPassConfirm: false, // 显示管理密码确认框

        // 用户端密码校验规则
        userPassRules: {
            confirmNewUserPassword: [
                { message: '请再次输入上传密码', trigger: 'blur' },
                { validator: (rule, value, callback) => {
                    if (value !== this.authSettings.user.authCode) {
                        callback(new Error('两次输入密码不一致'));
                    } else {
                        callback();
                    }
                }, trigger: 'blur' }
            ]
        },

        // 管理端密码校验规则
        adminPassRules: {
            confirmNewAdminPassword: [
                { message: '请再次输入管理密码', trigger: 'blur' },
                { validator: (rule, value, callback) => {
                    if (value !== this.authSettings.admin.adminPassword) {
                        callback(new Error('两次输入密码不一致'));
                    } else {
                        callback();
                    }
                }, trigger: 'blur' }
            ]
        }
    };
},
computed: {
},
methods: {
    // 处理用户端密码输入，判断是否需要显示确认框
    handleUserPassInput() {
        if (this.authSettings.user.authCode !== this.oriUserPassword) {
            this.showUserPassConfirm = true;
        } else {
            this.showUserPassConfirm = false;
        }
    },
    // 处理管理端密码输入，判断是否需要显示确认框
    handleAdminPassInput() {
        if (this.authSettings.admin.adminPassword !== this.oriAdminPassword) {
            this.showAdminPassConfirm = true;
        } else {
            this.showAdminPassConfirm = false;
        }
    },
    // 保存所有安全设置到后端
    saveSettings() {
        // 所有表单的Promise数组
        let validationPromises = [];

        // 验证用户密码表单
        validationPromises.push(new Promise((resolve) => {
            this.$refs.userPassForm.validate((valid) => {
                resolve(valid);
            });
        }));

        // 验证管理密码表单
        validationPromises.push(new Promise((resolve) => {
            this.$refs.adminPassForm.validate((valid) => {
                resolve(valid);
            });
        }));

        // 等待所有验证完成
        Promise.all(validationPromises).then((results) => {
            const isValid = results.every(valid => valid);

            if (!isValid) {
                return;
            }

            const settings = {
                auth: this.authSettings,
                upload: this.uploadSettings,
                access: this.accessSettings
            };
            // 不保存确认密码相关字段
            delete settings.auth.user.confirmNewUserPassword;
            delete settings.auth.admin.confirmNewAdminPassword;

            fetchWithAuth('/api/manage/sysConfig/security', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(settings)
            }).then(() => {
                this.$message.success('设置已保存');
            });
        });
    }
},
mounted() {
    this.loading = true;
    // 获取当前安全设置
    fetchWithAuth('/api/manage/sysConfig/security')
    .then((response) => response.json())
    .then((data) => {
        this.authSettings = data.auth;
        this.uploadSettings = data.upload;
        this.accessSettings = data.access;

        // 保存原密码
        this.oriUserPassword = this.authSettings.user.authCode;
        this.oriAdminPassword = this.authSettings.admin.adminPassword;
        this.authSettings.user.confirmNewUserPassword = '';
        this.authSettings.admin.confirmNewAdminPassword = '';
    })
    .finally(() => {
        this.loading = false;
    });
}
};
</script>

<style scoped>
/* 安全设置主容器样式 */
.security-settings {
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