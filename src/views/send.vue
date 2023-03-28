<template>
    <div class="container">
        <div class="form-box">
            <el-form ref="formRef" :rules="rules" :model="form" label-width="80px">
                <el-form-item label="文件名称" prop="name">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="接收地址" prop="ip">
                    <el-input v-model="form.ip"></el-input>
                </el-form-item>
                <el-form-item label="文件类型" prop="region">
                    <el-select v-model="form.region" placeholder="请选择">
                        <el-option key="txt" label="txt" value="txt"></el-option>
                        <el-option key="jpg" label="jpg" value="jpg"></el-option>
                        <el-option key="mp4" label="mp4" value="mp4"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="备注" prop="desc">
                    <el-input type="textarea" rows="5" v-model="form.desc"></el-input>
                </el-form-item>
                <el-form-item label="上传文件" prop="desc">
                    <el-upload
                        class="upload-demo"
                        drag
                        action="http://jsonplaceholder.typicode.com/api/posts/"
                        multiple
                        :on-change="handle"
                    >
                        <el-icon class="el-icon--upload"><upload-filled /></el-icon>
                        <div class="el-upload__text">
                            将文件拖到此处，或
                            <em>点击上传</em>
                        </div>
                    </el-upload>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit(formRef)">表单提交</el-button>
                    <el-button @click="onReset(formRef)">重置表单</el-button>
                </el-form-item>
            </el-form>
        </div>
    </div>
</template>

<script setup lang="ts" name="baseform">
import { reactive, ref } from 'vue';
import { ElMessage } from 'element-plus';
import type { FormInstance, FormRules } from 'element-plus';

const handle = (rawFile: any) => {
    console.log(rawFile);
};

const rules: FormRules = {
    name: [{ required: true, message: '请输入文件备注', trigger: 'blur' }],
    ip: [{ required: true, message: '请输入IPv4的格式', trigger: 'blur' }],
};
const formRef = ref<FormInstance>();
const form = reactive({
    name: '',
    ip:'',
    region: '',
    desc: '',
});
// 提交
const onSubmit = (formEl: FormInstance | undefined) => {
    // 表单校验
    if (!formEl) return;
    formEl.validate((valid) => {
        if (valid) {
            console.log(form);
            ElMessage.success('提交成功！');
        } else {
            return false;
        }
    });
};
// 重置
const onReset = (formEl: FormInstance | undefined) => {
    if (!formEl) return;
    formEl.resetFields();
};
</script>
<style scoped>

.upload-demo {
    width: 360px;
}
</style>