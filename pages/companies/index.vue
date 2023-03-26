<template>
  <section class="py-[200px] flex flex-col items-center justify-center px-4">
    <div class="text-[32px] font-semibold text-dark mb-4">Select Companies</div>
    <div class="w-full card">
      <div class="form-group" v-if="companies.length > 0">
        <label for="" class="text-grey">Companies</label>
        <p v-if="$fetchState.pending">Fetching companies...</p>
        <select
        v-else
        v-model="selectedCompany"
          name="companies"
          id=""
          class="appearance-none input-field form-icon-chevron_down"
        >
          <option :value="company.id" v-for="company in companies" :key="company.id">
            {{company.name}}
          </option>
        </select>
      </div>
      <button v-if="companies.length > 0" @click="openCompany" type="button" class="w-full btn btn-primary mt-[14px]">
        Continue
      </button>
      <div v-if="companies.length > 0" class="text-center">or</div>
        <NuxtLink :to="{ name: 'companies-create' }" class="w-full border btn btn-white">
            Create New Company
        </NuxtLink>
    </div>
  </section>
</template>

<script>
export default {
  middleware: ['auth'],
  data(){
    return {
      companies: [],
      selectedCompany: '',
    }
  },
  async fetch(){
    try {
      const {data} = await this.$axios.get('/company?limit=10');
      this.companies = data.result.data;
    } catch (error) {
      console.log(error);
    }
  },
  methods:{
    openCompany(){
      this.$router.push({
        name: 'companies-id',
        params: {
          id: this.selectedCompany,
        },
      });
    }
  }
}
</script>
