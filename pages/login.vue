<template>
  <section class="py-[50px] flex flex-col items-center justify-center px-4">
    <img src="/assets/svgs/logo-type.svg" alt="" />
    <div class="text-[32px] font-semibold text-dark mt-[70px]">Sign In</div>
    <p class="mt-4 text-base leading-7 text-center mb-[50px] text-grey">
      Manage your employees to achieve <br />
      a bigger goals for your company
    </p>
    <form class="w-full card" @submit.prevent="userLogin">
      <div class="form-group">
        <label for="" class="text-grey">Email Address</label>
        <input type="email" class="input-field" v-model="login.email" />
      </div>
      <div class="form-group">
        <label for="" class="text-grey">Password</label>
        <input type="password" class="input-field" v-model="login.password" />
      </div>
      <button type="submit" class="w-full btn btn-primary mt-[14px]">
            Sign In
            <div v-if="isLoading" class="ml-auto inline-block h-4 w-4 animate-spin rounded-full border-4 border-solid border-current border-r-transparent align-[-0.125em] motion-reduce:animate-[spin_1.5s_linear_infinite]"
            role="status">
            </div>
      </button>
    </form>
    <div v-if="showToast.active" :class="{ 'bg-green-500': isActive, 'bg-red-500': !isActive }" class="fixed top-4 right-4 text-white px-4 py-2 rounded-lg shadow-md" role="alert">
      {{ showToast.message }}
    </div>
  </section>
</template>

<script>
export default {
  middleware: 'auth',
  data() {
    return {
      isLoading: false, // tambahkan variabel isLoading
      showToast: {
        active: false,
        isSuccess: false,
        message: '',
      },
      login: {
        email: '',
        password: ''
      }
    }
  },
  methods: {
    async userLogin() {
      try {
        this.isLoading = true; // ubah isLoading menjadi true saat userLogin dijalankan
        let response = await this.$auth.loginWith('local', { data: this.login })
        .then(res => {
          console.log(res);
          this.isLoading = false;
          setTimeout(() => {
            this.showToast.active = true;
            this.showToast.isSuccess = true;
            this.showToast.message = '';
          }, 3000);
        });
        } catch (err) {
        this.loading = false;
        this.showToast.active = true;
        this.showToast.message = err.message;
        setTimeout(() => {
          this.showToast.active = false;
          this.showToast.message = '';
        }, 3000);
      }finally {
        console.log('BERHASIL');
        this.isLoading = false; // ubah isLoading menjadi false setelah selesai
      }
    }
  }
}
</script>
