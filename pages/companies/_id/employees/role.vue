<template>
      <section class="py-[70px] flex flex-col items-center justify-center px-4">
        <div class="text-[32px] font-semibold text-dark">
            Give a Role
        </div>
        <p class="mt-4 text-base leading-7 text-center mb-[50px] text-grey">
            Complete the information to make it more <br>
            easier when introduce to team
        </p>
        <form class="w-full card">
            <div class="flex flex-col items-center mb-[14px]">
                <img src="/assets/images/user-f-1.png" width="70" alt="">
                <div class="mt-6 mb-1 text-lg font-semibold">
                    {{ this.$store.state.employee.name }}
                </div>
                <p class="text-base text-grey">
                    {{ this.$store.state.employee.email }}
                </p>
            </div>
            <div class="form-group">
                <label for="" class="text-grey">Select Role</label>
                <p v-if="$fetchState.pending">Fetching roles...</p>
                <select v-else name="roles" id="" class="appearance-none input-field form-icon-chevron_down" :value="role_id" @change="updateRoleId">
                    <option :value="role.id" v-for="role in roles.data.result.data" :key="role.id">
                        {{ role.name }}
                    </option>
                </select>
            </div>

            <!-- Responsibilities -->
           <section>
                <label for="" class="text-grey"> Responsibilities </label>
                <div class="flex flex-col gap-4 mt-[10px]">
                    <div class="flex items-start md:items-center gap-[6px]" v-for="responsibility in role_responsibilities" :key="responsibility.id">
                        <img src="/assets/svgs/ic-check-circle.svg" alt="" />
                        <span class="text-sm text-dark">
                            {{ responsibility.name }}
                        </span>
                    </div>
                </div>
            </section>
             <NuxtLink :to="{ name: 'companies-id-employees-team' }" class="w-full btn btn-primary mt-[14px]">
                Continue
            </NuxtLink>
        </form>
    </section>
</template>

<script>
export default {
    layout: 'form',
    middleware: 'auth',
    data() {
        return {
            roles: [],
            selectedCompany: '', // boleh dihapus gak dipakai
        }
    },
    async fetch() {
        this.roles = await this.$axios.get('/role', {
            params: {
                company_id: this.$route.params.id,
                limit: 100,
            },
        })
    },
    computed: {
        role_id() {
            return this.$store.state.employee.role_id
        },
        role_responsibilities(){
            return this.$store.state.employee.role_responsibilities;
        }
    },
    methods: {
        fetchResponsibilities() {
            this.$axios.get('/responsibility', {
                params: {
                    role_id: this.$store.state.employee.role_id,
                    limit: 100,
                },
            })
                .then(({ data }) => {
                    this.$store.commit('employee/updateRoleResponsibilities', data.result.data)
                })
        },
        updateRoleId(event) {
            this.$store.commit('employee/updateRoleId', event.target.value)
            this.$store.commit('employee/updateRoleName', event.target.options[event.target.selectedIndex].text) // boleh dihapus gak dipakai
            this.fetchResponsibilities()
        },
    }
}
</script>
