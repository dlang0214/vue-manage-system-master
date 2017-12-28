<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>年度盘点汇总</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
          <el-switch
                @change="changeChu"
                v-model="ischu"
                on-color="#13ce66"
                off-color="#ff4949">
          </el-switch>
            <span v-if="ischu==true" style="padding-left: 10px;padding-top: 4px" >初盘</span>
            <span v-else style="padding-left: 10px;padding-top: 4px">复盘</span>
        <span style="margin-left: 50px">选择年份：</span>
        <el-date-picker
            v-model="thisyear"
            align="right"
            type="year"
            placeholder="选择年" @change="setYear">
        </el-date-picker>
        </div>
        <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <el-table-column prop="ym" label="日期" sortable width="150">
            </el-table-column>
            <el-table-column prop="countUserNoA" label="列管盘点总人数" sortable >
            </el-table-column>
            <el-table-column prop="countUserNo" label="资管盘点总人数" sortable>
            </el-table-column>
            <el-table-column prop="countPdaA" label="列管总数" sortable >
            </el-table-column>
            <el-table-column prop="countPda" label="资管总数" sortable >
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
                url:"",
                tableData: [],//表格展示数据
                chuData:[],   //初盘数据
                fuData:[],   //复盘数据
                cur_page: 1,
                thisyear:new Date(),
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                chartData:[],
                is_search: false,
                ischu:true,
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

        },
        methods: {
            setYear:function () {
                this.getchuData();
                this.getfuData();
                console.log(this.thisyear)
            },
            changeChu:function () {
                if(this.ischu == true){
                    this.tableData=this.chuData;
                }else {
                    this.tableData=this.fuData;
                }
            },
            getchuData(){//获取初盘数据
                let self = this;
                let year = self.thisyear.getFullYear();
                self.chartData=[];
                self.url = self.hrefLoction+'PdaNumberYear.json?year='+year+'&isFinance=1';
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                  self.chuData=response.data.dataInfo.listData;
                  self.tableData= self.chuData;
                }, (response) => {
                    console.log('error');
                });
            },
            getfuData(){//获取复盘数据
                let self = this;
                let year = self.thisyear.getFullYear();
               self.url = self.hrefLoction+'PdaNumberYear.json?year='+year+'&isFinance=0';
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
                console.log("aaaa1111");
                this.router.replace({ name: '/vuetable', params: { userId: 123 }})
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
