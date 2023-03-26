<template>
  <section class="py-[70px] flex flex-col items-center justify-center px-4">
    <div class="text-[32px] font-semibold text-dark">
      New Role
    </div>
    <p class="mt-4 text-base leading-7 text-center mb-[50px] text-grey">
      Manage your employees to achieve <br />
      a bigger goals for your company
    </p>
    <form class="w-full card" @submit.prevent="createRole">
      <div class="form-group">
        <label for="" class="text-grey">Role Name</label>
        <input type="text" class="input-field" v-model="role.name" />
        <div v-if="errors.name" class="text-red-500">{{ errors.name[0] }}</div>
      </div>
      <div class="form-group">
        <label for="idRes" class="text-grey">Responsibility</label>
        <ul id="listResp" class="flex flex-col gap-4">
          <li class="inline-flex items-center w-full gap-5">
            <input
              type="text"
              id="idRes"
              name="responsibility"
              class="w-full input-field"
              v-model="responsibilities[0]"
            />
            <a href="#" role="button" @click="addResponsibility">
              <img src="/assets/svgs/ric-plus.svg" alt="" />
            </a>
          </li>
          <li v-for="(resp, index) in responsibilities.slice(1)" :key="index" class="inline-flex items-center w-full gap-5">
            <input
              type="text"
              class="w-full input-field"
              v-model="responsibilities[index+1]"
            />
            <a href="#" role="button" @click="removeResponsibility(index+1)">
              <img src="/assets/svgs/ric-close-red.svg" alt="PowerHuman" />
            </a>
          </li>
        </ul>
      </div>
      <button type="submit" class="w-full btn btn-primary mt-[14px]">
        Save Role
      </button>
    </form>
  </section>
</template>

<script>
export default {
  layout: "form",
  middleware: ["auth"],
  data() {
    return {
      role: {
        name: "",
        company_id: this.$route.params.id,
      },
      responsibilities: [''],
      errors: {},
    };
  },
  methods: {
    async createRole() {
      try {
        const response = await this.$axios.post("/role", this.role);
        console.log(response);
        if (response.status === 200 && this.responsibilities.length > 0) {
          for (let i = 0; i < this.responsibilities.length; i++) {
            if(this.responsibilities[i] != ''){
                const payload = {
                  role_id: response.data.result.id,
                  name: this.responsibilities[i],
                };
                const resp = await this.$axios.post("/responsibility", payload);
                console.log(resp);
            }
          }
        }
        this.$router.push({ name: "companies-id-roles" });
      } catch (error) {
        this.errors = error.response.data.errors;
      }
    },
    addResponsibility() {
      this.responsibilities.push("");
    },
    removeResponsibility(index) {
      this.responsibilities.splice(index, 1);
    },
  },
};
</script>
