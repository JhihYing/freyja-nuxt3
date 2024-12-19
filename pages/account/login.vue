<script setup>
definePageMeta({
  layout: "account",
});

const email = ref("lori.murphy@example.com");
const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/; // 信箱格式正則表達式
const password = ref("zz123123");
const isRemembered = ref(false);
const authToken = useCookie("auth_token");
const isSubmitting = ref(false);

const login = async () => {
  if (isSubmitting.value) return; // 避免重複點擊按鈕

  if (!emailPattern.test(email.value)) {
    alert("請填寫正確的信箱格式");
    return;
  }

  isSubmitting.value = true;

  try {
    const res = await $fetch(
      "https://freyja-g9hs.onrender.com/api/v1/user/login",
      {
        method: "POST",
        body: {
          email: email.value,
          password: password.value,
        },
      }
    );

    if (res.status) {
      authToken.value = res.token; // 將 token 存進 cookie

      if (isRemembered.value) {
        authToken.maxAge = 30 * 24 * 60 * 60; // 設定 cookie 存活 30 天
      }
      alert("登入成功！");
    }
  } catch (error) {
    const errorMessage = error.data?.message || "登入失敗，請稍後再試！";
    alert(`登入失敗：${errorMessage}`);
  } finally {
    isSubmitting.value = false;
  }
};
</script>
<template>
  <div class="px-5 px-md-0">
    <div class="mb-10">
      <p class="mb-2 text-primary-100 fs-8 fs-md-7 fw-bold">
        享樂酒店，誠摯歡迎
      </p>
      <h1 class="text-neutral-0 fw-bold">立即開始旅程</h1>
    </div>

    <form class="mb-10">
      <div class="mb-4 fs-8 fs-md-7">
        <label class="mb-2 text-neutral-0 fw-bold" for="email">
          電子信箱
        </label>
        <input
          id="email"
          class="form-control p-4 text-neutral-100 fw-medium border-neutral-40"
          v-model.trim="email"
          placeholder="請輸入信箱"
          type="email"
        />
      </div>
      <div class="mb-4 fs-8 fs-md-7">
        <label class="mb-2 text-neutral-0 fw-bold" for="password"> 密碼 </label>
        <input
          id="password"
          class="form-control p-4 text-neutral-100 fw-medium border-neutral-40"
          v-model.trim="password"
          placeholder="請輸入密碼"
          type="password"
        />
      </div>
      <div
        class="d-flex justify-content-between align-items-center mb-10 fs-8 fs-md-7"
      >
        <div class="form-check d-flex align-items-end gap-2 text-neutral-0">
          <input
            v-model="isRemembered"
            id="remember"
            class="form-check-input"
            type="checkbox"
            value=""
          />
          <label class="form-check-label fw-bold" for="remember">
            記住帳號
          </label>
        </div>
        <button
          class="text-primary-100 fw-bold text-decoration-underline bg-transparent border-0"
          type="button"
        >
          忘記密碼？
        </button>
      </div>
      <button
        :class="{
          'btn-neutral-40 text-neutral-60': !email || !password,
        }"
        :disabled="!email || !password"
        class="btn btn-primary-100 w-100 py-4 text-neutral-0 fw-bold"
        type="button"
        @click="login"
      >
        會員登入
      </button>
    </form>

    <p class="mb-0 fs-8 fs-md-7">
      <span class="me-2 text-neutral-0 fw-medium">沒有會員嗎？</span>
      <NuxtLink
        to="/account/signup"
        class="text-primary-100 fw-bold text-decoration-underline bg-transparent border-0"
      >
        <span>前往註冊</span>
      </NuxtLink>
    </p>
  </div>
</template>
<style lang="scss" scoped>
@import "bootstrap/scss/mixins/breakpoints";

$grid-breakpoints: (
  xs: 0,
  sm: 576px,
  md: 768px,
  lg: 992px,
  xl: 1200px,
  xxl: 1400px,
  xxxl: 1537px,
);

input[type="password"] {
  font: small-caption;
  font-size: 1.5rem;
}

input::placeholder {
  color: #909090;
  font-size: 1rem;
  font-weight: 500;

  @include media-breakpoint-down(md) {
    font-size: 14px;
  }
}

.form-check-input {
  width: 1.5rem;
  height: 1.5rem;
}

.form-check-input:checked {
  background-color: #bf9d7d;
  border-color: #bf9d7d;
}
</style>
