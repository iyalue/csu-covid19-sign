<template>
    <div class="wb-home">
        <div class="inner">
            <img src="@/assets/logo.png" class="logo" alt="logo"/>
            <h1>CSU-COVID19-SIGN</h1>
            <p>简单几步即可建立羁绊</p>
            <div class="alert">
                <el-alert type="warning" center :closable="false">
                    <template slot="title">
                        服务反馈与通知群 {{ this.$store.state.group }}
                    </template>
                </el-alert>
            </div>
            <div class="readme">
                <h2>使用条款</h2>
                <el-row :gutter="36">
                    <el-col :sm="12" :xs="24">
                        <h3>关于打卡</h3>
                        <p>
                            本服务用于向用户提供，每日签到打卡的自动检查能力。旨在帮助用户在容易遗忘的时刻也能保持打卡不中断。
                        </p>
                        <p>
                            <span>这不代表你完全移交签到打卡的主动权，该服务仅作为减少工作量与提高效率的工具存在，</span>
                            <span>请自行衡量使用该服务所产生的影响，系统与用户可在任何时间主动终止服务。</span>
                        </p>
                    </el-col>
                    <el-col :sm="12" :xs="24">
                        <h3>关于服务</h3>
                        <p>
                            <span>本服务将在一个合适的时间，读取你上一次成功完成打卡的记录，并按照该记录进行签到打卡。</span>
                            <span>这一签到过程包含打卡必要的所有信息，并且该过程不需要用户的设备参与。</span>
                        </p>
                        <p>
                            <span>您可以在在服务自动选取的时间之前，自主完成签到打卡并填写您最新的打卡信息。</span>
                        </p>
                    </el-col>
                </el-row>
                <el-row :gutter="36">
                    <el-col :sm="12" :xs="24">
                        <h3>使用说明</h3>
                        <p>
                            <span>你需要在下方输入框填写您的信息门户账号密码，并关联您自己的手机号，</span>
                            <span>本服务会验证该信息并保存登录态。</span>
                            <span>为防止恶意行为，多次登录失败时将在一小时内的暂停登录服务。</span>
                        </p>
                        <p>
                            <span>本服务将在必要时调整面向使用者的用户政策，包含但不限于修改设置、暂停服务和移除账户。</span>
                        </p>
                    </el-col>
                    <el-col :sm="12" :xs="24">
                        <h3>数据安全</h3>
                        <p>
                            <span>本服务将不会在服务器中保存您的账号密码，这意味着如果本服务不慎丢失您的登录态，签到打卡服务将被暂停。</span>
                            <span>当您成功关闭签到任务时，服务端将释放您的登录态，并解除所有与该账号相关联的附加功能，请谨慎操作。</span>
                        </p>
                        <p>
                            <span>如果您有其他安全顾虑可以与开发者联系以获得帮助</span>
                        </p>
                    </el-col>
                </el-row>
            </div>
            <div class="alert">
                <el-alert :title="closeInfo" type="error" center
                          v-if="closeLogin" :closable="false"></el-alert>
            </div>
            <el-form class="form" ref="form" label-position="left" label-width="60px"
                     v-loading="loading" :rules="rules" :model="formData">
                <el-form-item label="账号" prop="username">
                    <el-input v-model="formData.username" placeholder="旧版信息门户账户" :disabled="closeLogin"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                    <el-input v-model="formData.password" placeholder="旧版信息门户密码"
                              show-password :disabled="closeLogin"></el-input>
                </el-form-item>
                <el-form-item label="昵称" prop="nickname">
                    <el-input v-model="formData.nickname" placeholder="起个帅气昵称" :disabled="closeLogin"></el-input>
                </el-form-item>
                <el-form-item label="手机" prop="phone">
                    <el-input v-model="formData.phone" placeholder="关联您的手机" type="number"
                              :disabled="closeLogin"></el-input>
                </el-form-item>
                <el-form-item label="须知" prop="readme" style="text-align: left">
                    <el-checkbox v-model="formData.readme" :disabled="closeLogin">我已认真阅读并同意服务使用条款</el-checkbox>
                </el-form-item>
                <div class="alert">
                    <el-alert type="warning">
                        <template slot="title">
                            <el-button type="text" @click="passwdTip=true">关于登录密码错误的说明</el-button>
                        </template>
                    </el-alert>
                </div>
                <el-button type="success" @click="check_form" plain :disabled="closeLogin">绑定账号</el-button>
            </el-form>
            <div class="tips">
                <h2>这是什么？</h2>
                <p>懂的都懂<br/>
                    不懂的我也不解释<br/>
                    这里水很深，利益牵扯太大<br/>
                    网上大部分内容都已经删除干净了<br/>
                    懂的人都是自己悟的<br/>
                    别不懂装懂<br/>
                    懂了吗<br/>
                </p>
                <h2>这安全吗？</h2>
                <p>不存储你的密码<br/>
                    服务使用SSL加密传输<br/>
                    仅在登录时使用账号密码<br/>
                    登录后打卡网站提供的Cookie<br/>
                    之后每一天都使用Cookie进行签到<br/>
                    Cookie无法用于获取其他信息<br/>
                    而且源码是开源的<br/>
                    接受安全反馈<br/>
                    <a href="https://github.com/wolfbolin/CSU-COVID19-SIGN" target="_blank">Github</a><br/>
                </p>
                <h2>以防万一</h2>
                <p>由于用户量增加<br/>
                    防止突如其来的服务中断<br/>
                    给你本来懒惰的打卡生活增添困扰<br/>
                    所以我建议你加入服务反馈群1158608406<br/>
                    如果出现业务内容的调整与变化<br/>
                    会尽快通知各位自行调整<br/>
                    @全体成员
                </p>
            </div>
            <el-dialog title="关于密码" width="80%" :visible.sync="passwdTip">
                <p>打卡服务密码请使用旧版信网中心密码(改版前后密码不同步)</p>
                <p>多次尝试失败被暂停服务时，可在一个小时后再次尝试登陆</p>
                <p>您可以在官方网站
                <a href="http://ca.its.csu.edu.cn/home/login/215" target="_blank">&lt;点击打开&gt;</a>
                尝试登陆成功后再绑定账号</p>
                <span slot="footer" class="dialog-footer">
                    <el-button type="primary" @click="passwdTip = false">确 定</el-button>
                </span>
            </el-dialog>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Home',
    data() {
        let checkReadme = (rule, value, callback) => {
            if (value) {
                return callback();
            } else {
                return callback("建立羁绊的必要前提");
            }
        };
        return {
            loading: false,
            passwdTip: false,
            passwdAlert: false,
            closeInfo: "",
            closeLogin: false,
            formData: {
                username: "",
                password: "",
                nickname: "",
                readme: "",
                phone: ""
            },
            rules: {
                username: [
                    {required: true, message: '请输入信息门户账户', trigger: 'blur'},
                    {min: 6, max: 14, message: '长度在 6 到 14 个字符', trigger: 'blur'}
                ],
                password: [
                    {required: true, message: '请输入信息门户密码', trigger: 'blur'}
                ],
                nickname: [
                    {required: true, message: '给自己起个名字吧', trigger: 'blur'},
                    {min: 4, max: 16, message: '长度在 4 到 16 个字符', trigger: 'blur'}
                ],
                phone: [
                    {required: true, message: '请输入收信电话号码', trigger: 'blur'},
                    {min: 11, max: 11, message: '长度应为 11 个数字', trigger: 'blur'}
                ],
                readme: [
                    {required: true, validator: checkReadme, trigger: 'change'}
                ]
            }
        }
    },
    methods: {
        check_open: function () {
            this.loading = true;
            let that = this;
            let data_host = this.$store.state.host
            this.$http.get(data_host + `/user/open`)
                .then(function (res) {
                    console.log(res)
                    that.loading = false;
                    if (res.data.status !== 'success') {
                        that.closeLogin = true
                        that.closeInfo = res.data.message
                    }
                })
                .catch(function (res) {
                    that.loading = false;
                    console.log(res);
                })
        },
        check_form: function () {
            this.$refs["form"].validate((valid) => {
                if (valid) {
                    console.log("Check success, submit!");
                    this.user_login();
                } else {
                    console.log('"Check error"');
                    return false;
                }
            });
        },
        user_login: function () {
            this.loading = true;
            let that = this;
            let data_host = this.$store.state.host;
            let http_data = {
                username: this.formData.username,
                password: this.formData.password,
                nickname: this.formData.nickname,
                phone: this.formData.phone
            }
            console.log(http_data)
            this.$http.post(data_host + `/user/login`, http_data)
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
        }
    },
    mounted() {
        this.check_open()
    }
}
</script>

<style lang="scss" scoped>
.wb-home {
    .form {
        width: 90%;
        margin-top: 36px;
        display: inline-block;
        text-align: center;

        .alert {
            .el-button {
                padding: 0;
            }
        }
    }

    .el-date-editor.el-input {
        width: 100%;
    }
}

</style>
