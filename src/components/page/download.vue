<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 管理员管理</el-breadcrumb-item>
                <el-breadcrumb-item>信息下载</el-breadcrumb-item>
            </el-breadcrumb>

        </div>
        <el-tabs v-model="activeName">
            <el-tab-pane label="盘点数据下载" name="one">
                <el-form ref="form" :model="pandian" label-width="100px">
                    <el-form-item label="选择年月">
                        <el-date-picker v-model="pandian.month" type="month" placeholder="选择月"  style="width: 100%;"></el-date-picker>
                    </el-form-item>

                    <el-form-item label="初盘/复盘">
                        <el-radio-group v-model="pandian.isFinance">
                            <el-radio label="0">初盘</el-radio>
                            <el-radio label="1">复盘</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="资管/列管">
                        <el-radio-group v-model="pandian.isAdd">
                            <el-radio label="0">资管</el-radio>
                            <el-radio label="1">列管</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="download('pdaByUser')">下载盘点数据</el-button>
                    </el-form-item>
                </el-form>
            </el-tab-pane>
            <el-tab-pane label="盘亏数据下载" name="two">
                <el-form ref="form" :model="pandian" label-width="100px">
                    <el-form-item label="选择年月">
                        <el-date-picker v-model="pandian.month" type="month" placeholder="选择月"  style="width: 100%;"></el-date-picker>
                    </el-form-item>

                    <el-form-item label="初盘/复盘">
                        <el-radio-group v-model="pandian.isFinance">
                            <el-radio label="0">初盘</el-radio>
                            <el-radio label="1">复盘</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="资管/列管">
                        <el-radio-group v-model="pandian.isAdd">
                            <el-radio label="0">资管</el-radio>
                            <el-radio label="1">列管</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="download('pdaByLoss')">下载盘亏数据</el-button>
                    </el-form-item>
                </el-form>
            </el-tab-pane>
            <el-tab-pane label="盘盈数据下载" name="three">
                <el-form ref="form" :model="pandian" label-width="100px">
                    <el-form-item label="选择年月">
                        <el-date-picker v-model="pandian.month" type="month" placeholder="选择月"  style="width: 100%;"></el-date-picker>
                    </el-form-item>

                    <el-form-item label="初盘/复盘">
                        <el-radio-group v-model="pandian.isFinance">
                            <el-radio label="0">初盘</el-radio>
                            <el-radio label="1">复盘</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="download('pdaByWin')">下载盘盈数据</el-button>
                    </el-form-item>
                </el-form>
            </el-tab-pane>
            <el-tab-pane label="差异化数据下载" name="four">
                <el-form ref="form" :model="pandian" label-width="100px">
                    <el-form-item label="选择年月">
                        <el-date-picker v-model="pandian.month" type="month" placeholder="选择月"  style="width: 100%;"></el-date-picker>
                    </el-form-item>

                    <el-form-item label="初盘/复盘">
                        <el-radio-group v-model="pandian.isFinance">
                            <el-radio label="0">初盘</el-radio>
                            <el-radio label="1">复盘</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="资管/列管">
                        <el-radio-group v-model="pandian.isAdd">
                            <el-radio label="0">资管</el-radio>
                            <el-radio label="1">列管</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="download('pdaByDiffrent')">下载差异化数据</el-button>
                    </el-form-item>
                </el-form>
            </el-tab-pane>

        </el-tabs>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data: function(){
            return {
                activeName:'one',
                pandian:{
                    isFinance:"0",
                    month:new Date(),
                    isAdd:"0"
                },
            }
        },
        created(){

        },
        watch:{

        },
        methods: {
            download:function (method) {
                var href ="http://appinter.sunwoda.com/common/PdaAssetUser/downloadExcel.json";
                var mon
                if(this.pandian.month.getMonth()+1>9){
                    mon = this.pandian.month.getMonth()+1;
                }else {
                    mon = "0"+(this.pandian.month.getMonth()+1);
                }
                console.log(mon)
                if(method == 'pdaByWin'){
                    window.location = href+'?method='+method+"&year=2017&isFinance="+this.pandian.isFinance+"&month="+mon
                }else {
                    window.location = href+'?method='+method+"&year="+this.pandian.month.getFullYear()+
                        "&isFinance="+this.pandian.isFinance+"&isAdd="+this.pandian.isAdd+"&month="+mon
                }


            }


        },
        computed:{

        },
        beforeMount(){

        }
    }
</script>

<style >

    .el-form{
        width: 300px;
    }
</style>
