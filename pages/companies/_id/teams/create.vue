<template>
    <section class="py-[70px] flex flex-col items-center justify-center px-4">
        <div class="text-[32px] font-semibold text-dark">
            Build New Team
        </div>
        <p class="mt-4 text-base leading-7 text-center mb-[50px] text-grey">
            Team that can bring your company <br>
            growing bigger and bigger
        </p>
        <form class="w-full card" @submit.prevent="createTeam">
            <div class="mb-[2px] mx-auto">
                <img src="/assets/svgs/ric-box.svg" alt="">
            </div>
            <div class="form-group">
                <label for="" class="text-grey">Email Address</label>
                <input type="email" class="input-field disabled:bg-grey disabled:outline-none"
                    :value="this.$auth.user.email" disabled>
            </div>
            <div class="form-group">
                <label for="" class="text-grey">Team Name</label>
                <input type="text" class="input-field" v-model="team.name">
                <div v-if="errors.name" class="text-red-500">{{ errors.name[0] }}</div>
            </div>
            <div class="form-group">
                <label for="" class="text-grey">Status</label>
                <select name="" id="" class="appearance-none input-field form-icon-chevron_down">
                    <option value="" selected>Active</option>
                    <option value="">Inactive</option>
                </select>
            </div>
            <div class="form-group">
              <label for="icon" class="text-grey">Icon</label>
              <input type="file" class="input-field" @change="handleIconChange">
              <div v-if="errors.icon" class="text-red-500">{{ errors.icon[0] }}</div>
              <div class="mt-2" v-if="previewImage">
                <img :src="previewImage" class="w-24 h-24 object-cover rounded">
              </div>
            </div>
            <button type="submit" class="w-full btn btn-primary mt-[14px]">
                Continue
            </button>
        </form>
    </section>
</template>

<script>
export default {
  layout: 'form',
  middleware: ['auth'],
  data() {
    return {
      team: {
        name: '',
        icon: null,
        company_id: this.$route.params.id,
      },
      errors: {},
      previewImage: null,
    }
  },
  methods: {
    async createTeam() {
      try {
        let formData = new FormData();
        formData.append('name', this.team.name);
        formData.append('company_id', this.team.company_id);
        if (this.team.icon) {
          formData.append('icon', this.team.icon);
        }
        const response = await this.$axios.post('/team', formData, {
          headers: {
            'Content-Type': 'multipart/form-data;charset=utf-8;'
          }
        });
        console.log(response);
        this.$router.push({name: 'companies-id-teams'});
      } catch (error) {
        this.errors = error.response.data.errors;
      }
    },
    handleIconChange(event) {
      if (event.target.files.length > 0) {
        this.team.icon = event.target.files[0];
        this.previewImage = URL.createObjectURL(event.target.files[0]);
      } else {
        this.team.icon = null;
        this.previewImage = null;
      }
    }
  }
}
</script>
