<template>
	<div class="system-edit-menu-container">
		<el-dialog :title="state.dialog.title" v-model="state.dialog.isShowDialog" width="769px"
			:close-on-click-modal="false">
			<el-form :model="state.ruleForm" :rules="rules" ref="ruleFormRef" size="default" label-width="80px">
				<el-row :gutter="35">
					<el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24" class="mb20">
						<el-form-item label="上级菜单">
							<el-cascader :options="state.menuData"
								:props="{ label: 'title', value: 'id', checkStrictly: true, emitPath: false }"
								placeholder="请选择上级菜单" clearable class="w100" v-model="state.ruleForm.pid">
								<template #default="{ node, data }">
									<span>{{ data.title }}</span>
									<span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
								</template>
							</el-cascader>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24" class="mb20">
						<el-form-item label="菜单类型" prop="menuType">
							<el-radio-group v-model="state.ruleForm.menuType">
								<el-radio label="0">目录</el-radio>
								<el-radio label="1">菜单</el-radio>
								<el-radio label="2">按钮</el-radio>
							</el-radio-group>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
						<el-form-item label="菜单名称" prop="menuName">
							<el-input v-model="state.ruleForm.menuName" placeholder="请填写菜单名称" clearable></el-input>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
						<el-form-item label="接口规则" prop="name">
							<el-input v-model="state.ruleForm.name" placeholder="后端 aip 地址" clearable></el-input>
						</el-form-item>
					</el-col>
					<template v-if="state.ruleForm.menuType === '0' || state.ruleForm.menuType === '1'">
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="路由路径" prop="path">
								<el-input v-model="state.ruleForm.path" placeholder="路由中的 path 值" clearable></el-input>
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="重定向">
								<el-input v-model="state.ruleForm.redirect" placeholder="请输入路由重定向" clearable></el-input>
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="菜单图标">
								<IconSelector placeholder="请输入菜单图标" v-model="state.ruleForm.icon" type="all" />
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="组件路径" prop="component">
								<el-input v-model="state.ruleForm.component" placeholder="组件路径" clearable></el-input>
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="链接地址">
								<el-input v-model="state.ruleForm.linkUrl" placeholder="外链/内嵌时链接地址（http:xxx.com）"
									clearable :disabled="state.ruleForm.isLink === 0">
								</el-input>
							</el-form-item>
						</el-col>
					</template>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
						<el-form-item label="权限标识">
							<el-select v-model="state.ruleForm.roles" multiple placeholder="选择角色" clearable
								class="w100">
								<el-option v-for="role in state.roles" :key="'role_' + role.id" :label="role.name"
									:value="role.id"></el-option>
							</el-select>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
						<el-form-item label="菜单排序">
							<el-input-number v-model="state.ruleForm.menuSort" controls-position="right"
								placeholder="请输入排序" class="w100" />
						</el-form-item>
					</el-col>
					<template v-if="state.ruleForm.menuType === '0' || state.ruleForm.menuType === '1'">
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="是否隐藏">
								<el-radio-group v-model="state.ruleForm.isHide">
									<el-radio v-for="dict in visibleOptions" :key="dict.value" :label="dict.value">{{
											dict.label
									}}</el-radio>
								</el-radio-group>
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="页面缓存">
								<el-radio-group v-model="state.ruleForm.isKeepAlive">
									<el-radio :label="1">缓存</el-radio>
									<el-radio :label="0">不缓存</el-radio>
								</el-radio-group>
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="是否固定">
								<el-radio-group v-model="state.ruleForm.isAffix">
									<el-radio :label="1">固定</el-radio>
									<el-radio :label="0">不固定</el-radio>
								</el-radio-group>
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="是否外链">
								<el-radio-group v-model="state.ruleForm.isLink"
									:disabled="state.ruleForm.isIframe === 1">
									<el-radio :label="1">是</el-radio>
									<el-radio :label="0">否</el-radio>
								</el-radio-group>
							</el-form-item>
						</el-col>
						<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" class="mb20">
							<el-form-item label="是否内嵌">
								<el-radio-group v-model="state.ruleForm.isIframe" @change="onSelectIframeChange">
									<el-radio :label="1">是</el-radio>
									<el-radio :label="0">否</el-radio>
								</el-radio-group>
							</el-form-item>
						</el-col>
					</template>
				</el-row>
			</el-form>
			<template #footer>
				<span class="dialog-footer">
					<el-button @click="onCancel" size="default">取 消</el-button>
					<el-button type="primary" @click="onSubmit(ruleFormRef)" size="default" :loading="state.loading">{{
							state.dialog.submitTxt
					}}</el-button>
				</span>
			</template>
		</el-dialog>
	</div>
