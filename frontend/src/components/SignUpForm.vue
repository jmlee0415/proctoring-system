<template>
  <div class="container">
    <div class="container pt-6 pb-6 pl-4 pr-4">
      <div class="box">
        <form @submit.prevent="submitForm()" class="form">
          <br />
          <b-field label="ID" type="" message="" align="left">
            <b-input
              v-model="username"
              placeholder="xxxx@xxxx.xxx"
              maxlength="30"
              size="is-medium"
            ></b-input>
          </b-field>
          <br />
          <b-field label="비밀번호" type="" message="" align="left">
            <b-input
              v-model="password"
              type="password"
              placeholder="비밀번호"
              maxlength="30"
              size="is-medium"
              password-reveal
            >
            </b-input>
          </b-field>
          <br />
          <b-field label="이름" type="" message="" align="left">
            <b-input
              v-model="userRealname"
              placeholder="이름"
              maxlength="30"
              size="is-medium"
            ></b-input>
          </b-field>
          <br />
          <b-field label="핸드폰 번호" type="" align="left">
            <b-input
              v-model="phoneNumber"
              placeholder="01x-xxxx-xxxx"
              maxlength="30"
              size="is-medium"
            ></b-input>
          </b-field>
          <br />
          <!-- 파일 선택 및 업로드 -->
          <b-field label="프로필 사진" align="left">
            <input
              type="file"
              id="file"
              ref="file"
              v-on:change="handleFileUpload()"
            />
          </b-field>
          <b-field label="권한 설정" type="" message="" align="left">
            <div class="block">
              <b-radio
                v-model="role"
                name="instructor"
                native-value="instructor"
              >
                강사
              </b-radio>
              <b-radio v-model="role" name="student" native-value="student">
                학생
              </b-radio>
            </div>
          </b-field>
          <br />
          <b-button @click="submitForm()" class="is-primary">
            회원 가입
          </b-button>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import { validateEmail } from "@/utils/validation";
import { validatePhoneNumber } from "@/utils/validation";
import axios from "axios";
export default {
  name: "SignUpForm",
  data() {
    return {
      username: "",
      password: "",
      userRealname: "",
      phoneNumber: "",
      userImage: "",
      role: "",
      memberagree: false
    };
  },
  computed: {
    isIdValid() {
      return validateEmail(this.username); //id가 이메일 형식이 맞는지 체크
    },
    isPhoneNumberValid() {
      return validatePhoneNumber(this.phoneNumber); //phonenumber가 핸드폰 번호 형식이 맞는지 체크
    }
  },
  methods: {
    submitForm() {
      let formData = new FormData();
      formData.append("username", this.username);
      formData.append("password", this.password);
      formData.append("userRealname", this.userRealname);
      formData.append("phoneNumber", this.phoneNumber);
      formData.append("file", this.file);
      formData.append("role", this.role);
      axios
        .post("http://localhost:8000/user/signup", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
            "Access-Control-Allow-Origin": "*"
          }
        })
        .then(response => {
          this.success();
          console.log(response.data);
          this.$router.push({ name: "Sign In" });
        })
        .catch(error => {
          this.danger();
          console.log(error);
        })
        .finally(() => {
          this.initForm();
        });
    },
    initForm() {
      this.username = "";
      this.password = "";
      this.userRealName = "";
      this.phoneNumber = "";
      this.userImage = "";
      this.role = "";
    },
    handleFileUpload() {
      this.file = this.$refs.file.files[0];
    },
    success() {
      this.$buefy.notification.open({
        message: "회원가입에 성공하셨습니다.",
        type: "is-success",
        position: "is-bottom-right"
      });
    },
    danger() {
      this.$buefy.notification.open({
        message: `회원 가입을 다시 시도해 주세요.`,
        type: "is-danger",
        position: "is-bottom-right"
      });
    }
  }
};
</script>
