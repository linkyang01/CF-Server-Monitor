<template>
  <div id="login-overlay" class="login-overlay">
    <div class="login-container">
      <div class="login-header">
        <div class="login-icon">
          <svg width="44" height="44" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round" class="login-lock-svg">
            <rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect>
            <path d="M7 11V7a5 5 0 0 1 10 0v4"></path>
          </svg>
        </div>
        <h2 class="login-title">{{ trans.adminLogin }}</h2>
        <p class="login-subtitle">{{ trans.enterCredentials }}</p>
      </div>
      <form @submit.prevent="$emit('login')">
        <div v-if="isMultipleMode" class="login-form-group">
          <label class="login-label">{{ trans.apiEndpoint }}</label>
          <select :value="selectedApiIndex" class="login-input" @change="$emit('api-index-change', Number($event.target.value))">
            <option
              v-for="(base, index) in apiBases"
              :key="index"
              :value="index"
            >
              [{{ index }}] {{ base }}
            </option>
          </select>
        </div>
        <div class="login-form-group">
          <label class="login-label">{{ trans.username }}</label>
          <input type="text" name="username" autocomplete="username" v-model="loginForm.username" required class="login-input" placeholder="yanglin">
        </div>
        <div class="login-form-group last">
          <label class="login-label">{{ trans.password }}</label>
          <div class="password-input-wrapper">
            <input :type="passwordVisible.login ? 'text' : 'password'" name="password" autocomplete="current-password" v-model="loginForm.password" required class="login-input">
            <button type="button" class="password-toggle" @click="$emit('toggle-password', 'login')" :title="passwordVisible.login ? '隐藏密码' : '显示密码'">
              <svg v-if="passwordVisible.login" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
                <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"></path>
                <line x1="1" y1="1" x2="23" y2="23"></line>
              </svg>
              <svg v-else width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                <circle cx="12" cy="12" r="3"></circle>
              </svg>
            </button>
          </div>
        </div>
        <div v-if="turnstileSiteKey && (turnstileLoginEnabled || (turnstileEnabled && !turnstileVerified))" class="login-form-group">
          <div id="admin-turnstile-container"></div>
        </div>
        <div v-if="loginError" id="login-error" class="login-error">{{ loginError }}</div>
        <button type="submit" class="login-btn" :disabled="loginLoading">
          <span v-if="loginLoading" class="btn-spinner"></span>
          <span v-else>{{ trans.login }}</span>
        </button>
      </form>
    </div>
    <Footer />
  </div>
</template>

<script setup>
import Footer from '../../../components/Footer.vue'

defineProps({
  trans: { type: Object, required: true },
  isMultipleMode: { type: Boolean, default: false },
  apiBases: { type: Array, default: () => [] },
  selectedApiIndex: { type: Number, default: 0 },
  loginForm: { type: Object, required: true },
  passwordVisible: { type: Object, required: true },
  loginError: { type: String, default: '' },
  loginLoading: { type: Boolean, default: false },
  turnstileSiteKey: { type: String, default: '' },
  turnstileLoginEnabled: { type: Boolean, default: false },
  turnstileEnabled: { type: Boolean, default: false },
  turnstileVerified: { type: Boolean, default: false }
})

defineEmits(['login', 'toggle-password', 'api-index-change'])
</script>

<style scoped>
.login-lock-svg {
  color: var(--accent-green, #10b981);
  display: inline-block;
  vertical-align: middle;
}
.password-toggle {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  background: transparent;
  border: none;
  cursor: pointer;
  color: var(--text-muted, #888);
  transition: color 0.15s ease;
  padding: 0 4px;
}
.password-toggle:hover {
  color: var(--text-normal, #eee);
}
.btn-spinner {
  display: inline-block;
  width: 16px;
  height: 16px;
  border: 2px solid rgba(255, 255, 255, 0.35);
  border-top-color: #ffffff;
  border-radius: 50%;
  animation: login-spin 0.75s linear infinite;
  vertical-align: middle;
}
@keyframes login-spin {
  to { transform: rotate(360deg); }
}
</style>
