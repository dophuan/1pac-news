<template>
  <el-dialog
    title="Login"
    :visible.sync="dialogVisible"
    :close-on-click-modal="false"
    @closed="closeForm"
  >
    <el-form
      :model="form"
      ref="loginForm"
      label-width="120px"
    >
      <el-form-item
        label="Email"
        prop="email"
        :rules="[
          {
            required: true,
            message: 'Please enter your email',
            trigger: 'blur',
          },
          {
            type: 'email',
            message: 'Please make sure that your email is correct',
            trigger: 'change',
          },
        ]"
      >
        <el-input
          v-model="form.email"
          placeholder="Email"
        />
      </el-form-item>
      <el-form-item
        label="Password"
        prop="pass"
        :rules="[
          {
            required: true,
            message: 'Please enter your password',
            trigger: 'blur',
          },
        ]"
      >
        <el-input
          type="password"
          v-model="form.pass"
          placeholder="Enter your password"
          autocomplete="off"
        />
      </el-form-item>
      <div
        class="create-account"
        @click="showRegisterForm"
      >
        Create new account
      </div>
      <el-form-item>
        <el-button type="primary" @click="submitForm">Login</el-button>
        <el-button @click="closeForm">Cancel</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<script>
  import { event } from '../../utils/event'
  import { sha256 } from 'js-sha256'

  export default {
    name: 'LoginForm',
    props: {
      listUsers: {
        type: Array,
        required: true,
      },
      dialogVisible: {
        type: Boolean,
        required: true,
        default: false,
      },
    },
    data: () => {
      return {
        form: {}
      }
    },
    mounted() {
      this.form = {
        email: '',
        pass: '',
      }
    },
    methods: {
      submitForm() {
        let result = this.foundUser
        if (result.length === 0) {
          this.$notify.error({
            title: 'Error',
            message: 'Email or password is incorrect. Please try again!',
          })
        } else {
          this.$notify.success({
            title: 'Success',
            message: 'Login success',
          })
          event.emit('dialog.set_login_user', result)
          this.closeForm()
        }
      },
      closeForm() {
        this.$refs['loginForm'].resetFields()
        event.emit('dialog.hide_login_form')
      },
      showRegisterForm() {
        event.emit('dialog.show_register_form')
        this.closeForm()
      },
      filterUser(item) {
        return item.email === this.form.email && item.pass === sha256(this.form.pass)
      }
    },
    computed: {
      foundUser() {
        return this.listUsers.filter(this.filterUser)
      }
    }
  }
</script>

<style lang="scss">
  .create-account {
    width: 100%;
    text-align: right;

    &:hover {
      cursor: pointer;
    }
  }
</style>
