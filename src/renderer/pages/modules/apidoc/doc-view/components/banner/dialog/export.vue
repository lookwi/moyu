/*
    创建者：shuxiaokai
    创建时间：2020-11-4 19:07
    模块名称：导出文档
    备注：xxxx
*/
<template>
    <s-dialog title="导出文档" :isShow="visible" width="40%" @close="handleClose">
        <div>
            <s-download-button url="/api/project/doc_word" :params="{ projectId: $route.query.id }">导出为Word</s-download-button>
            <s-download-button url="/api/project/doc_offline_data" :params="{ projectId: $route.query.id }">导出为HTML</s-download-button>
            <s-download-button url="/api/project/export/moyu" :params="{ projectId: $route.query.id }">导出为摸鱼文档</s-download-button>
        </div>
        <div slot="footer">
            <el-button size="mini" type="warning" @click="handleClose">关闭</el-button>
        </div>
    </s-dialog>
</template>

<script>
export default {
    props: {
        visible: { //是否现实弹窗
            type: Boolean,
            default: false,
        },
    },
    data() {
        return {
            formInfo: {
                name: "", //------文件名称
            },
            //=====================================其他参数====================================//
            loading: false, //----确认按钮状态
        };
    },
    mounted() {},
    methods: {
        handleExport() {
            this.loading = true;
            this.axios.get("/api/project/doc_word", { params: { projectId: this.$route.query.id } }).then(() => {}).catch((err) => {
                console.error(err);
            }).finally(() => {
                this.loading = false;
            });
        },
        //=====================================其他操作=====================================//
        //关闭弹窗
        handleClose() {
            this.$emit("update:visible", false);
            this.$emit("close");
        },
    },
};
</script>

<style lang="scss">

</style>
