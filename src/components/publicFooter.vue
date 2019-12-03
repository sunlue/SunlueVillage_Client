<template>
  <footer>
    Copyright © 涪城区数字乡村信息管理平台 All Rights Reserved.Powered by<a target="_blank" href="http://www.sunlue.com">上略互动</a> | {{questionArr.icp_copyright}} |
    {{questionArr.police_copyright}}
  </footer>
</template>

<script>
    import axios from "axios";

    export default {
        data() {
            return {
                webInfo: {},
                type: "setting",
                questionArr:[]
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

