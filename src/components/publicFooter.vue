<template>
  <footer>
    © {{webInfo.webName}} <a href="http://www.sunlue.com">上略互动网络技术有限公司</a>  |    {{webInfo.siteIcp}}  |  备案信息: {{webInfo.siteGwa}}
  </footer>
</template>

<script>
  import axios from "axios";
    export default {
        data(){
            return{
                webInfo:{}
            }
        },
        mounted:function(){
            this.getWebIntro()
        },
        methods :{
            getWebIntro:function(){
                let self = this
                axios.get('../../static/data/webIntro.json').then(function (res) {
                    self.webInfo = res.data
                })
            }
        }
    }
</script>