</template>

<script setup lang="ts" name="systemAddMenu">
import type { SysDictDataMapItem } from '/@/api/system/dict/model';
import type { FormInstance, FormRules } from 'element-plus'
import { ElMessage } from 'element-plus';
import { defineAsyncComponent, ref, reactive, nextTick, PropType } from 'vue';
import { storeToRefs } from 'pinia';
import { useRoutesList } from '/@/stores/routesList';
import { getBackEndControlRoutes } from '/@/router/backEnd';
import { getMenuParams, getMenuInfo, addMenu ,updateMenu} from '/@/api/system/menu';
import { handleTree } from "/@/utils/gfast";

// 定义子组件向父组件传值/事件
const emit = defineEmits(['refresh']);

// 引入组件
const IconSelector = defineAsyncComponent(() => import('/@/components/iconSelector/index.vue'));

const props = defineProps({
	visibleOptions: {
		type: Object as PropType<SysDictDataMapItem[]>,
		default: () => [] as SysDictDataMapItem[]
	}
})

// 定义变量内容
const stores = useRoutesList();
const state = reactive({
	loading: false,
	roles: [] as Array<any>,
	// 参数请参考 `/src/router/route.ts` 中的 `dynamicRoutes` 路由菜单格式
	ruleForm: {
		id: undefined,
		pid: 0, // 上级菜单
		menuType: '0', // 菜单类型
		menuName: '', // 菜单名称
		name: '', // 接口规则
		component: '', // 组件路径
		isLink: 0, // 是否外链
		menuSort: 0, // 菜单排序
		path: '', // 路由路径
		redirect: '', // 路由重定向，有子集 children 时
		icon: '', // 菜单图标
		roles: [], // 权限标识，取角色管理
		isHide: '0', // 是否隐藏
		isKeepAlive: 1, // 是否缓存
		isAffix: 0, // 是否固定
		linkUrl: '', // 外链/内嵌时链接地址（http:xxx.com），开启外链条件，`1、isLink:true 2、链接地址不为空`
		isIframe: 0, // 是否内嵌，开启条件，`1、isIframe:true 2、链接地址不为空`
	},
	menuData: [] as RouteItems, // 上级菜单数据
	dialog: {
		isShowDialog: false,
		type: '',
		title: '',
		submitTxt: '',
	},
});

const ruleFormRef = ref<FormInstance>()
const rules = reactive<FormRules>({
	parentId: [{ required: true, message: '父菜单不能为空', trigger: 'blur' }],
	name: [{ required: true, message: '接口规则不能为空', trigger: 'blur' }],
	path: [{ required: true, message: '路由地址不能为空', trigger: 'blur' }],
	menuName: [{ required: true, message: '菜单名称不能为空', trigger: 'blur' }],
	menuType: [{ required: true, message: '菜单类型不能为空', trigger: 'blur' }],
})

