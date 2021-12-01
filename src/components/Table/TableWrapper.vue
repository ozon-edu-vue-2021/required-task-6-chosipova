<template>
  <div class="container text-start">
    <div class="mb-4">
      <div class="form-check form-switch">
        <input
          class="form-check-input"
          type="checkbox"
          id="paginator"
          :checked="staticPaging"
          @change="switchPaging"
        />
        <label class="form-check-label" for="paginator">Пагинация</label>
      </div>
      <div class="form-check form-switch">
        <input
          class="form-check-input"
          type="checkbox"
          id="scroll"
          :checked="infinityScroll"
          @change="switchScroll"
        />
        <label class="form-check-label" for="scroll">Бесконечный скролл</label>
      </div>
    </div>
    <h4 v-if="error">{{ errorMessage }}</h4>
    <div v-else>
      <div
        class="spinner-border text-primary text-center"
        role="status"
        v-show="loading"
      >
        <span class="visually-hidden">Loading...</span>
      </div>
      <Table
        v-show="!loading"
        :rows="rows"
        :staticPaging="staticPaging"
        :totalPages="totalPages"
        :currentPage="currentPage"
        @getPage="getPage"
      />
    </div>
  </div>
</template>

<script>
import Table from "./Table.vue";
//import Column from "./Column.vue";

export default {
  components: {
    Table,
    //Column,
  },
  created() {
    this.blockingPromise = this.getPage(1);
  },
  data() {
    return {
      rows: [],
      currentPage: 1,
      staticPaging: false,
      infinityScroll: false,
      paginationLimitPages: 10,
      totalPages: 50,
      error: false,
      errorMessage: "",
    };
  },
  computed: {
    scrollLimitPages() {
      let lineValue = (window.outerHeight + 300) / 40;
      return lineValue - (lineValue % 10);
    },
    loading() {
      return !this.rows.length;
    },
  },
  methods: {
    async getPage(number) {
      try {
        this.clearErrorData();
        this.blockingPromise && (await this.blockingPromise);
        let response;
        if (this.staticPaging) {
          response = await fetch(
            `https://jsonplaceholder.typicode.com/comments?_page=${number}&_limit=${this.paginationLimitPages}`
          );
          this.rows = await response.json();
          this.currentPage = number;
        } else if (this.infinityScroll) {
          response = await fetch(
            `https://jsonplaceholder.typicode.com/comments?_page=${number}&_limit=${this.scrollLimitPages}`
          );
          this.rows = this.rows.concat(await response.json());
          this.currentPage++;
        } else {
          response = await fetch(
            `https://jsonplaceholder.typicode.com/comments`
          );
          this.rows = await response.json();
        }
        if (!response.ok) {
          throw new Error("Что-то пошло не так!");
        }
      } catch (error) {
        this.error = true;
        this.errorMessage = "Не удалось загрузить данные:( Приходите позже.";
      }
    },
    async scrollGetPage() {
      window.onscroll = () => {
        let bottomOfWindow =
          document.documentElement.scrollTop + window.innerHeight ===
          document.documentElement.offsetHeight;
        if (bottomOfWindow) {
          this.getPage(this.currentPage);
        }
      };
    },
    switchPaging() {
      this.staticPaging = !this.staticPaging;
      this.infinityScroll = false;
      this.getPage(1);
    },
    switchScroll() {
      this.infinityScroll = !this.infinityScroll;
      this.staticPaging = false;
      this.getPage(1);
      this.rows = [];
      this.scrollGetPage();
    },
    clearErrorData() {
      this.error = false;
      this.errorMessage = "";
    },
  },
};
</script>

<style scoped>
.spinner-border {
  position: fixed;
  top: 50%;
  left: 50%;
}
</style>
