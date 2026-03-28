<template>
  <div class="register-container">
    <div class="register-card">
      <div class="register-header">
        <h1>创建账户</h1>
        <p>加入我们，开启精彩旅程</p>
      </div>

      <div class="tab-buttons">
        <button
          :class="['tab-btn', { active: activeTab === 'phone' }]"
          @click="activeTab = 'phone'"
        >
          <svg class="tab-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path>
          </svg>
          手机注册
        </button>
        <button
          :class="['tab-btn', { active: activeTab === 'email' }]"
          @click="activeTab = 'email'"
        >
          <svg class="tab-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path>
            <polyline points="22,6 12,13 2,6"></polyline>
          </svg>
          邮箱注册
        </button>
      </div>

      <form @submit.prevent="handleRegister" class="register-form">
        <div v-if="activeTab === 'phone'" class="form-group">
          <label class="form-label">手机号码</label>
          <div class="input-wrapper">
            <span class="input-prefix">+86</span>
            <input
              v-model="form.phone"
              type="tel"
              placeholder="请输入手机号码"
              class="form-input"
              :class="{ error: errors.phone }"
              maxlength="11"
            />
          </div>
          <span v-if="errors.phone" class="error-message">{{ errors.phone }}</span>
        </div>

        <div v-if="activeTab === 'email'" class="form-group">
          <label class="form-label">邮箱地址</label>
          <div class="input-wrapper">
            <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path>
              <polyline points="22,6 12,13 2,6"></polyline>
            </svg>
            <input
              v-model="form.email"
              type="email"
              placeholder="请输入邮箱地址"
              class="form-input"
              :class="{ error: errors.email }"
            />
          </div>
          <span v-if="errors.email" class="error-message">{{ errors.email }}</span>
        </div>

        <div class="form-group">
          <label class="form-label">验证码</label>
          <div class="input-with-button">
            <div class="input-wrapper">
              <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect>
                <path d="M7 11V7a5 5 0 0 1 10 0v4"></path>
              </svg>
              <input
                v-model="form.code"
                type="text"
                placeholder="请输入验证码"
                class="form-input"
                :class="{ error: errors.code }"
                maxlength="6"
              />
            </div>
            <button
              type="button"
              :class="['code-btn', { disabled: countdown > 0 }]"
              :disabled="countdown > 0"
              @click="sendCode"
            >
              {{ countdown > 0 ? `${countdown}s 后重新发送` : '发送验证码' }}
            </button>
          </div>
          <span v-if="errors.code" class="error-message">{{ errors.code }}</span>
        </div>

        <div class="form-group">
          <label class="form-label">设置密码</label>
          <div class="input-wrapper">
            <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect>
              <path d="M7 11V7a5 5 0 0 1 10 0v4"></path>
            </svg>
            <input
              v-model="form.password"
              :type="showPassword ? 'text' : 'password'"
              placeholder="请设置密码（8-20位，包含字母和数字）"
              class="form-input"
              :class="{ error: errors.password }"
            />
            <button type="button" class="toggle-password" @click="showPassword = !showPassword">
              <svg v-if="showPassword" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"></path>
                <line x1="1" y1="1" x2="23" y2="23"></line>
              </svg>
              <svg v-else viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                <circle cx="12" cy="12" r="3"></circle>
              </svg>
            </button>
          </div>
          <span v-if="errors.password" class="error-message">{{ errors.password }}</span>
        </div>

        <div class="form-group">
          <label class="form-label">确认密码</label>
          <div class="input-wrapper">
            <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect>
              <path d="M7 11V7a5 5 0 0 1 10 0v4"></path>
            </svg>
            <input
              v-model="form.confirmPassword"
              :type="showConfirmPassword ? 'text' : 'password'"
              placeholder="请再次输入密码"
              class="form-input"
              :class="{ error: errors.confirmPassword }"
            />
            <button type="button" class="toggle-password" @click="showConfirmPassword = !showConfirmPassword">
              <svg v-if="showConfirmPassword" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"></path>
                <line x1="1" y1="1" x2="23" y2="23"></line>
              </svg>
              <svg v-else viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                <circle cx="12" cy="12" r="3"></circle>
              </svg>
            </button>
          </div>
          <span v-if="errors.confirmPassword" class="error-message">{{ errors.confirmPassword }}</span>
        </div>

        <div class="form-group">
          <label class="checkbox-label">
            <input type="checkbox" v-model="form.agreed" class="checkbox-input" />
            <span class="checkbox-custom"></span>
            <span class="agreement-text">
              我已阅读并同意
              <a href="#" class="link">《用户协议》</a>
              和
              <a href="#" class="link">《隐私政策》</a>
            </span>
          </label>
          <span v-if="errors.agreed" class="error-message">{{ errors.agreed }}</span>
        </div>

        <button type="submit" class="submit-btn" :disabled="loading">
          <span v-if="loading" class="loading-spinner"></span>
          {{ loading ? '注册中...' : '立即注册' }}
        </button>
      </form>

      <div class="login-link">
        已有账户？
        <a href="#" class="link">立即登录</a>
      </div>
    </div>

    <div v-if="toast.show" :class="['toast', toast.type]" :style="{ top: toast.top + 'px' }">
      <svg v-if="toast.type === 'success'" class="toast-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path>
        <polyline points="22 4 12 14.01 9 11.01"></polyline>
      </svg>
      <svg v-else class="toast-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <circle cx="12" cy="12" r="10"></circle>
        <line x1="15" y1="9" x2="9" y2="15"></line>
        <line x1="9" y1="9" x2="15" y2="15"></line>
      </svg>
      <span class="toast-message">{{ toast.message }}</span>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'

