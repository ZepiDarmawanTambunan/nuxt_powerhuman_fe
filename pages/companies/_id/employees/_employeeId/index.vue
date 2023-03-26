<template>
    <!-- Main Content -->
    <div class="lg:pr-[70px] py-[50px] lg:ml-[320px] xl:ml-[365px] px-4 lg:pl-0">
        <!-- Top Section -->
        <section class="flex flex-col flex-wrap justify-between gap-6 md:items-center md:flex-row">
            <div class="flex items-center justify-between gap-4">
                <a href="#" id="toggleOpenSidebar" class="lg:hidden">
                    <svg class="w-6 h-6 text-dark" fill="none" stroke="currentColor" viewBox="0 0 24 24"
                        xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M4 6h16M4 12h16M4 18h7">
                        </path>
                    </svg>
                </a>
                <div class="text-[32px] font-semibold text-dark">
                    Employees
                </div>
            </div>
            <div class="flex items-center gap-4">
                <form class="shrink md:w-[516px] w-full">
                    <input type="text" class="input-field !outline-none !border-none italic form-icon-search ring-indigo-200
                        focus:ring-2 transition-all duration-300 w-full" placeholder="Search people, team, project">
                </form>
                <a href="#"
                    class="flex-none w-[46px] h-[46px] bg-white rounded-full p-[11px] relative notification-dot">
                    <img src="/assets/svgs/ic-bell.svg" alt="">
                </a>
            </div>
        </section>

        <section class="pt-[50px]">
            <!-- Section Header -->
            <div class="mb-[30px]">
                <div class="flex items-center justify-between gap-6">
                    <div>
                        <div class="text-xl font-medium text-dark">
                            People
                        </div>
                        <p class="text-grey">The rangers</p>
                    </div>
                </div>
            </div>

            <div class="card py-6 md:!py-10 md:!px-[38px] !gap-y-0 animate-pulse">
                <div class="animate-pulse w-14 h-14 rounded-full bg-gray-400">
                    <div class="h-full w-full rounded-full bg-gray-300"></div>
                </div>
                <div class="mt-6 mb-1 font-semibold text-center text-gray-400">{{employee.name}}</div>
                <div class="text-center text-gray-400">Loading...</div>
                <div class="text-blue-700 mt-[30px] underline relative z-20">Loading...</div>
            </div>
        </section>
    </div>
</template>
<script>
export default {
  layout: 'dashboard',
  middleware: ['auth'],
  data(){
    return {
      employee: {},
    }
  },
  async fetch(){
    try {
      const { data } = await this.$axios.get('/employee', {
        params: {
          // 'company_id': this.$route.params.id,
          'id': this.$route.params.employeeId
        }
      });
      this.employee = data.result;
    } catch (error) {
      console.log(error);
    }
  },
}

</script>
