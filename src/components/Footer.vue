// Footer.vue
// 组件功能：全站底部版权信息展示
<template>
    <!-- 页脚容器，受 disableFooter 控制是否显示 -->
    <div class="page-footer" v-if="!disableFooter">
        <p>
            © 2024-{{ thisYear }} Designed by 
            <!-- 作者链接 -->
            <a class="footer-name" href="https://github.com/MarSeventh" target="_blank">SanyueQi</a> for You!
            <!-- 右侧外链图标，链接可自定义 -->
            <a :href="footerLink" target="_blank">
                <font-awesome-icon icon="paper-plane" class="footer-link-icon"/>
            </a>
        </p>
    </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
    name: 'Footer', // 组件名称
    computed: {
        ...mapGetters(['userConfig']), // 获取全局 userConfig 配置
        // 页脚右侧外链，优先用 userConfig.footerLink
        footerLink() {
            return this.userConfig?.footerLink || 'https://github.com/MarSeventh/CloudFlare-ImgBed'
        },
        // 当前年份
        thisYear() {
            return new Date().getFullYear()
        },
        // 是否禁用页脚
        disableFooter() {
            return this.userConfig?.disableFooter || false
        }
    }
}
</script>

<style scoped>
/* 页脚整体样式 */
.page-footer {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100vw;
    color: var(--page-footer-text-color);
    font-size: large;
    user-select: none;
}
@media (max-width: 768px) {
    .page-footer {
        font-size: small;
    }
}
/* 作者名样式 */
.footer-name {
    color: var(--page-footer-name-color);
    font-weight: bold;
    text-decoration: none;
}
/* 外链图标样式 */
.footer-link-icon {
    color: var(--page-footer-name-color);
    margin-left: 5px;
}
</style>