const activeTab = ref('phone')
const showPassword = ref(false)
const showConfirmPassword = ref(false)
const loading = ref(false)
const countdown = ref(0)
let countdownTimer = null

const form = reactive({
  phone: '',
  email: '',
  code: '',
  password: '',
  confirmPassword: '',
  agreed: false
})

const errors = reactive({
  phone: '',
  email: '',
  code: '',
  password: '',
  confirmPassword: '',
  agreed: ''
})

const toast = reactive({
  show: false,
  message: '',
  type: 'success',
  top: 20
})

let toastTimer = null

const showToast = (message, type = 'success') => {
  if (toastTimer) {
    clearTimeout(toastTimer)
  }
  
  toast.message = message
  toast.type = type
  toast.show = true
  toast.top = 20

  toastTimer = setTimeout(() => {
    toast.show = false
  }, 3000)
}

const validatePhone = (phone) => {
  const phoneRegex = /^1[3-9]\d{9}$/
  return phoneRegex.test(phone)
}

const validateEmail = (email) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return emailRegex.test(email)
}

const validatePassword = (password) => {
  const passwordRegex = /^(?=.*[a-zA-Z])(?=.*\d).{8,20}$/
  return passwordRegex.test(password)
}

const sendCode = () => {
  if (activeTab.value === 'phone') {
    if (!form.phone) {
      errors.phone = '请输入手机号码'
      return
    }
    if (!validatePhone(form.phone)) {
      errors.phone = '请输入正确的手机号码'
      return
    }
    errors.phone = ''
  } else {
    if (!form.email) {
      errors.email = '请输入邮箱地址'
      return
    }
    if (!validateEmail(form.email)) {
      errors.email = '请输入正确的邮箱地址'
      return
    }
    errors.email = ''
  }

  countdown.value = 60
  countdownTimer = setInterval(() => {
    countdown.value--
    if (countdown.value <= 0) {
      clearInterval(countdownTimer)
    }
  }, 1000)

  if (activeTab.value === 'phone') {
    showToast('验证码已发送到您的手机，请注意查收', 'success')
  } else {
    showToast('验证码已发送到您的邮箱，请注意查收', 'success')
  }
}

const handleRegister = () => {
  Object.keys(errors).forEach(key => {
    errors[key] = ''
  })

  if (activeTab.value === 'phone') {
    if (!form.phone) {
      errors.phone = '请输入手机号码'
      return
    }
    if (!validatePhone(form.phone)) {
      errors.phone = '请输入正确的手机号码'
      return
    }
  } else {
    if (!form.email) {
      errors.email = '请输入邮箱地址'
      return
    }
    if (!validateEmail(form.email)) {
      errors.email = '请输入正确的邮箱地址'
      return
    }
  }

  if (!form.code) {
    errors.code = '请输入验证码'
    return
  }
  if (form.code.length !== 6) {
    errors.code = '验证码格式不正确'
    return
  }

  if (!form.password) {
    errors.password = '请设置密码'
    return
  }
  if (!validatePassword(form.password)) {
    errors.password = '密码长度为8-20位，且包含字母和数字'
    return
  }

  if (!form.confirmPassword) {
    errors.confirmPassword = '请再次输入密码'
    return
  }
  if (form.password !== form.confirmPassword) {
    errors.confirmPassword = '两次输入的密码不一致'
    return
  }

  if (!form.agreed) {
    errors.agreed = '请阅读并同意用户协议和隐私政策'
    return
  }

  loading.value = true

  setTimeout(() => {
    loading.value = false
    showToast('注册成功！', 'success')
    form.phone = ''
    form.email = ''
    form.code = ''
    form.password = ''
    form.confirmPassword = ''
    form.agreed = false
  }, 2000)
}
</script>

<style scoped>
.register-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  width: 100%;
}

