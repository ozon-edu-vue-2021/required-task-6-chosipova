<template>
  <th scope="col" class="position-relative">
    {{ title }}
    <i :class="sortIcon" @click="toggleSort"></i>
    <i :class="filterIcon" @click.stop="toggleFilter"></i>
    <div
      v-click-outside="closeFilter"
      class="position-absolute input-group shadow bg-white rounded mt-2"
      v-show="isFilterActive"
    >
      <input
        class="form-control rounded"
        type="text"
        placeholder="Search..."
        v-model="filterText"
        @input="filter"
        @keydown.enter="closeFilter"
      />
      <div class="input-group-append p-2">
        <i class="bi bi-x-lg" @click="filterClear"></i>
      </div>
    </div>
  </th>
</template>

<script>
import ClickOutside from "vue-click-outside";

export default {
  props: {
    title: {
      type: String,
      default: "",
    },
    prop: {
      type: String,
      default: "",
    },
    sortType: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      sortDirection: "",
      isFilterActive: false,
      filterText: "",
    };
  },
  computed: {
    sortIcon() {
      if (this.sortType === "number") {
        return this.sortDirection === "asc"
          ? "bi bi-sort-numeric-up"
          : "bi bi-sort-numeric-down-alt";
      } else {
        return this.sortDirection === "asc"
          ? "bi bi-sort-alpha-up"
          : "bi bi-sort-alpha-down-alt";
      }
    },
    filterIcon() {
      return this.filterText.length ? "bi bi-funnel-fill" : "bi bi-funnel";
    },
  },
  methods: {
    toggleSort() {
      this.isFilterActive = false;
      this.sortDirection =
        this.sortDirection === "desc" || !this.sortDirection ? "asc" : "desc";
      this.$emit("sortData", {
        prop: this.prop,
        direction: this.sortDirection,
      });
    },
    toggleFilter() {
      this.isFilterActive = !this.isFilterActive;
    },
    filter() {
      this.$emit("filterData", {
        prop: this.prop,
        text: this.filterText,
      });
    },
    closeFilter() {
      this.isFilterActive = false;
    },
    filterClear() {
      this.filterText = "";
      this.filter();
      this.isFilterActive = false;
    },
  },
  directives: {
    ClickOutside,
  },
};
</script>

<style scoped>
i {
  cursor: pointer;
}
th {
  padding: 0.5rem 0.5rem;
}
.input-group {
  width: 15em;
}
</style>
