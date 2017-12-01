<template>
    <div class="sidebar">
        <el-menu :default-active="onRoutes" class="el-menu-vertical-demo"  unique-opened router>
            <template v-for="item in items">
                <template v-if="item.subs">
                    <el-submenu :index="item.index">
                        <template slot="title"><i :class="item.icon"></i>{{ item.title }}</template>
                        <el-menu-item  v-for="(subItem,i) in item.subs" :key="i" :index="subItem.index">{{ subItem.title }}
                        </el-menu-item>
                    </el-submenu>
                </template>
                <template v-else>
                    <el-menu-item :index="item.index">
                        <i :class="item.icon"></i>{{ item.title }}
                    </el-menu-item>
                </template>
            </template>
        </el-menu>
    </div>
</template>

<script>

    export default {
        data() {
            return {
                items: [
                    {
                        icon: 'el-icon-setting',
                        index: 'readme',
                        title: '功能简述'
                    },
                    {
                        icon: 'el-icon-menu',
                        index: '2',
                        title: '表格',
                        subs: [
                            {
                                index: 'basetable',
                                title: '年度盘点汇总'
                            },
                            {
                                index: 'vuetable',
                                title: '人员盘点汇总'
                            },
                            {
                                index: 'pankuitable',
                                title: '盘亏报表'
                            },
                            {
                                index: 'losetable',
                                title: '未盘对比'
                            },
                            {
                                index: 'unsametable',
                                title: '差异化报表'
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-date',
                        index: '3',
                        title: '表单',
                        subs: [
                            {
                                index: 'baseform',
                                title: '手工盘点'
                            },
//                            {
//                                index: 'vueeditor',
//                                title: '编辑器'
//                            },
//                            {
//                                index: 'markdown',
//                                title: 'markdown'
//                            },
//                            {
//                                index: 'upload',
//                                title: '文件上传'
//                            }
                        ]
                    },
//                    {
//                        icon: 'el-icon-star-on',
//                        index: 'basecharts',
//                        title: '图表'
//                    },
//                    {
//                        icon: 'el-icon-upload2',
//                        index: 'drag',
//                        title: '拖拽'
//                    }
                ]
            }
        },
        computed:{
            onRoutes(){
                return this.$route.path.replace('/','');
            }
        }
    }
</script>

<style scoped>
    .sidebar{
        display: block;
        position: absolute;
        width: 250px;
        left: 0;
        top: 70px;
        bottom:0;
    }
    .sidebar > ul {
        height:100%;
    }
    .el-menu-vertical-demo{
        margin-top: 30px;
        margin-left: 20px;
        margin-bottom:20px;
        background: #ffffff;
        border: solid 1px #eee;
    }
    .el-menu--horizontal.el-menu--dark .el-submenu .el-menu-item.is-active, .el-menu-item.is-active {
        color: #20a0ff;
        border-right: 2px solid;
    }
</style>
