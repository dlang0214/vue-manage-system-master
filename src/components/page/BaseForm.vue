<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 表单</el-breadcrumb-item>
                <el-breadcrumb-item>手工盘点</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <el-tabs v-model="activeName">
            <el-tab-pane label="盘点信息录入" name="first">
                <div class="form-box">
                <el-form ref="form" :model="formGu" label-width="100px">
                    <el-form-item label="资产编号">
                        <el-input v-model="formGu.invCode" placeholder="请输入资产编号"></el-input>
                    </el-form-item>
                    <el-form-item label="盘点时间">
                        <el-date-picker type="date" placeholder="选择日期" v-model="date1" style="width: 100%;"></el-date-picker>
                    </el-form-item>
                    <el-form-item label="盘点人">
                        <el-input v-model="formGu.invUserNo" placeholder="请输入盘点人工号"></el-input>
                    </el-form-item>
                    <el-form-item label="备注">
                        <el-input v-model="formGu.invAdditional"></el-input>
                    </el-form-item>
                    <el-form-item label="盘点部门">
                        <el-input v-model="formGu.pdaDesc" placeholder="请输入盘点部门"></el-input>
                    </el-form-item>
                    <el-form-item label="使用部门">
                        <el-input v-model="formGu.desc" placeholder="请输入使用部门"></el-input>
                    </el-form-item>

                    <el-form-item label="初盘/复盘">
                        <el-radio-group v-model="formGu.isFinance">
                            <el-radio label="0">初盘</el-radio>
                            <el-radio label="1">复盘</el-radio>
                        </el-radio-group>
                    </el-form-item>
                    <el-form-item label="更改标签">
                            <el-switch v-model="formGu.invReplace"
                                       on-text="是"
                                       off-text="否"
                            >
                            </el-switch>
                    </el-form-item>
                    <el-form-item label="盘点状态">
                        <el-radio-group v-model="formGu.invStutas">
                            <el-radio label="1">正常使用</el-radio>
                            <el-radio label="2">闲置资产</el-radio>
                            <el-radio label="3">待维修</el-radio>
                            <el-radio label="4">待报废</el-radio>
                        </el-radio-group>
                    </el-form-item>

                    <el-form-item>
                        <el-button type="primary" @click="onSubmit">提交</el-button>
                        <el-button>取消</el-button>
                    </el-form-item>
                </el-form>
            </div>
            </el-tab-pane>
            <el-tab-pane label="资产盘点异常录入" name="second">
                <div class="form-box">
                <el-form ref="form" :model="form" label-width="100px">
                    <el-form-item label="资产编号">
                        <el-input v-model="form.tagNumber" placeholder="请输入资产编号"></el-input>
                    </el-form-item>
                    <el-form-item label="资产名称">
                        <el-input v-model="form.description" placeholder="请输入资产名称"></el-input>
                    </el-form-item>
                    <el-form-item label="盘点时间">
                        <el-date-picker type="date" placeholder="选择日期" v-model="date2" style="width: 100%;"></el-date-picker>
                    </el-form-item>
                    <el-form-item label="规格型号">
                        <el-input v-model="form.modelNumber" placeholder="请输入规格型号"></el-input>
                    </el-form-item>
                    <el-form-item label="使用部门">
                        <el-input v-model="form.desc"  placeholder="请输入使用部门"></el-input>
                    </el-form-item>

                    <el-form-item label="初盘/复盘">
                            <el-radio-group v-model="form.isFinance">
                                <el-radio label="0">初盘</el-radio>
                                <el-radio label="1">复盘</el-radio>
                            </el-radio-group>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="onSubmitY">提交</el-button>
                    </el-form-item>
                </el-form>
            </div>
            </el-tab-pane>

        </el-tabs>


    </div>
</template>

<script>
    export default {
        data: function(){
            return {
                activeName:"first",
                date1:new Date(),
                date2:new Date(),
                formGu:{
                    invYM:"",
                    invCode:"",
                    invTimeStr:"",
                    invUserNo:"",
                    invStutas:"1",
                    invReplace:false,
                    invAdditional:"",
                    isFinance:"0",
                    pdaDesc:"",
                    desc:""
                },
                form:{
                    tagNumber:"",
                    desc:"",
                    description:"",
                    modelNumber:"",
                    isFinance:"0",
                    addDateString:""
                }
            }
        },
        methods: {
            onSubmit() {
                var vm = this;
                console.log(this.formGu);
                 if(vm.formGu.invReplace==true){
                     vm.formGu.invReplace=1
                 }else {
                     vm.formGu.invReplace=0
                 }
                if( vm. formGu.invCode==""){
                    vm.$message("资产编号不能为空");
                    return 0
                }
                if( vm.formGu.invUserNo==""){
                    vm.$message("盘点人不能为空");
                    return 0
                }
                if( vm.formGu.pdaDesc==""){
                    vm.$message("盘点部门不能为空");
                    return 0
                }
                 var month;
                 var day;
                 if(this.date1.getMonth()+1<10){
                     month="0"+(this.date1.getMonth()+1)
                 }else {
                     month=(this.date1.getMonth()+1)
                 }
                if(this.date1.getDate()<10){
                   day="0"+this.date1.getDate()
                }else {
                    day=this.date1.getDate()
                }
                this.formGu.invYM=this.date1.getFullYear()+""+month
                this.formGu.invTimeStr=this.date1.getFullYear()+"-"+month+"-"+day+" "+"00:00:00"

                $.ajax({
                    type:"post",
                    url:"http://appinter.sunwoda.com/common/PdaAssetUser/check.json",
                    data:vm.formGu,
                    dataType:"json",
                    success:function (data) {
                        if(data.statusCode == 0){
                            vm.formGu = { }
                        }
                        vm.$message(data.message);
                    },
                    error:function (da) {
                        vm.$message(data.message);
                    }
                });
            },
            onSubmitY(){
                console.log("Aaaa");
                console.log(this.form);
                var vm = this;
                var month;
                if(this.date1.getMonth()+1<10){
                    month="0"+(vm.date2.getMonth()+1)
                }else {
                    month=(vm.date2.getMonth()+1)
                }
                vm.form.addDateString = vm.date2.getFullYear()+""+month;
                if( vm.form.tagNumber==""){
                    vm.$message("资产编号不能为空");
                    return 0
                }
                if( vm.form.description==""){
                    vm.$message("资产名称不能为空");
                    return 0
                }
                if( vm.form.desc==""){
                    vm.$message("使用部门不能为空");
                    return 0
                }
                $.ajax({
                    type:"post",
                    url:"http://appinter.sunwoda.com/common/PdaAssetUser/insertPdaDifferent.json",
                    data:vm.form,
                    dataType:"json",
                    success:function (data) {
                        console.log(data);
                        if(data.statusCode==0){
                            vm.$message(data.message);
                            vm.form={}
                        }
                        else {
                            vm.$message(data.message);
                        }
                    },
                    error:function (da) {
                        console.log(da);
                    }
                });
            }
        }
    }
</script>
