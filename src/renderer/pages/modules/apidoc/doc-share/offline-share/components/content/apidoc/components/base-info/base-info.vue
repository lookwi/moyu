/*
    创建者：shuxiaokai
    创建时间：2020-10-13 13:47
    模块名称：请求发送相关
    备注：xxxx
*/
<template>
    <div class="base-info">
        <!-- host信息 -->
        <el-radio-group v-model="host" size="mini" class="mb-2">
            <el-popover v-for="(item, index) in hostEnum" :key="index" :open-delay="600" :close-delay="0" placement="top-start" trigger="hover" :content="item.url" class="mr-2">
                <el-radio slot="reference" :label="item.url" border>{{ item.name }}</el-radio>
            </el-popover>
        </el-radio-group>
        <!-- 请求操作区域 -->
        <div class="d-flex w-100">
            <s-v-input
                    v-model="path"
                    placeholder="刷新可以恢复接口到最初状态"
                    size="small"
            >
                <div slot="prepend" class="request-input">{{ baseInfo.method.toUpperCase() }}</div>
            </s-v-input>
            <el-button
                v-if="!loading"
                :loading="loading"
                :disabled="!config.isElectron"
                :title="config.isElectron ? '' : '由于浏览器限制，非electron环境无法模拟发送请求'"
                type="success"
                size="small"
                @click="sendRequest">
                发送请求
            </el-button>
            <el-button v-if="loading" type="danger" size="small" @click="stopRequest">取消请求</el-button>
        </div>
        <!-- 请求参数展示 -->
        <pre class="w-100">{{ host }}{{ path }}</pre>
    </div>
</template>

<script>
import mixin from "@/pages/modules/apidoc/mixin" //公用数据和函数

export default {
    mixins: [mixin],
    computed: {
        currentSelectDoc() { //当前选中的doc
            return this.$store.state.apidoc.activeDoc[this.$route.query.id];
        },
        hostEnum: {
            get() {
                return this.$store.state.apidoc.hostEnum;
            },
            set(val) {
                this.$store.commit("apidoc/initAndChangeHostEnum", val);
            },
        },
        apidocInfo() {
            return this.$store.state.apidoc.apidocInfo;
        },
        baseInfo() {
            const { apidocInfo } = this.$store.state.apidoc;
            const result = {
                name: apidocInfo?.info?.name,
                description: apidocInfo?.info?.description,
                host: apidocInfo?.item?.url.host,
                path: apidocInfo?.item?.url.path,
                method: apidocInfo?.item?.method || "",
                contentType: apidocInfo?.item?.contentType,
                createdAt: apidocInfo.createdAt,
            };
            return result;
        },
        loading() { //发送请求loading效果
            return this.$store.state.apidoc.sendRequestLoading;
        },
    },
    data() {
        return {
            path: "",
            host: "",
            requestPath: "",
            loading2: false,
        };
    },
    watch: {
        baseInfo: {
            handler(baseInfo) {
                this.path = baseInfo.path;
                this.host = baseInfo.host;
            },
            immediate: true,
            deep: true,
        },
    },
    methods: {
        //===============================发送请求，保存请求，发布请求=======================//
        //发送请求
        sendRequest() {
            const paths = this.convertPlainParamsToTreeData(this.apidocInfo.item.paths);
            const queryParams = this.convertPlainParamsToTreeData(this.apidocInfo.item.queryParams);
            const requestBody = this.convertPlainParamsToTreeData(this.apidocInfo.item.requestBody);
            const headers = this.convertPlainParamsToTreeData(this.apidocInfo.item.headers);
            this.$store.dispatch("apidoc/sendRequest", {
                url: this.apidocInfo.item.url,
                method: this.apidocInfo.item.method,
                contentType: this.apidocInfo.item.contentType,
                paths,
                queryParams,
                requestBody,
                headers,
            }).then((res) => {
                console.log(res)
            }).catch((err) => {
                console.error(err);
            });
        },
        //取消发送
        stopRequest() {
            this.$store.dispatch("apidoc/stopRequest");
        },
        //=====================================其他操作====================================//
    },
};
</script>

<style lang="scss" scoped>
.base-info {
    padding: size(10) size(20);
    box-shadow: 0 3px 2px $gray-400;
    position: relative;
    z-index: 1;
    .request-input {
        display: flex;
        align-items: center;
        .el-select {
            width: 100px;
        }
    }
}
</style>
