<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>盘亏/已盘对比图</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
                <el-checkbox v-model="isnotF">初盘</el-checkbox>
                <el-checkbox v-model="isF">复盘</el-checkbox>
        <span style="margin-left: 50px">选择年份：</span>
        <el-date-picker
            v-model="thisyear"
            align="right"
            type="year"
            placeholder="选择年" @change="setYear">
        </el-date-picker>
        </div>
        <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange" >
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="ym" label="年月" sortable width="150">
            </el-table-column>
            <el-table-column  label="初盘/复盘" sortable width="120"  prop="ym">
                <template scope="scope" style="padding-left: -18px;padding-right: -18px">
                    <div style="border-bottom: solid 1px #dfe6ec;padding-left: 20px">初盘</div>
                    <div style="padding-left: 20px">复盘</div>
                </template>
            </el-table-column>
            <el-table-column  label="总数量" sortable width="180">
                <template scope="scope" style="padding-left: -18px;padding-right: -18px">
                    <div style="border-bottom: solid 1px #dfe6ec">{{scope.row.isF.countNumber}}</div>
                    <div>{{scope.row.isNotF.countNumber}}</div>
                </template>
            </el-table-column>
            <el-table-column label="操作" width="180" >
                <template scope="scope">
                    <el-button size="small"
                            @click="handleEdit(scope.$index, scope.row)">查看本月个人详情</el-button>
                </template>
            </el-table-column>
        </el-table>


    </div>
</template>
<script>
    import Schart from 'vue-schart';
    export default {
        components: {
            Schart
        },
        data() {
            return {
                tableData: [],//表格展示数据
                chuData:[],   //初盘数据
                fuData:[],   //复盘数据
                cur_page: 1,
                isnotF:true,
                isF:true,
                thisyear:new Date(),
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                chartData:[],
                is_search: false,
                options1: {
                    title: "年度盘点汇总",
                    bgColor: '#aaa',
                    titleColor: '#ffffff',
                    fillColor: '#e0f2f1',
                    axisColor: '#ffffff',
                    contentColor: '#999'
                },

            }
        },
        created(){
            this.getchuData();
            this.getfuData();

        },
        computed: {
            data(){
                const self = this;
                return self.tableData.filter(function(d){
                    let is_del = false;
                    for (let i = 0; i < self.del_list.length; i++) {
                        if(d.name === self.del_list[i].name){
                            is_del = true;
                            break;
                        }
                    }

                    if(!is_del){
                        if(d.address.indexOf(self.select_cate) > -1 &&
                            (d.name.indexOf(self.select_word) > -1 ||
                            d.address.indexOf(self.select_word) > -1)
                        ){
                            return d;
                        }
                    }
                })
            }
        },
        watch:{
            isnotF:function (val, oldVal) {
                if(val==true){

                }
            },
            isF:function (val, oldVal) {

            }

        },
        methods: {
            setYear:function () {
                this.getchuData();
                this.getfuData();
                console.log(this.thisyear)
            },
            getchuData(){//获取初盘数据
                let self = this;
                let year = self.thisyear.getFullYear();
                self.chartData=[];
                if(process.env.NODE_ENV === 'development'){
                    self.url = 'http://172.16.110.125:8080/swdAPP/common/PdaAssetUser/PdaLossYear.json?year='+year;
                };
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                  self.chuData=response.data.dataInfo.listData;
                  self.tableData= self.chuData;
                  for (var i=0;i<self.chuData.length;i++){
                      console.log("aaaa");
                      self.chartData.push({name:i+1+"月",value:self.chuData[i].countNumber})
                  }

                  console.log(self.chartData);
                }, (response) => {
                    console.log('error');
                });

            },
            getfuData(){//获取复盘数据
                let self = this;
                let year = self.thisyear.getFullYear();
                if(process.env.NODE_ENV === 'development'){
                    self.url = 'http://appinter.sunwoda.com/common/PdaAssetUser/PdaNumberYear.json?year='+year+'&isFinance=0';
                };
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.fuData=response.data.dataInfo.listData;

                }, (response) => {
                    console.log('error');
                });

            },
            search(){
                this.is_search = true;
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            handleEdit(index, row) {
                this.$message('编辑第'+(index+1)+'行');
            },
            handleDelete(index, row) {
                this.$message.error('删除第'+(index+1)+'行');
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            }
        }
    }
</script>

<style scoped>
.handle-box{
    margin-bottom: 20px;
}
.handle-select{
    width: 120px;
}
.handle-input{
    width: 300px;
    display: inline-block;
}
</style>
