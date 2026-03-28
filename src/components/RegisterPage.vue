<template>
  <div class="register-container">
    <el-card class="register-card" shadow="hover">
      <div class="register-header">
        <h1 class="title">创建账户</h1>
        <p class="subtitle">欢迎加入我们！请填写以下信息完成注册</p>
      </div>

      <el-form
        ref="formRef"
        :model="formData"
        :rules="rules"
        label-position="top"
        class="register-form"
      >
        <el-form-item label="用户名" prop="username">
          <el-input
            v-model="formData.username"
            placeholder="请输入用户名"
            size="large"
            prefix-icon="User"
            clearable
          />
        </el-form-item>

        <el-form-item label="手机号码" prop="phone">
          <el-row :gutter="12">
            <el-col :span="16">
              <el-input
                v-model="formData.phone"
                placeholder="请输入手机号码"
                size="large"
                prefix-icon="Phone"
                clearable
              />
            </el-col>
            <el-col :span="8">
              <el-button
                :loading="phoneCodeLoading"
                :disabled="phoneCodeLoading || phoneCountdown > 0"
                type="primary"
                size="large"
                class="code-btn"
                @click="sendPhoneCode"
              >
                {{ phoneCountdown > 0 ? `${phoneCountdown}s` : '发送验证码' }}
              </el-button>
            </el-col>
          </el-row>
        </el-form-item>

        <el-form-item label="手机验证码" prop="phoneCode">
          <el-input
            v-model="formData.phoneCode"
            placeholder="请输入手机验证码"
            size="large"
            prefix-icon="Lock"
            maxlength="6"
            show-word-limit
          />
        </el-form-item>

        <el-form-item label="邮箱地址" prop="email">
          <el-row :gutter="12">
            <el-col :span="16">
              <el-input
                v-model="formData.email"
                placeholder="请输入邮箱地址"
                size="large"
                prefix-icon="Message"
                clearable
              />
            </el-col>
            <el-col :span="8">
              <el-button
                :loading="emailCodeLoading"
                :disabled="emailCodeLoading || emailCountdown > 0"
                type="primary"
                size="large"
                class="code-btn"
                @click="sendEmailCode"
              >
                {{ emailCountdown > 0 ? `${emailCountdown}s` : '发送验证码' }}
              </el-button>
            </el-col>
          </el-row>
        </el-form-item>

        <el-form-item label="邮箱验证码" prop="emailCode">
          <el-input
            v-model="formData.emailCode"
            placeholder="请输入邮箱验证码"
            size="large"
            prefix-icon="Lock"
            maxlength="6"
            show-word-limit
          />
        </el-form-item>

        <el-form-item label="密码" prop="password">
          <el-input
            v-model="formData.password"
            type="password"
            placeholder="请输入密码（至少6位）"
            size="large"
            prefix-icon="Key"
            show-password
          />
        </el-form-item>

        <el-form-item label="确认密码" prop="confirmPassword">
          <el-input
            v-model="formData.confirmPassword"
            type="password"
            placeholder="请再次输入密码"
            size="large"
            prefix-icon="Key"
            show-password
          />
        </el-form-item>

        <el-form-item>
          <el-button
            type="primary"
            size="large"
            class="submit-btn"
            :loading="submitLoading"
            @click="handleSubmit"
          >
            立即注册
          </el-button>
        </el-form-item>

        <div class="login-link">
          已有账户？<a href="javascript:void(0)">立即登录</a>
        </div>
      </el-form>
    </el-card>
  </div>
</template>

<script setup>
import { ref, reactive, onUnmounted } from 'vue'
import { ElMessage } from 'element-plus'

const formRef = ref(null)
const phoneCodeLoading = ref(false)
const emailCodeLoading = ref(false)
const submitLoading = ref(false)
const phoneCountdown = ref(0)
const emailCountdown = ref(0)

let phoneTimer = null
let emailTimer = null

const formData = reactive({
  username: '',
  phone: '',
  phoneCode: '',
  email: '',
  emailCode: '',
  password: '',
  confirmPassword: ''
})

const validatePhone = (rule, value, callback) => {
  const phoneReg = /^1[3-9]\d{9}$/
  if (!value) {
    callback(new Error('请输入手机号码'))
  } else if (!phoneReg.test(value)) {
    callback(new Error('请输入正确的手机号码'))
  } else {
    callback()
  }
}

const validateEmail = (rule, value, callback) => {
  const emailReg = /^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$/
  if (!value) {
    callback(new Error('请输入邮箱地址'))
  } else if (!emailReg.test(value)) {
    callback(new Error('请输入正确的邮箱地址'))
  } else {
    callback()
  }
}

