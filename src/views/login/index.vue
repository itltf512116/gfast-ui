<template>
	<div class="login-container">
		<div class="login-content-out">
			<div class="login-content">
				<div class="login-content-main">
					<div class="login-icon-group">
						<div class="login-icon-group-title">
							<img :src="logoMini" />
							<div class="login-icon-group-title-text font25">{{ getThemeConfig.globalViceTitle }}</div>
						</div>
					</div>
					<div v-if="!state.isScan">
						<el-tabs v-model="state.tabsActiveName">
							<el-tab-pane :label="$t('message.label.one1')" name="account">
								<Account />
							</el-tab-pane>
							<el-tab-pane :label="$t('message.label.two2')" name="mobile">
								<Mobile />
							</el-tab-pane>
						</el-tabs>
					</div>
					<Scan v-if="state.isScan" />
					<div class="login-content-main-sacn" @click="state.isScan = !state.isScan">
						<i class="iconfont" :class="state.isScan ? 'icon-diannao1' : 'icon-barcode-qr'"></i>
						<div class="login-content-main-sacn-delta"></div>
					</div>
				</div>
			</div>
		</div>
		<div class="login-footer">
			<div class="login-footer-content mt15">
				<div class="login-footer-content-warp">
					<div>Copyright © 2021-2023 g-fast.cn All Rights Reserved.</div>
					<div class="mt5">云南奇讯科技有限公司版权所有</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script setup lang="ts" name="loginIndex">
import { defineAsyncComponent, onMounted, reactive, computed } from 'vue';
import { storeToRefs } from 'pinia';
import { useThemeConfig } from '/@/stores/themeConfig';
import { NextLoading } from '/@/utils/loading';
import logoMini from '/@/assets/logo-mini.svg';

// 引入组件
const Account = defineAsyncComponent(() => import('/@/views/login/component/account.vue'));
const Mobile = defineAsyncComponent(() => import('/@/views/login/component/mobile.vue'));
const Scan = defineAsyncComponent(() => import('/@/views/login/component/scan.vue'));

// 定义变量内容
const storesThemeConfig = useThemeConfig();
const { themeConfig } = storeToRefs(storesThemeConfig);
const state = reactive({
	tabsActiveName: 'account',
	isScan: false,
});

// 获取布局配置信息
const getThemeConfig = computed(() => {
	return themeConfig.value;
});
// 页面加载时
onMounted(() => {
	NextLoading.done();
});
</script>

<style scoped lang="scss">
.login-container {
	width: 100%;
	height: 100%;
	position: relative;
  background-image: url("/@/assets/bg.jpg");
  background-size: cover;
	.login-icon-group {
		width: 100%;
		height: 100%;
		position: relative;
		.login-icon-group-title {
			display: flex;
			align-items: center;
			img {
				width: 80px;
				height: 70px;
			}
			&-text {
				padding-left: 5px;
				color: var(--el-color-primary);
			}
		}
		&-icon {
			width: 60%;
			height: 70%;
			position: absolute;
			left: 0;
			bottom: 0;
		}
	}
  .login-content-out {
    width: 100%;
    height: 100%;
    padding-top: 150px;
  }
	.login-content {
		width: 500px;
		padding: 20px;
    margin: auto;
		background-color: var(--el-color-white);
		border: 5px solid var(--el-color-primary-light-8);
		border-radius: 5px;
		overflow: hidden;
		z-index: 1;
    position: relative;
		.login-content-main {
			margin: 0 auto;
			width: 80%;
			.login-content-title {
				color: var(--el-text-color-primary);
				font-weight: 500;
				font-size: 22px;
				text-align: center;
				letter-spacing: 4px;
				margin: 15px 0 30px;
				white-space: nowrap;
				z-index: 5;
				position: relative;
				transition: all 0.3s ease;
			}
		}
		.login-content-main-sacn {
			position: absolute;
			top: 0;
			right: 0;
			width: 50px;
			height: 50px;
			overflow: hidden;
			cursor: pointer;
			transition: all ease 0.3s;
			color: var(--el-text-color-primary);
			&-delta {
				position: absolute;
				width: 35px;
				height: 70px;
				z-index: 2;
				top: 2px;
				right: 21px;
				background: var(--el-color-white);
				transform: rotate(-45deg);
			}
			&:hover {
				opacity: 1;
				transition: all ease 0.3s;
				color: var(--el-color-primary) !important;
			}
			i {
				width: 47px;
				height: 50px;
				display: inline-block;
				font-size: 48px;
				position: absolute;
				right: 2px;
				top: -1px;
			}
		}
	}
  .login-footer{
    position: absolute;
    bottom: 5px;
    width: 100%;
    &-content {
      width: 100%;
      display: flex;
      &-warp {
        margin: auto;
        color: #e0e3e9;
        text-align: center;
        animation: error-num 1s ease-in-out;
      }
    }
  }
}
</style>
