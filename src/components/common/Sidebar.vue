<template>
    <div class="sidebar">
        <el-menu :default-active="onRoutes" class="el-menu-vertical-demo"  unique-opened router>
            <template v-for="item in datas">
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
                        icon: 'el-icon-document',
                        index: 'readme',
                        title: '功能简述'
                    },
                    {
                        icon: 'el-icon-menu',
                        index: '2',
                        title: '人员盘点报表',
                        subs: [
                            {
                                index: 'basetable',
                                title: '人员年度盘点汇总'
                            },
                            {
                                index: 'vuetable',
                                title: '人员盘点详情'
                            },

                        ]
                    },
                    {
                        icon: 'el-icon-circle-check',
                        index: '3',
                        title: '盘亏盘盈报表',
                        subs: [
                            {
                                index: 'loseall',
                                title: '年度盘亏盘盈汇总',
                            },
                            {
                                index: 'pankuitable',
                                title: '盘亏部门信息'
                            },
                            {
                                index: 'losetable',
                                title: '盘盈部门信息'
                            },

                        ]
                    },
                    {
                        icon: 'el-icon-information',
                        index: '4',
                        title: '差异化数据报表',
                        subs: [
                            {
                                index: 'unsameall',
                                title: '差异化年度汇总'
                            },
                            {
                                index: 'unsametable',
                                title: '差异化详情'
                            }

                        ]
                    },
                    {
                        icon: 'el-icon-search',
                        index: 'selectmess',
                        title: '查询资产信息'
                    },
                    {
                        icon: 'el-icon-edit',
                        index: '6',
                        title: '盘点信息录入',
                        subs: [
                            {
                                index: 'baseform',
                                title: '手工盘点'
                            },
                            {
                                index: 'vueeditor',
                                title: '批量导入'
                            },
                        ]
                    },
//                    {
//                        icon: 'el-icon-star-on',
//                        index: 'basecharts',
//                        title: '图表'
//                    },
                    {
                        icon: 'el-icon-setting',
                        index: '7',
                        title: '管理员管理',
                        subs: [
                            {
                                index: 'AuthorizationManagement',
                                title: '权限管理'
                            },
                            {
                                index: 'userManage',
                                title: '人员管理'
                            },
                            {
                                index: 'roleManager',
                                title: '角色管理'
                            },
                            {
                                index: 'download',
                                title: '数据下载'
                            },
                            {
                                index: 'pandianbiao',
                                title: '资产表管理'
                            },
                            {
                                index: 'loseTnum',
                                title: '无标签表管理'
                            },


                        ]
                    },

                ],
                pids:[],
                datas:[]

            }
        },
        computed:{
            onRoutes(){
                return this.$route.path.replace('/','');
            }
        },
        mounted:function () {
            this.getData()
        },
        methods:{
            getData:function () {
                let self = this;
                self.url = self.hrefLoction+'findUserRole.json?userNo='+JSON.parse(localStorage.getItem("pdaUserMess")).userNo;
                self.$axios.get( self.url
                ).then((response) => {
                    console.log(response);
                    self.loading = false;
                    if(response.data.statusCode==0){
                       for(var i=0;i<response.data.extData.length;i++){
                           if( response.data.extData[i].isCheck==1){
                           if($.inArray(response.data.extData[i].fPName,self.pids)>=0){

                           }else {
                               self.pids.push(response.data.extData[i].fPName);
                               self.datas.push(
                                   {icon: response.data.extData[i].incon,
                                   index: response.data.extData[i].url,
                                   title: response.data.extData[i].fPName,
                                   subs:[]});
                           }}
                       }
                       console.log(self.pids);


                        for (var i = 0;i<self.pids.length;i++){
                            for(var j=0;j<response.data.extData.length;j++){
                                    if(response.data.extData[j].fPName == self.pids[i] &&  response.data.extData[j].isCheck=="1"){
                                        self.datas[i].subs.push({
                                            index: response.data.extData[j].url,
                                            title: response.data.extData[j].pName
                                        });
                                    }


                            }
                        }
                        console.log(self.datas);
                    }
                    else {
                        self.$message(response.data.message)
                    }

                }, (response) => {
                    console.log('error');
                });
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