.register-card {
  background: #ffffff;
  border-radius: 24px;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  padding: 48px;
  width: 100%;
  max-width: 520px;
  animation: slideUp 0.5s ease-out;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.register-header {
  text-align: center;
  margin-bottom: 36px;
}

.register-header h1 {
  font-size: 32px;
  font-weight: 700;
  color: #1a1a2e;
  margin-bottom: 8px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.register-header p {
  color: #6b7280;
  font-size: 16px;
}

.tab-buttons {
  display: flex;
  background: #f3f4f6;
  border-radius: 12px;
  padding: 6px;
  margin-bottom: 32px;
}

.tab-btn {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 14px 20px;
  border: none;
  background: transparent;
  color: #6b7280;
  font-size: 15px;
  font-weight: 500;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.tab-btn:hover {
  color: #4b5563;
}

.tab-btn.active {
  background: #ffffff;
  color: #667eea;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.tab-icon {
  width: 18px;
  height: 18px;
}

.register-form {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.form-label {
  font-size: 14px;
  font-weight: 500;
  color: #374151;
}

.input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.input-prefix {
  position: absolute;
  left: 16px;
  color: #6b7280;
  font-weight: 500;
  font-size: 15px;
}

.input-icon {
  position: absolute;
  left: 16px;
  width: 20px;
  height: 20px;
  color: #9ca3af;
}

.form-input {
  width: 100%;
  padding: 16px 16px 16px 60px;
  border: 2px solid #e5e7eb;
  border-radius: 12px;
  font-size: 15px;
  color: #1a1a2e;
  transition: all 0.3s ease;
  background: #f9fafb;
}

.form-input:focus {
  outline: none;
  border-color: #667eea;
  background: #ffffff;
  box-shadow: 0 0 0 4px rgba(102, 126, 234, 0.1);
}

.form-input.error {
  border-color: #ef4444;
  background: #fef2f2;
}

.form-input.error:focus {
  box-shadow: 0 0 0 4px rgba(239, 68, 68, 0.1);
}

.form-input::placeholder {
  color: #9ca3af;
}

.toggle-password {
  position: absolute;
  right: 16px;
  background: none;
  border: none;
  cursor: pointer;
  color: #9ca3af;
  padding: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: color 0.2s ease;
}

.toggle-password:hover {
  color: #6b7280;
}

.toggle-password svg {
  width: 20px;
  height: 20px;
}

.input-with-button {
  display: flex;
  gap: 12px;
}

.input-with-button .input-wrapper {
  flex: 1;
}

.code-btn {
  padding: 0 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: #ffffff;
  border: none;
  border-radius: 12px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  white-space: nowrap;
}

.code-btn:hover:not(.disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(102, 126, 234, 0.4);
}

.code-btn.disabled {
  background: #d1d5db;
  cursor: not-allowed;
  opacity: 0.8;
}

.error-message {
  color: #ef4444;
  font-size: 13px;
  font-weight: 400;
}

.checkbox-label {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  cursor: pointer;
  user-select: none;
}

.checkbox-input {
  display: none;
}

.checkbox-custom {
  width: 20px;
  height: 20px;
  border: 2px solid #d1d5db;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
  flex-shrink: 0;
  margin-top: 2px;
}

.checkbox-input:checked + .checkbox-custom {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-color: #667eea;
}

.checkbox-input:checked + .checkbox-custom::after {
  content: '';
  width: 12px;
  height: 12px;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 24 24' fill='none' stroke='white' stroke-width='3'%3E%3Cpolyline points='20 6 9 17 4 12'%3E%3C/polyline%3E%3C/svg%3E");
  background-size: contain;
  background-repeat: no-repeat;
}

.agreement-text {
  font-size: 14px;
  color: #6b7280;
  line-height: 1.6;
}

.link {
  color: #667eea;
  text-decoration: none;
  font-weight: 500;
  transition: color 0.2s ease;
}

.link:hover {
  color: #764ba2;
  text-decoration: underline;
}

.submit-btn {
  margin-top: 8px;
  padding: 18px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: #ffffff;
  border: none;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.submit-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 12px 28px rgba(102, 126, 234, 0.4);
}

.submit-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.loading-spinner {
  width: 20px;
  height: 20px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-top-color: #ffffff;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.login-link {
  text-align: center;
  margin-top: 28px;
  padding-top: 24px;
  border-top: 1px solid #e5e7eb;
  color: #6b7280;
  font-size: 15px;
}

.toast {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 14px 24px;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.15);
  z-index: 1000;
  animation: toastSlide 0.3s ease-out;
  min-width: 320px;
}

@keyframes toastSlide {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
}

.toast.success {
  background: #10b981;
  color: #ffffff;
}

.toast.error {
  background: #ef4444;
  color: #ffffff;
}

.toast-icon {
  width: 20px;
  height: 20px;
  flex-shrink: 0;
}

.toast-message {
  font-size: 15px;
  font-weight: 500;
}

@media (max-width: 640px) {
  .register-card {
    padding: 32px 24px;
    margin: 20px;
  }

  .register-header h1 {
    font-size: 28px;
  }

  .input-with-button {
    flex-direction: column;
  }

  .code-btn {
    padding: 14px;
  }
}
</style>
