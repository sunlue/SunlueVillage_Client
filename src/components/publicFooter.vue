<template>
  <footer>
    © 涪城区数字乡村信息管理平台 All rights reserved by:<a target="_blank" href="http://www.sunlue.com">上略互动网络技术有限公司</a> | {{webInfo.icp_copyright}} | 备案信息:
    {{webInfo.police_copyright}}
  </footer>
</template>

<script>
    import axios from "axios";

    export default {
        data() {
            return {
                webInfo: {},
                type: "setting"
            }
        },
        mounted: function () {
            this.getWebIntro()
        },
        methods: {
            getWebIntro: function () {
                // let self = this
                // axios.get('../../static/data/webIntro.json').then(function (res) {
                //     self.webInfo = res.data
                // })
                let apiUrl = this.$config.apiUrl + 'basis/site/read';
                axios.get(apiUrl, {
                    params:{
                        type: this.type
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        this.questionArr = res.data.data.content;
                    }
                })
            }
        }
    }
</script>

