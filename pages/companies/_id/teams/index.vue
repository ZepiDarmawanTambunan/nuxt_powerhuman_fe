<template>
  <!-- Main Content -->
  <div class="lg:pr-[70px] py-[50px] lg:ml-[320px] xl:ml-[365px] px-4 lg:pl-0">
    <!-- Top Section -->
    <section
      class="flex flex-col flex-wrap justify-between gap-6 md:items-center md:flex-row"
    >
      <div class="flex items-center justify-between gap-4">
        <a href="#" id="toggleOpenSidebar" class="lg:hidden">
          <svg
            class="w-6 h-6 text-dark"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 6h16M4 12h16M4 18h7"
            ></path>
          </svg>
        </a>
        <div class="text-[32px] font-semibold text-dark">My Teams</div>
      </div>
      <div class="flex items-center gap-4">
        <form class="shrink md:w-[516px] w-full">
          <input
            type="text"
            v-model="searchInput"
            @input="handleSearch"
            class="input-field !outline-none !border-none italic form-icon-search ring-indigo-200 focus:ring-2 transition-all duration-300 w-full"
            placeholder="Search people, team, project"
          />
        </form>
        <a
          href="#"
          class="flex-none w-[46px] h-[46px] bg-white rounded-full p-[11px] relative notification-dot"
        >
          <img src="/assets/svgs/ic-bell.svg" alt="" />
        </a>
      </div>
    </section>

    <section class="pt-[50px]">
      <!-- Section Header -->
      <div class="mb-[30px]">
        <div
          class="flex flex-col justify-between gap-6 sm:items-center sm:flex-row"
        >
          <div>
            <div class="text-xl font-medium text-dark">Available</div>
            <p class="text-grey">Empower company</p>
          </div>
          <NuxtLink
            :to="{ name: 'companies-id-teams-create' }"
            class="btn btn-primary"
            >Build New Team</NuxtLink
          >
        </div>
      </div>

      <!-- ITEMS -->
      <div
        class="grid grid-cols-2 gap-4 sm:grid-cols-3 lg:grid-cols-4 xl:gap-10 lg:gap-3"
      >
        <p v-if="$fetchState.pending">Fetching mountains...</p>
        <div
          v-else
          class="items-center card py-6 md:!py-10 md:!px-[38px] !gap-y-0"
          v-for="team in filteredTeams"
          :key="team.id"
        >
          <a
            href="#"
            class="absolute inset-0 focus:ring-2 ring-primary rounded-[26px]"
          ></a>
          <img :src="team.icon" alt="" />
          <div class="mt-6 mb-1 font-semibold text-center text-dark">
            {{ team.name }}
          </div>
          <p class="text-center text-grey">{{ team.employees_count }} People</p>
        </div>
      </div>

      <div v-if="teams.length < 1 && !$fetchState.pending" class="text-center">Empty Data</div>

      <!-- PAGINATION -->
      <div class="flex justify-center mt-8">
        <ul class="flex flex-row">
          <li v-if="currentPage > 1">
            <a
              class="bg-gray-200 text-gray-500 hover:text-white hover:bg-blue-500 rounded-l-lg px-3 py-2"
              href="#"
              @click.prevent="changePage(currentPage - 1)"
              aria-label="Previous"
            >
              <span aria-hidden="true">&laquo;</span>
            </a>
          </li>
          <li v-for="page in totalPages" :key="page">
            <a
              :class="
                currentPage === page
                  ? 'bg-blue-500 text-white'
                  : 'bg-gray-200 text-gray-700'
              "
              class="hover:bg-blue-500 hover:text-white px-3 py-2 mx-1"
              href="#"
              @click.prevent="changePage(page)"
              >{{ page }}</a
            >
          </li>
          <li v-if="currentPage < totalPages">
            <a
              class="bg-gray-200 text-gray-500 hover:text-white hover:bg-blue-500 rounded-r-lg px-3 py-2"
              href="#"
              @click.prevent="changePage(currentPage + 1)"
              aria-label="Next"
            >
              <span aria-hidden="true">&raquo;</span>
            </a>
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  layout: "dashboard",
  middleware: ["auth"],
  data() {
    return {
      teams: [],
      searchInput: "",
      pagination: {
        currentPage: 1,
        totalPages: 1,
      },
    };
  },
  async fetch() {
    await this.fetchTeams();
  },
  methods: {
    async fetchTeams(page = 1) {
      try {
        const { data } = await this.$axios.get("/team", {
          params: {
            company_id: this.$route.params.id,
            limit: 10,
            page,
          },
        });
        this.teams = data.result.data;
        this.pagination.currentPage = data.result.current_page;
        this.pagination.totalPages = data.result.last_page;
      } catch (error) {
        console.log(error);
      }
    },
    async handleSearch() {
      if (this.searchInput) {
        try {
          const { data } = await this.$axios.get("/role", {
            params: {
              company_id: this.$route.params.id,
              limit: 100,
              name: this.searchInput,
            },
          });
          this.teams = data.result.data;
          this.pagination.currentPage = 1;
          this.pagination.totalPages = 1;
        } catch (error) {
          console.log(error);
        }
      } else {
        await this.fetchTeams();
      }
    },
    async changePage(page) {
      if (page < 1 || page > this.pagination.totalPages) {
        return;
      }
      await this.fetchTeams(page);
    },
  },
  computed: {
    filteredTeams() {
      if (this.searchInput) {
        return this.teams.filter((team) =>
          team.name.toLowerCase().includes(this.searchInput.toLowerCase())
        );
      }
      return this.teams;
    },
    currentPage() {
      return this.pagination.currentPage;
    },
    totalPages() {
      return this.pagination.totalPages;
    },
  },
};
</script>
