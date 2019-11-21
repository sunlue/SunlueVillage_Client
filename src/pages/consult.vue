<template>
  <div id="consult-box" class="inside-box">
    <publicHead :show="show" :rightBtn="false"/>
    <section class="consult-box">
      <div class="content-box">
        <p class="title">咨询</p>
        <div class="container-fluid">
          <div class="row">
            <div class="col-lg-8">
              <div class="clearfix left-list">
                <span class="list-name">咨询类别</span>
                <select class="consultSelect custom-select" name="" id="" v-model="type">
                  <option value="0">乡镇村落</option>
                  <option value="1">乡镇文化</option>
                  <option value="2">乡镇发展</option>
                </select>
              </div>
              <div class="clearfix left-list">
                <span class="list-name">咨询内容</span>
                <textarea placeholder="请输入咨询内容..." class="form-control custom-textarea" aria-label="With textarea"
                          rows="6" v-model="details">
                </textarea>
              </div>
              <div class="clearfix left-list">
                <span class="list-name">联系方式</span>
                <input class="form-control phone-input" type="text" placeholder="请输入手机号" v-model="phone">
              </div>
              <div class="clearfix left-list">
                <span class="list-name">验证</span>
                <input class="form-control code-input" type="text" placeholder="验证码" v-model="code">
                <p class="code-img">
                  <img src="../../static/images/code.png" alt="">
                </p>
              </div>
              <span class="submit-btn" @click="submitData">
                提交
              </span>
            </div>
            <div class="col-lg-4">
              <div class="common-box">
                <p class="name">常见问题</p>

                <p class="common-list" :key="index" v-for="(item,index) in questionArr" @click="showQuestion(index)">
                  {{item.name}}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <b-modal centered id="modalPopover" :title="questionTitle" ok-only>
      <p>
        {{questionValue}}
      </p>
    </b-modal>

    <b-modal id="modal-sm" size="sm" title="提示" ok-only>{{hint}}</b-modal>
    <publicFooter/>
  </div>
</template>

<script>
    import publicHead from "../components/publicHead"
    import publicFooter from "../components/publicFooter"
    import axios from "axios"

    export default {
        data() {
            return {
                show: 4,
                questionTitle: '',
                questionValue: '',
                questionArr: [],
                type: 0,
                details: "",
                phone: "",
                code: "",
                hint: ""
            }
        },
        components: {
            publicHead,
            publicFooter
        },
        mounted() {
            this.getQuestion();
        },
        methods: {
            //获取热门问题
            getQuestion() {
                axios.get("../../static/data/question.json").then(res => {
                    this.questionArr = res.data
                })
            },
            // 显示问题答案
            showQuestion(index) {
                this.questionTitle = this.questionArr[index].name
                this.questionValue = this.questionArr[index].content
                this.$bvModal.show("modalPopover");
            },
            //提交数据
            submitData() {
                const rule = /^((13[0-9])|(14[5,7])|(15[0-3,5-9])|(17[0,3,5-8])|(18[0-9])|166|198|199|(147))\d{8}$/
                if (this.type === '') {
                    this.hint = "请选择咨询类别";
                    this.$bvModal.show("modal-sm");
                    return
                }
                if (this.details === '') {
                    this.hint = "请输入咨询内容";
                    this.$bvModal.show("modal-sm");
                    return
                }
                if (this.phone === '') {
                    this.hint = "请输入联系电话";
                    this.$bvModal.show("modal-sm");
                    return
                }
                if(!rule.test(this.phone)){
                    this.hint = "请输入正确的手机号码";
                    this.$bvModal.show("modal-sm");
                    return
                }
                if (this.code === '') {
                    this.hint = "请输入验证码";
                    this.$bvModal.show("modal-sm");
                    return
                }

                this.hint = "提交成功";
                this.$bvModal.show("modal-sm");
            }
        }
    }
</script>

<style src="../assets/css/consult.css" scoped></style>
<style>
  #consult-box .broadside-box {
    display: none;
  }

  .modal-footer {
    background-color: #fff;
  }
</style>
