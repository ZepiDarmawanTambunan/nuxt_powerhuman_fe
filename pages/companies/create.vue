<template>
  <section class="py-[200px] flex flex-col items-center justify-center px-4">
    <div class="text-[32px] font-semibold text-dark mb-4">Create Companies</div>
    <form class="w-full card" @submit.prevent="createCompany">
      <div class="form-group">
          <label for="" class="text-grey">Name</label>
          <input type="text" class="input-field" v-model="company.name">
          <div v-if="errors.name" class="text-red-500">{{ errors.name[0] }}</div>
      </div>
      <div class="form-group">
        <label for="" class="text-grey">Logo</label>
        <input type="file" class="input-field" @change="handleLogoChange">
        <div v-if="errors.logo" class="text-red-500">{{ errors.logo[0] }}</div>
        <div class="mt-2" v-if="previewImage">
          <img :src="previewImage" class="w-24 h-24 object-cover rounded">
        </div>
      </div>
      <button type="submit" class="w-full btn btn-primary mt-[14px]">
          Save
      </button>
    </form>
  </section>
</template>

<script>
export default {
  middleware: ['auth'],
  data(){
    return {
      company: {
        name: '',
        logo: null, // Menambahkan properti logo
      },
      errors: {},
      previewImage: null,
    }
  },
  methods:{
    async createCompany() {
      try {
        let formData = new FormData();
        formData.append('name', this.company.name);
        if (this.company.logo) {
          formData.append('logo', this.company.logo);
        }
        const response = await this.$axios.post('/company', formData, {
          headers: {
            'Content-Type': 'multipart/form-data;charset=utf-8;'
          }
        });
        this.$router.push({
          name: 'companies-id',
          params: {
            id: response.data.result.id,
          },
        });
      } catch (error) {
        this.errors = error.response.data.errors;
      }
    },
    handleLogoChange(event) { // Fungsi untuk menangani perubahan pada field logo
      if (event.target.files.length > 0) {
        this.company.logo = event.target.files[0];
        this.previewImage = URL.createObjectURL(event.target.files[0]); // Menampilkan preview image
      } else {
        this.company.logo = null;
        this.previewImage = null;
      }
    }
  }
}
</script>