const validateConfirmPassword = (rule, value, callback) => {
  if (value !== formData.password) {
    callback(new Error('两次输入的密码不一致'))
  } else {
    callback()
  }
}

const rules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 2, max: 20, message: '用户名长度在 2 到 20 个字符', trigger: 'blur' }
  ],
  phone: [
    { validator: validatePhone, trigger: 'blur' }
  ],
  phoneCode: [
    { required: true, message: '请输入手机验证码', trigger: 'blur' },
    { len: 6, message: '验证码长度为6位', trigger: 'blur' }
  ],
  email: [
    { validator: validateEmail, trigger: 'blur' }
  ],
  emailCode: [
    { required: true, message: '请输入邮箱验证码', trigger: 'blur' },
    { len: 6, message: '验证码长度为6位', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, message: '密码至少6位', trigger: 'blur' }
  ],
  confirmPassword: [
    { required: true, message: '请确认密码', trigger: 'blur' },
    { validator: validateConfirmPassword, trigger: 'blur' }
  ]
}

const sendPhoneCode = () => {
  if (!formData.phone) {
    ElMessage.warning('请先输入手机号码')
    return
  }
  
  phoneCodeLoading.value = true
  
  // 模拟发送验证码
  setTimeout(() => {
    phoneCodeLoading.value = false
    ElMessage.success('手机验证码已发送，请查收')
    
    // 开始倒计时
    phoneCountdown.value = 60
    phoneTimer = setInterval(() => {
      phoneCountdown.value--
      if (phoneCountdown.value <= 0) {
        clearInterval(phoneTimer)
        phoneTimer = null
      }
    }, 1000)
  }, 1000)
}

const sendEmailCode = () => {
  if (!formData.email) {
    ElMessage.warning('请先输入邮箱地址')
    return
  }
  
  emailCodeLoading.value = true
  
  // 模拟发送验证码
  setTimeout(() => {
    emailCodeLoading.value = false
    ElMessage.success('邮箱验证码已发送，请查收')
    
    // 开始倒计时
    emailCountdown.value = 60
    emailTimer = setInterval(() => {
      emailCountdown.value--
      if (emailCountdown.value <= 0) {
        clearInterval(emailTimer)
        emailTimer = null
      }
    }, 1000)
  }, 1000)
}

const handleSubmit = async () => {
  if (!formRef.value) return
  
  try {
    await formRef.value.validate()
    
    submitLoading.value = true
    
    // 模拟注册请求
    setTimeout(() => {
      submitLoading.value = false
      ElMessage.success('注册成功！')
      
      // 重置表单
      formRef.value.resetFields()
      
      // 清除倒计时
      if (phoneTimer) {
        clearInterval(phoneTimer)
        phoneTimer = null
        phoneCountdown.value = 0
      }
      if (emailTimer) {
        clearInterval(emailTimer)
        emailTimer = null
        emailCountdown.value = 0
      }
    }, 1500)
  } catch (e) {
    console.log('表单验证失败')
  }
}

onUnmounted(() => {
  if (phoneTimer) clearInterval(phoneTimer)
  if (emailTimer) clearInterval(emailTimer)
})
</script>

<style scoped>
.register-container {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.register-card {
  width: 100%;
  max-width: 500px;
  border-radius: 20px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  overflow: hidden;
}

.register-header {
  text-align: center;
  margin-bottom: 30px;
  padding-top: 20px;
}

.title {
  font-size: 28px;
  font-weight: 700;
  color: #303133;
  margin-bottom: 10px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.subtitle {
  color: #909399;
  font-size: 14px;
  margin: 0;
}

.register-form {
  padding: 0 20px 20px;
}

.code-btn {
  width: 100%;
  border-radius: 8px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
}

.code-btn:hover {
  opacity: 0.9;
}

.code-btn:disabled {
  background: #c0c4cc;
}

.submit-btn {
  width: 100%;
  height: 48px;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  margin-top: 10px;
}

.submit-btn:hover {
  opacity: 0.9;
}

.login-link {
  text-align: center;
  margin-top: 20px;
  color: #909399;
  font-size: 14px;
}

.login-link a {
  color: #667eea;
  text-decoration: none;
  font-weight: 500;
}

.login-link a:hover {
  text-decoration: underline;
}

:deep(.el-input__wrapper) {
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
}

:deep(.el-input__wrapper:hover) {
  box-shadow: 0 2px 12px rgba(102, 126, 234, 0.2);
}

:deep(.el-input.is-focus .el-input__wrapper) {
  box-shadow: 0 0 0 2px rgba(102, 126, 234, 0.2);
}

:deep(.el-form-item__label) {
  font-weight: 500;
  color: #606266;
}
</style>
