<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 差异化数据报表</el-breadcrumb-item>
                <el-breadcrumb-item>年度汇总</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
          <el-switch
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
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="ym" label="日期" sortable width="150">
            </el-table-column>
            <el-table-column  label="固定资产未盘点总数" sortable >
                <template scope="scope">
                    {{scope.row.isF.countNumber}}
                </template>
            </el-table-column>
            <el-table-column  label="列管资产未盘点总数" sortable>
                <template scope="scope">
                    {{scope.row.isF.countANumber}}
                </template>
            </el-table-column>
            <el-table-column label="未盘点状态" width="180">
                <template scope="scope">
                 <div v-if="scope.row.isF.isFinance==0">
                     初盘
                 </div>
                    <div v-else="scope.row.isF.isFinance==1">
                        复盘
                    </div>
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
                ischu:true
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
            ischu:function (val, oldVal) {
                if(val==true){
                    this.tableData=this.chuData;
                }else {
                    this.tableData=this.fuData;
                }
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
                self.url = self.hrefLoction+'PdaDifferYear.json?year='+year+'&isFinance=0';
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
                    self.url = self.hrefLoction+'PdaDifferYear.json?year='+year+'&isFinance=1';
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