// 打开弹窗
const openDialog = (type: string, row: any) => {
	initForm();
	nextTick(() => {
		//获取角色信息
		getMenuParams().then((res: any) => {
			state.roles = res.data.roles;
			const menu = { id: 0, title: '主类目', children: [] };
			menu.children = handleTree(res.data.menus, 'id', 'pid');
			state.menuData = new Array(menu) as any;
		});

		if (row) {
			if (type === 'edit') {
				state.dialog.title = '修改菜单';
				state.dialog.submitTxt = '修 改';
				getMenuInfo(row.id).then((res) => {
					const data = res.data.rule;
					state.ruleForm = {
						id: data.id,
						pid: data.pid, // 上级菜单
						menuType: '' + data.menuType, // 菜单类型
						menuName: data.title, // 菜单名称
						name: data.name, // 接口规则
						component: data.component, // 组件路径
						isLink: data.isLink, // 是否外链
						menuSort: data.weigh, // 菜单排序
						path: data.path, // 路由路径
						redirect: data.redirect, // 路由重定向，有子集 children 时
						icon: data.icon, // 菜单图标
						roles: res.data.roleIds, // 权限标识，取角色管理
						isHide: '' + data.isHide, // 是否隐藏
						isKeepAlive: data.isCached, // 是否缓存
						isAffix: data.isAffix, // 是否固定
						linkUrl: data.linkUrl, // 外链/内嵌时链接地址（http:xxx.com），开启外链条件，`1、isLink:true 2、链接地址不为空`
						isIframe: data.isIframe, // 是否内嵌，开启条件，`1、isIframe:true 2、链接地址不为空`
					};
				});
			} else if (type === 'add') {
				state.ruleForm.pid = row.id
				state.dialog.title = '修改菜单';
				state.dialog.submitTxt = '修 改';
			}
		}
		state.dialog.isShowDialog = true;
		state.dialog.type = type;
		state.loading = false;
	});
};
// 关闭弹窗
const closeDialog = () => {
	state.dialog.isShowDialog = false;
};
// 是否内嵌下拉改变
const onSelectIframeChange = () => {
	if (state.ruleForm.isIframe === 1) state.ruleForm.isLink = 1;
	else state.ruleForm.isLink = 0;
};
// 取消
const onCancel = () => {
	closeDialog();
};
// 新增
const onSubmit = async (formEl: FormInstance | undefined) => {
	if (!formEl) return
	await formEl.validate((valid, fields) => {
		if (!valid) {
			return
		}
		if (state.dialog.type === 'edit') {
			//修改
			updateMenu(state.ruleForm)
				.then(() => {
					ElMessage.success('菜单修改成功');
					closeDialog(); // 关闭弹窗
					resetMenuSession();
					emit('refresh');
				})
				.finally(() => {
					state.loading = false;
				});
		} else {
			//添加
			addMenu(state.ruleForm)
				.then(() => {
					ElMessage.success('菜单添加成功');
					closeDialog(); // 关闭弹窗
					resetMenuSession();
					emit('refresh');
				})
				.finally(() => {
					state.loading = false;
				});
		}
	})
};

// 重置菜单session
const resetMenuSession = () => {
	getBackEndControlRoutes();
};
const initForm = () => {
	state.ruleForm = {
		id: undefined,
		pid: 0, // 上级菜单
		menuType: '0', // 菜单类型
		menuName: '', // 菜单名称
		name: '', // 接口规则
		component: '', // 组件路径
		isLink: 0, // 是否外链
		menuSort: 0, // 菜单排序
		path: '', // 路由路径
		redirect: '', // 路由重定向，有子集 children 时
		icon: '', // 菜单图标
		roles: [], // 权限标识，取角色管理
		isHide: '0', // 是否隐藏
		isKeepAlive: 1, // 是否缓存
		isAffix: 0, // 是否固定
		linkUrl: '', // 外链/内嵌时链接地址（http:xxx.com），开启外链条件，`1、isLink:true 2、链接地址不为空`
		isIframe: 0, // 是否内嵌，开启条件，`1、isIframe:true 2、链接地址不为空`
	};
};

// 暴露变量
defineExpose({
	openDialog,
});
</script>
