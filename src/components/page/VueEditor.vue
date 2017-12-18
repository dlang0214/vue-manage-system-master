<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 表单</el-breadcrumb-item>
                <el-breadcrumb-item>批量导入</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="plugins-tips">
           导入的文件必须是Excel文件，格式如下（必须严格遵循下面格式）
            <!--<el-button icon="upload2">下载模板</el-button>-->
        </div>
        <el-tabs v-model="activeName2" >
            <el-tab-pane label="固定资产导入" name="first">
                <el-table
                    :data="tableData"
                    stripe
                    style="width: 100%">
                    <el-table-column
                        prop="date"
                        label="盘点年月"
                    >
                    </el-table-column>
                    <el-table-column
                        prop="pNo"
                        label="资产编号"
                    >
                    </el-table-column>
                    <el-table-column
                        prop="time"
                        label="盘点时间"
                        width="180">
                    </el-table-column>
                    <el-table-column
                        prop="person"
                        label="盘点人工号"
                        width="120">
                    </el-table-column>
                    <el-table-column
                        prop="stats"
                        label="盘点状态">
                    </el-table-column>
                    <el-table-column
                        prop="isChange"
                        label="是否更改标签">
                    </el-table-column>
                    <el-table-column
                        prop="remark"
                        label="盘点备注">
                    </el-table-column>
                    <el-table-column
                        prop="isF"
                        label="初盘/复盘">
                    </el-table-column>
                    <el-table-column
                        prop="pDept"
                        label="盘点部门">
                    </el-table-column>
                </el-table>
                <div style="margin-top: 10px">
                    <el-button type="primary" style="position: relative">选择文件<i class="el-icon-upload el-icon--right"></i>
                        <input type="file" id="file" @change="getFile" style="position: absolute;width: 100%;height: 100%;top: 0;left: 0;opacity: 0"/>
                    </el-button>{{fileName}}
                    <el-button type="primary" style="position: relative" @click="uploadFile">确认上传<i class="el-icon-upload el-icon--right"></i>
                    </el-button>
                </div>
            </el-tab-pane>
            <el-tab-pane label="异常资产导入" name="second">
                <el-table
                    :data="tableDataY"
                    stripe
                    style="width: 100%">
                    <el-table-column
                        prop="pNo"
                        label="资产编号"
                    >
                    </el-table-column>
                    <el-table-column
                        prop="pName"
                        label="资产名称"
                        width="180">
                    </el-table-column>
                    <el-table-column
                        prop="stats"
                        label="规格型号">
                    </el-table-column>
                    <el-table-column
                        prop="pDept"
                        label="使用部门">
                    </el-table-column>
                    <el-table-column
                        prop="date"
                        label="未盘点年月">
                    </el-table-column>
                    <el-table-column
                        prop="isF"
                        label="初盘/复盘">
                    </el-table-column>
                </el-table>
                <div style="margin-top: 10px">
                    <el-button type="primary" style="position: relative">选择文件<i class="el-icon-upload el-icon--right"></i>
                        <input type="file" id="file1" @change="getFile1" style="position: absolute;width: 100%;height: 100%;top: 0;left: 0;opacity: 0"/>
                    </el-button>{{fileName1}}
                    <el-button type="primary" style="position: relative" @click="uploadFile1">确认上传<i class="el-icon-upload el-icon--right"></i>
                    </el-button>
                </div>
            </el-tab-pane>
        </el-tabs>

    </div>
</template>

<script>

    import ElButton from "../../../node_modules/element-ui/packages/button/src/button.vue";

    export default {
        components: {ElButton},
        data: function(){
            return {
                fileName:"",
                fileName1:"",
                files:[],
                files1:[],
                activeName2:"first",
                tableData: [
                    {
                    date: '201712',
                    pNo: 'H00471',
                    time: '2017/12/10  0:00:00',
                    person:"170711124",
                    stats:"正常使用",
                    isChange:"是",
                    remark:"",
                    isF:"初盘",
                    pDept:"信息中心"
                    },
                    {
                    date: '201712',
                    pNo: 'H00471',
                    time: '2017/12/10  0:00:00',
                    person:"170711124",
                    stats:"正常使用",
                    isChange:"是",
                    remark:"",
                    isF:"初盘",
                    pDept:"信息中心"
                },
                    {
                    date: '201712',
                    pNo: 'H00471',
                    time: '2017/12/10  0:00:00',
                    person:"170711124",
                    stats:"正常使用",
                    isChange:"是",
                    remark:"",
                    isF:"初盘",
                    pDept:"信息中心"
                },
                    {
                    date: '201712',
                    pNo: 'H00471',
                    time: '2017/12/10  0:00:00',
                    person:"170711124",
                    stats:"正常使用",
                    isChange:"是",
                    remark:"",
                    isF:"初盘",
                    pDept:"信息中心"
                },
                ],
                tableDataY:[
                    {
                        pNo: 'H00471',
                        pName: '双温水工模温机',
                        stats:"",
                        date: '201712',
                        pDept:"信息中心",
                        isF:"初盘",
                    },
                    {
                        pNo: 'H00471',
                        pName: '双温水工模温机',
                        stats:"",
                        date: '201712',
                        pDept:"信息中心",
                        isF:"初盘",
                    },
                    {
                        pNo: 'H00471',
                        pName: '双温水工模温机',
                        stats:"",
                        date: '201712',
                        pDept:"信息中心",
                        isF:"初盘",
                    },
                    {
                        pNo: 'H00471',
                        pName: '双温水工模温机',
                        stats:"",
                        date: '201712',
                        pDept:"信息中心",
                        isF:"初盘",
                    },
                ]
            }
        },

        methods: {
            getFile:function () {
                var file=document.getElementById("file").files[0];
                console.log(file);
                this.fileName=file.name
                this.files[0]=file;
            },
            uploadFile:function () {
                var vm = this;
                if(vm.files==[]){
                    vm.$message("未选择文件");
                    return 0;
                }
                var formData = new FormData();
                formData.append('multipartFile',vm.files[0]);
                $.ajax({
                    url: vm.hrefLoction+"parseExcelIntoPdaAsset.json",
                    type: "post",
                    data: formData,
                    cache: false,
                    contentType: false,
                    processData: false,
                    mimeType: "multipart/form-data",
                    success: function (data) {
                        var obj = JSON.parse(data);
                        vm.$message(obj["message"]);
                    },
                    error:function () {
                        vm.$message("上传错误");
                    }
                })
            },
            getFile1:function () {
                var file=document.getElementById("file1").files[0];
                console.log(file);
                this.fileName1=file.name
                this.files1[0]=file;
            },
            uploadFile1:function () {
                var vm = this;
                var formData = new FormData();
                formData.append('multipartFile',vm.files1[0]);
                if(vm.files1==[]){
                    vm.$message("未选择文件");
                    return 0;
                }
                $.ajax({
                    url: vm.hrefLoction+"parseExcelIntoPdaDifferent.json",
                    type: "post",
                    data: formData,
                    cache: false,
                    contentType: false,
                    processData: false,
                    mimeType: "multipart/form-data",
                    success: function (data) {
                        console.log(data)
                        var obj = JSON.parse(data);
                        vm.$message(obj["message"]);
                    },
                    error:function () {
                        vm.$message("上传错误");
                    }
                })
            }
        },
        computed: {

        }
    }
</script>
<style scoped>
    .editor-btn{
        margin-top: 20px;
    }
</style>
