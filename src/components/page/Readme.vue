<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-setting"></i> 功能概述</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="ms-doc">
            <h3>资产盘点报表</h3>
            <article>
                <h1>简介</h1>
                <p>sunwoda资产盘点后台，统计每月的资产报表，分析资产数据</p>
                <h2>功能</h2>
                <el-checkbox disabled checked>年度盘点汇总</el-checkbox>
                <br>
                <el-checkbox disabled checked>人员盘点信息</el-checkbox>
                <br>
                <el-checkbox disabled checked>盘亏盘赢信息汇总</el-checkbox>
                <br>
                <el-checkbox disabled checked>差异化信息汇总</el-checkbox>
                <br>
                <el-checkbox disabled checked>资产信息查询</el-checkbox>
                <br>
                <el-checkbox disabled checked>资产信息录入</el-checkbox>
                <br>
                <el-checkbox disabled checked>资产信息批量导入</el-checkbox>
            </article>
        </div>

    </div>
</template>

<script>
    export default {
        data: function(){
            return {}
        }
    }
</script>

<style scoped>
    .ms-doc{
        width:100%;
        max-width: 980px;
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
    }
    .ms-doc h3{
        padding: 9px 10px 10px;
        margin: 0;
        font-size: 14px;
        line-height: 17px;
        background-color: #f5f5f5;
        border: 1px solid #d8d8d8;
        border-bottom: 0;
        border-radius: 3px 3px 0 0;
    }
    .ms-doc article{
        padding: 45px;
        word-wrap: break-word;
        background-color: #fff;
        border: 1px solid #ddd;
        border-bottom-right-radius: 3px;
        border-bottom-left-radius: 3px;
    }
    .ms-doc article h1{
        font-size:32px;
        padding-bottom: 10px;
        margin-bottom: 15px;
        border-bottom: 1px solid #ddd;
    }
    .ms-doc article h2 {
        margin: 24px 0 16px;
        font-weight: 600;
        line-height: 1.25;
        padding-bottom: 7px;
        font-size: 24px;
        border-bottom: 1px solid #eee;
    }
    .ms-doc article p{
        margin-bottom: 15px;
        line-height: 1.5;
    }
    .ms-doc article .el-checkbox{
        margin-bottom: 5px;
    }
</style>
