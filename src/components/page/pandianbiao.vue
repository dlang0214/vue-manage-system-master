<template>
    <div  v-loading.body="loading">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i>管理员管理</el-breadcrumb-item>
                <el-breadcrumb-item>资产表管理</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <el-tabs type="border-card" style="overflow: scroll">
            <el-tab-pane>
                <span slot="label"><i class="el-icon-date"></i>数据清空</span>
                <div>注：每月应清空上月的数据，最好在月初进行</div>
                <div style="margin-top: 20px">
                    <el-button type="primary" @click="clear('PDA_INV_ITEM')">清空erp资产表</el-button>
                    <el-button type="primary" @click="clear('PDA_INV_ITEM_ADD')">清空列管资产表</el-button>
                    <el-button type="primary" @click="clear('PDA_INV_ITEM_ADDENDUM')">清空副表</el-button>
                    <el-button type="primary" @click="clear('PDA_ASSET')">清空资产信息表</el-button>
                </div>
            </el-tab-pane>
            <el-tab-pane label="数据导入">
                <div>注：在每月盘点前应导入新数据，否则盘点的信心都为新增资产</div>
                <div style="margin-top: 20px">
                    <el-button @click="tongbu" type="primary">同步erp数据</el-button>
                </div>
                <div style="margin-top: 20px">
                    <div>资产信息导入模板</div>
                    <el-table
                    :data="tableData"
                    stripe
                    style="width: 100%">
                    <el-table-column
                        prop="tagNumber"
                        label="资产编号"
                    >
                    </el-table-column>
                    <el-table-column
                        prop="description"
                        label="资产说明"
                    >
                    </el-table-column>
                    <el-table-column
                        prop="modelNumber"
                        label="规格型号"
                    >
                    </el-table-column>
                    <el-table-column
                        prop="cmaNumber"
                        label="仪校编码"
                    >
                    </el-table-column>
                    <el-table-column
                        prop="serialNumber"
                        label="序列号/MAC">
                    </el-table-column>
                    <el-table-column
                        prop="localtion"
                        label="存放地点">
                    </el-table-column>
                    <el-table-column
                        prop="fullName"
                        label="使用人">
                    </el-table-column>
                    <el-table-column
                        prop="BDateString"
                        label="资产购入时间">
                    </el-table-column>
                    <el-table-column
                        prop="userNo"
                        label="录入人员工号">
                    </el-table-column>
                    <el-table-column
                        prop="desc"
                        label="使用部门">
                    </el-table-column>
                </el-table>
                </div>

                <div class="errorMess" v-show="errorMess" >
                    批量导入错误信息：<br>
                    <div v-for="i in errorlist">{{i}}</div>
                </div>
                <div style="margin-top: 10px">
                    <input type="file" id="file1" @change="getFile" style="border: solid 1px #eee"/>
                </div>

                <div style="margin-top: 20px">
                    <el-button  type="primary" @click="postInsert('PDA_INV_ITEM_ADD')">导入列管资产</el-button>
                    <el-button  type="primary" @click="postInsert('PDA_INV_ITEM_ADDENDUM')">导入副表</el-button>
                </div>
            </el-tab-pane>
            <el-tab-pane label="数据操作">
                <shuju></shuju>
            </el-tab-pane>

        </el-tabs>


    </div>
</template>

<script>
    import shuju from "../pageItme/shujucaozuo.vue";
    import ElInput from "../../../node_modules/element-ui/packages/input/src/input.vue";

    export default {
        components: {
            ElInput,
            shuju},
        data: function(){
            return {
                loading:false,
                tableData:[
                    {
                    tagNumber:"cs123",
                    description:"测试数据",
                    modelNumber:"",
                    cmaNumber:"",
                    serialNumber:"",
                    localtion:"石龙仔三楼机房",
                    fullName:"",
                    descCondition:"",
                    userNo:"170711124",
                    BDateString:"",
                    desc:"信息中心",
                    },
                    {
                        tagNumber:"cs124",
                        description:"测试数据",
                        modelNumber:"",
                        cmaNumber:"",
                        serialNumber:"",
                        localtion:"石龙仔三楼机房",
                        fullName:"",
                        descCondition:"",
                        userNo:"170711124",
                        BDateString:"2017-10-12",
                        desc:"信息中心",
                    },
                    {
                        tagNumber:"cs125",
                        description:"测试数据",
                        modelNumber:"",
                        cmaNumber:"",
                        serialNumber:"",
                        localtion:"石龙仔三楼机房",
                        fullName:"",
                        descCondition:"",
                        userNo:"170711124",
                        BDateString:"2017-10-12",
                        desc:"信息中心",
                    }
                ],
                errorlist:[],
                errorMess:false,
                files:[]
            }
        },
        methods: {
//            根据表名清空各表数据
            clear:function (tableName) {
                let self = this;
                self.loading = true;
                self.url = self.hrefLoction+'deleteALLPdaAssetInforTable.json?message='+tableName;
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    self.$message(response.data.message);
                }, (response) => {
                    console.log('error');
                });
            },
//            erp表数据同步
            getFile:function () {
                this.errorlist = [];
                this.errorMess = false
                var file=document.getElementById("file1").files[0];
                console.log(file);
                this.files[0]=file;
            },
            postInsert:function (tableName) {

                var vm = this;

                console.log(vm.files);
                if(vm.files.length == 0){
                    vm.$message("未选择文件");
                    return 0;
                }
                var formData = new FormData();
                formData.append('message',tableName)
                formData.append('multipartFile',vm.files[0]);
                $.ajax({
                    url: vm.hrefLoction+"parseExcelPdaAssetInfor.json",
                    type: "post",
                    data: formData,
                    cache: false,
                    contentType: false,
                    processData: false,
                    mimeType: "multipart/form-data",
                    success: function (data) {
                        var obj = JSON.parse(data);
                        console.log(obj);
                        console.log(obj.dataInfo);
                        if(obj["extData"] == -10000 || obj["statusCode"] != 0){
                            vm.$message(obj["message"]);
                            return 0;
                        }
                        vm.$message("成功"+obj["dataInfo"].listData.length+"条！"+"失败"+obj["extData"].length+"条！");
                        vm.errorlist = obj["extData"];
                        if(vm.errorlist.length>0){
                            vm.errorMess = true
                        }
                    },
                    error:function () {
                        vm.$message("上传错误");
                    }
                })
            },
            tongbu:function () {
                let self = this;
                self.loading = true;
                self.url = self.hrefLoction+'nsertREPPdaAssetInfor.json';
                self.$axios.get(self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    self.$message(response.data.message);
                }, (response) => {
                    console.log('error');
                });
            }
        }
    }
</script>
<style scoped>

    .errorMess {
        height: 200px;
        overflow-y: scroll;
        margin-left: 50px;
        color: red;
        z-index: 999;
        border: solid 1px #aaa;
        margin-right: 50px;
        margin-top: 30px;
        padding-left: 30px;
        padding-top: 10px;
        padding-bottom: 10px;
    }
    .el-tabs--border-card {
        background: #fff;
        border: 1px solid #d1dbe5;
        overflow: scroll;
    }
</style>
