/*
    创建者：shuxiaokai
    创建时间：2020-05-26 16:47
    模块名称：新增前端路由
    备注：xxxx
*/
<template>
    <s-dialog title="新增前端路由" :isShow="isShow" @close="handleClose">
        <s-form v-if="isShow" ref="form" showTip :formInfo="formInfo">
            <s-form-item label="名称" vModel="name" oneLine required></s-form-item>
            <s-form-item label="路径" vModel="path" oneLine required></s-form-item>
            <s-form-item label="分组名称" vModel="groupName" oneLine></s-form-item>
        </s-form>
        <div slot="footer">
            <el-button :loading="loading" size="mini" type="primary" @click="handleSubmit">确定</el-button>
            <el-button size="mini" type="warning" @click="handleClose">取消</el-button>
        </div>
    </s-dialog>
</template>

<script>
export default {
    props: {
        isShow: {
            type: Boolean,
            default: false,
        },
    },
    data() {
        return {
            formInfo: {},
            loading: false,
        };
    },
    created() {

    },
    methods: {
        //=====================================获取远程数据==================================//

        //=====================================前后端交互====================================//
        handleSubmit() {
            this.$refs.form.validate((valid) => {
                if (valid) {
                    const params = {
                        ...this.formInfo,
                    };
                    this.loading = true;
                    this.axios.post("/api/security/client_routes", params).then(() => {
                        this.$emit("success");
                        this.handleClose();
                    }).catch((err) => {
                        this.$errorThrow(err, this);
                    }).finally(() => {
                        this.loading = false;
                    });
                } else {
                    this.$nextTick(() => {
                        const input = document.querySelector(".el-form-item.is-error input");
                        if (input) {
                            input.focus();
                        }
                    });
                    this.loading = false;
                }
            });
        },
        //=====================================组件间交互====================================//
        handleClose() {
            this.$emit("update:isShow", false);
        },
        //=====================================其他操作=====================================//
    },
};
</script>

<style lang="scss">

</style>
