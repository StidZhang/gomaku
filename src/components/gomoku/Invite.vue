<template>
<div class="invite-box">
  <div class="welcome-message">
    <h2>Hello, {{ $store.state.username }}!</h2>
  </div>
  <a-form :form="form" @submit='handleSubmit' class="invite-form">
    <a-row>
      <a-col :span="18">
        <a-form-item>
          <a-input placeholder="Invite a guest!" v-decorator="[
          'guestName',
          { rules: [{ required: true, message: 'Please input a guest name!' }] }
        ]">
            <a-icon slot="prefix" type='user-add' style="color: rgba(0,0,0,.25)" />
          </a-input>
        </a-form-item>
      </a-col>
      <a-col :span="6">
        <a-form-item>
          <a-button type='primary' htmlType="submit">
            Invite
          </a-button>
        </a-form-item>
      </a-col>
    </a-row>
  </a-form>
  <LogoutButton></LogoutButton>
</div>
</template>

<script>
import LogoutButton from '@/components/auth/Logout'

export default {
  beforeCreate() {
    this.form = this.$form.createForm(this)
  },
  components: {
    LogoutButton
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault()
      this.form.validateFields((err, values) => {
        if (!err) {
          var gameConfig = {
            size: 13,
            invite: values.guestName
          }
          this.$socket.emit("gomoku_create", gameConfig)
        }
      })
    }
  }
}
</script>

<style>
.invite-box {
  width: 40%;
  text-align: center;
  margin: auto;
}
</style>
