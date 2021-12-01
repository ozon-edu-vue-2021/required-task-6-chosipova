<template>
  <ul class="pagination justify-content-center">
    <li class="page-item" @click="$listeners.getPage(1)">
      <span class="page-link">
        <span aria-hidden="true">&laquo;</span>
      </span>
    </li>
    <li
      class="page-item"
      @click="
        currentPage > 1
          ? $listeners.getPage(currentPage - 1)
          : $listeners.getPage(1)
      "
    >
      <span class="page-link">
        <span aria-hidden="true">&lsaquo;</span>
      </span>
    </li>
    <li
      v-for="number in shownPagesNumbers"
      :key="number"
      :class="isActiveNumber(number)"
      @click="$listeners.getPage(number)"
    >
      <span class="page-link">
        {{ number }}
      </span>
    </li>
    <li
      class="page-item"
      @click="
        currentPage < totalPages
          ? $listeners.getPage(currentPage + 1)
          : $listeners.getPage(totalPages)
      "
    >
      <span class="page-link">
        <span aria-hidden="true">&rsaquo;</span>
      </span>
    </li>
    <li class="page-item" @click="$listeners.getPage(totalPages)">
      <span class="page-link">
        <span aria-hidden="true">&raquo;</span>
      </span>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    totalPages: {
      type: Number,
      default: 0,
    },
    currentPage: {
      type: Number,
      default: 0,
    },
  },
  computed: {
    shownPagesNumbers() {
      if (this.currentPage <= 3) {
        if (this.totalPages < 5) {
          return Array(this.totalPages)
            .fill(null)
            .map((index) => index + 1);
        }
        return [1, 2, 3, 4, 5];
      }

      if (this.currentPage >= this.totalPages - 2) {
        return [
          this.totalPages - 4,
          this.totalPages - 3,
          this.totalPages - 2,
          this.totalPages - 1,
          this.totalPages,
        ];
      }
      return [
        this.currentPage - 2,
        this.currentPage - 1,
        this.currentPage,
        this.currentPage + 1,
        this.currentPage + 2,
      ];
    },
  },
  methods: {
    isActiveNumber(number) {
      return ["page-item", { active: this.currentPage === number }];
    },
    getPage() {
      console.log();
    },
  },
};
</script>

<style scoped>
.page-item {
  cursor: pointer;
  user-select: none;
}
</style>
