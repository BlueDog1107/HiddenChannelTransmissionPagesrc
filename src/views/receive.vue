<template>
	<div>
		<div class="container">
			<div class="handle-box">
				<el-select id="selectfiletype" v-model="query.filetype" placeholder="文件类型" class="handle-select mr10">
					<el-option key="1" label="txt" value="txt"></el-option>
					<el-option key="2" label="jpg" value="jpg"></el-option>
                    <el-option key="3" label="mp4" value="mp4"></el-option>
				</el-select>
				<el-input v-model="query.filename" placeholder="用户名" class="handle-input mr10"></el-input>
				<el-button type="primary" :icon="Search" @click="handleSearch">搜索</el-button>
				<el-button type="primary" :icon="Refresh" @click ="onReset">重置</el-button>
			</div>
			<el-table :data="tableData" border class="table" ref="multipleTable" header-cell-class-name="table-header">
				<el-table-column label="ID" width="55" align="center">
                    <template #default="scope">
                        <span>{{(query.pageIndex - 1)*query.pageSize + scope.$index+1}}</span>
                    </template>
                </el-table-column>
				<el-table-column prop="name" label="用户名"></el-table-column>
                <el-table-column prop="filename" label="文件名"></el-table-column>
                <el-table-column prop="filetype" label="文件类型"></el-table-column>
				<el-table-column label="状态" align="center">
					<template #default="scope">
						<el-tag
							:type="scope.row.state === '已下载' ? 'success' : scope.row.state === '未下载' ? 'danger' : ''"
						>
							{{ scope.row.state }}
						</el-tag>
					</template>
				</el-table-column>

				<el-table-column prop="date" label="发送时间"></el-table-column>
				<el-table-column label="操作" width="220" align="center">
					<template #default="scope">
						<el-button text :icon="Download" @click="handleEdit(scope.$index, scope.row)" v-permiss="15">
							下载
						</el-button>
						<el-button text :icon="Delete" class="red" @click="handleDelete(scope.$index)" v-permiss="16">
							删除
						</el-button>
					</template>
				</el-table-column>
			</el-table>
			<div class="pagination">
				<el-pagination
					background
					layout="total, prev, pager, next, sizes"
					:current-page="query.pageIndex"
					:page-size="query.pageSize"
                    :page-sizes="[10,20]"
					:total="pageTotal"
					@current-change="handlePageChange"
                    @size-change="handleSizeChange"
				></el-pagination>
			</div>
		</div>

		<!-- 编辑弹出框 -->
		<el-dialog :data="tableData" title="下载" v-model="editVisible" width="30%">
			<el-form label-width="100px">
				<el-form-item label="文件名:" >{{ form.filename }}</el-form-item>
				<el-form-item label="文件类型:">{{ form.filetype }}</el-form-item>
			</el-form>
			<template #footer>
				<span class="dialog-footer">
					<el-button @click="editVisible = false">取 消</el-button>
					<el-button type="primary" @click=downLoadfile>下 载</el-button>
				</span>
			</template>
		</el-dialog>
	</div>
</template>

<script setup lang="ts" name="basetable">
import { ref, reactive } from 'vue';
import { ElMessage, ElMessageBox } from 'element-plus';
import { Delete, Edit, Search, Plus, Download, Refresh } from '@element-plus/icons-vue';
import { fetchData } from '../api/index';
import { fi } from 'element-plus/es/locale';
import { ITEM_RENDER_EVT } from 'element-plus/es/components/virtual-list/src/defaults';
import { isEmpty } from 'lodash';

interface TableItem {
	name: string;
	filename: string;
    filetype: string;
	state: string;
	date: string;
}

const query = reactive({
	filetype: '',
	filename: '',
	pageIndex: 1,
	pageSize: 10
});
const tableData = ref<TableItem[]>([]);
const pageTotal = ref(0);
// 获取表格数据
const getData = () => {
	fetchData().then(res => {
		tableData.value = res.data.list.filter(
		(item:any, index:any) => index < query.pageIndex * query.pageSize && index >= query.pageSize * (query.pageIndex - 1)
	);
		pageTotal.value = res.data.list.length;
	});
};
getData();
//重置查询操作
const onReset = () => {
    query.filetype = '',
    query.filename = ''
};
// 查询操作
const handleSearch = () => {
	query.pageIndex = 1;
	getData();
};
// 分页导航
const handlePageChange = (val: number) => {
	query.pageIndex = val;
    getData();
};
const handleSizeChange = (val: number) => {
    query.pageSize = val;
}

// 删除操作
const handleDelete = (index: number) => {
	// 二次确认删除
	ElMessageBox.confirm('确定要删除吗？', '提示', {
		type: 'warning'
	})
		.then(async () => {
			ElMessage.success('删除成功');
			tableData.value.splice(index,1);
			pageTotal.value --;

		})
		.catch(() => {});
};
// 表格编辑时弹窗和保存
const editVisible = ref(false);
let form = reactive({
	filename: '',
	filetype: ''
});
let idx: number = -1;
const handleEdit = (index: number, row: any) => {
	idx = index;
	form.filename = row.filename;
	form.filetype = row.filetype;
	editVisible.value = true;
};
const downLoadfile = () => {
	editVisible.value = false;
	ElMessage.success(`下载成功`);
	tableData.value[idx].state = "已下载";
};
</script>

<style scoped>
.handle-box {
	margin-bottom: 20px;
}

.handle-select {
	width: 120px;
}

.handle-input {
	width: 300px;
}
.table {
	width: 100%;
	font-size: 14px;
}
.red {
	color: #F56C6C;
}
.mr10 {
	margin-right: 10px;
}
.table-td-thumb {
	display: block;
	margin: auto;
	width: 40px;
	height: 40px;
}
</style>
