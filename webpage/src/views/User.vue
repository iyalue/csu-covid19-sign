<template>
    <div class="wb-list">
        <div class="inner">
            <img src="@/assets/logo.png" class="logo" alt="logo"/>
            <h1>CSU-COVID19-SIGN</h1>
            <p>关于用户他自己的一些事情</p>
            <div class="alert">
                <el-alert type="warning" center :closable="false">
                    <template slot="title">
                        服务反馈与通知群 {{this.$store.state.group}}
                    </template>
                </el-alert>
            </div>
            <div class="readme">
                <h2>常见问答</h2>
                <el-row :gutter="16 ">
                    <el-col :sm="12" :xs="24">
                        <h3>关于时间</h3>
                        <p>新用户签到打卡任务将于次日起生效。老用户退出服务将移除包括当日在内的打卡任务。</p>
                        <p>新登录用户的签到打卡时间将根据负载自动指派，不支持用户手动修改。</p>
                    </el-col>
                    <el-col :sm="12" :xs="24">
                        <h3>一些问题</h3>
                        <p>暂不支持当天打卡任务状态查询(会支持的)。</p>
                        <p>短信通知服务和随机打卡时间服务现已上线。</p>
                        <p>普通用户退出后再登录，打卡时间不会变化。</p>
                    </el-col>
                </el-row>
            </div>
            <el-row>
                <el-col :lg="16" :sm="16" :xs="24">
                    <el-form class="form" ref="form" label-position="left" label-width="60px"
                             v-loading="loading" :rules="rules" :model="formData">
                        <el-form-item label="账号" prop="username">
                            <el-input v-model="formData.username" placeholder="信息门户账户"></el-input>
                        </el-form-item>
                        <el-form-item label="手机" prop="phone">
                            <el-input v-model="formData.phone" placeholder="用于身份认证"></el-input>
                        </el-form-item>
                        <el-form-item label="时间" prop="time" style="text-align: left">
                            <el-select v-model="formData.time" placeholder="打卡时段"
                                       :disabled="taskInfo.randOpt !== 'Yes'">
                                <el-option v-for="item in taskTimeGroup" :key="item.value"
                                    :label="item.label" :value="item.value">
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <el-button type="primary" @click="check_form('mod_task')"
                                   plain v-if="taskInfo.randOpt === 'Yes'">修改任务</el-button>
                        <el-button type="success" @click="check_form('get_task')" plain>查询状态</el-button>
                        <el-button type="danger" @click="check_form('del_task')" plain>解除绑定</el-button>
                    </el-form>
                </el-col>
                <el-col :lg="8" :sm="8" :xs="24" class="info">
                    <el-row class="item">
                        <el-col :span="12" class="key">
                            <p>用户昵称</p>
                        </el-col>
                        <el-col :span="12" class="val">
                            <p>{{ taskInfo.nickname }}</p>
                        </el-col>
                    </el-row>
                    <el-row class="item">
                        <el-col :span="12" class="key">
                            <p>打卡时间</p>
                        </el-col>
                        <el-col :span="12" class="val">
                            <p>{{ taskInfo.taskTime }}</p>
                        </el-col>
                    </el-row>
                    <el-row class="item">
                        <el-col :span="12" class="key">
                            <p>随机时间</p>
                        </el-col>
                        <el-col :span="12" class="val">
                            <p>{{ taskInfo.randOpt }}</p>
                        </el-col>
                    </el-row>
                    <el-row class="item">
                        <el-col :span="12" class="key">
                            <p>短信通知</p>
                        </el-col>
                        <el-col :span="12" class="val">
                            <p>{{ taskInfo.smsOpt }}</p>
                        </el-col>
                    </el-row>
                </el-col>
            </el-row>
            <div class="tips">
                <h2>有多少用户？</h2>
                <p>懂的都懂<br/>
                    不懂的我也不解释<br/>
                    这里水很深，利益牵扯太大<br/>
                    网上大部分内容都已经删除干净了<br/>
                    懂的人都是自己悟的<br/>
                    别不懂装懂<br/>
                    懂了吗<br/>
                </p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'List',
    data() {
        return {
            loading: false,
            formData: {
                username: "",
                phone: "",
                time: ""
            },
            taskTimeGroup: [
                {value: "00", label: "00:00-00:59"},
                {value: "01", label: "01:00-01:59"},
                {value: "02", label: "02:00-02:59"},
                {value: "03", label: "03:00-03:59"},
                {value: "04", label: "04:00-04:59"},
                {value: "05", label: "05:00-05:59"},
                {value: "06", label: "06:00-06:59"},
                {value: "07", label: "07:00-07:59"},
                {value: "08", label: "08:00-08:59"},
                {value: "09", label: "09:00-09:59"},
            ],
            taskInfo: {
                nickname: "",
                taskTime: "",
                randOpt: "",
                smsOpt: ""
            },
            rules: {
                username: [
                    {required: true, message: '请输入信息门户账户', trigger: 'blur'},
                    {min: 6, max: 14, message: '长度在 6 到 14 个字符', trigger: 'blur'}
                ],
                phone: [
                    {required: true, message: '请输入收信电话号码', trigger: 'blur'},
                    {min: 11, max: 11, message: '长度应为 11 个数字', trigger: 'blur'}
                ]
            }
        }
    },
    methods: {
        check_form: function (task) {
            this.$refs["form"].validate((valid) => {
                if (valid) {
                    console.log("Check success, submit!");
                    if (task === "get_task") {
                        this.user_task();
                    } else if (task === "del_task") {
                        this.user_logout();
                    } else if (task === "mod_task") {
                        this.user_modify();
                    }
                }
            });
        },
        user_task: function () {
            this.loading = true;
            let that = this;
            let data_host = this.$store.state.host;
            this.$http.get(data_host + `/user/task`,
                {
                    params: {
                        username: this.formData.username,
                        phone: this.formData.phone,
                    }
                })
                .then(function (res) {
                    that.loading = false
                    if (res.data.status === "success") {
                        that.taskInfo = res.data.data
                        that.formData.time = that.taskInfo["taskTime"].split(":")[0]
                        if(that.taskInfo.randOpt === 'Yes'){
                            that.taskInfo.taskTime = "时段随机"
                        }
                    }
                })
        },
        user_logout: function () {
            this.loading = true;
            let that = this;
            let data_host = this.$store.state.host;
            let http_data = {
                username: this.formData.username,
                phone: this.formData.phone,
            }
            this.$http.post(data_host + `/user/logout`, http_data)
                .then(function (res) {
                    console.log(res)
                    that.loading = false;
                    if (res.data.status === 'success') {
                        that.loading = false;
                        that.$message({
                            message: res.data.message,
                            type: 'success'
                        });
                        that.$router.push("/user");
                    } else {
                        that.$message({
                            message: res.data.message,
                            type: 'error'
                        });
                    }
                })
                .catch(function (res) {
                    that.loading = false;
                    console.log(res);
                })
        },
        user_modify: function () {
            this.loading = true;
            let that = this;
            let data_host = this.$store.state.host;
            let http_data = {
                username: this.formData.username,
                phone: this.formData.phone,
                time: this.formData.time
            }
            this.$http.put(data_host + `/user/task`, http_data)
                .then(function (res) {
                    that.loading = false
                    if (res.data.status === "success") {
                        that.$message({
                            message: res.data.message,
                            type: 'success'
                        });
                    } else {
                        that.$message({
                            message: res.data.message,
                            type: 'error'
                        });
                    }
                })
                .catch(function (res) {
                    that.loading = false;
                    console.log(res);
                })
        }
    }
}
</script>

<style lang="scss" scoped>
.wb-list {
    .info {
        padding: 36px 16px;

        .item {
            margin-bottom: 12px;

            p {
                margin: 0.5em 0;
            }

            .key {
                min-height: 38px;
                border-radius: 4px;
                background-color: rgba(145, 190, 240, 0.3);
            }

            .val {
                min-height: 38px;
                background-color: rgba(145, 190, 240, 0.1);
            }
        }
    }

    .form {
        width: 90%;
        margin-top: 36px;
        display: inline-block;
        text-align: center;
    }
}
</style>