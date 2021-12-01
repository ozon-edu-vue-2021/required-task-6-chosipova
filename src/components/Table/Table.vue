<template>
  <div>
    <table class="table">
      <thead>
        <TableHeader
          prop="id"
          sortType="number"
          title="ID"
          @sortData="setSortData"
          @filterData="setFilterData"
        />
        <TableHeader
          prop="email"
          sortType="text"
          title="Email"
          @sortData="setSortData"
          @filterData="setFilterData"
        />
        <TableHeader
          prop="postId"
          sortType="number"
          title="Post ID"
          @sortData="setSortData"
          @filterData="setFilterData"
        />
        <TableHeader
          prop="name"
          sortType="text"
          title="Name"
          @sortData="setSortData"
          @filterData="setFilterData"
        />
      </thead>
      <tbody>
        <tr v-for="item in sortedRows" :key="item.id">
          <td width="10%">{{ item.id }}</td>
          <td width="30%">
            <a :href="`mailto:${item.email}`">{{ item.email }}</a>
          </td>
          <td width="10%">{{ item.postId }}</td>
          <td width="40%">{{ item.name }}</td>
        </tr>
      </tbody>
    </table>
    <Paginator
      v-if="staticPaging"
      :totalPages="totalPages"
      v-on="$listeners"
      :currentPage="currentPage"
    />
  </div>
</template>

<script>
import Paginator from "./Paginator.vue";
import { orderBy } from "lodash/collection";
import TableHeader from "./TableHeader.vue";

export default {
  props: {
    rows: {
      type: Array,
      default: () => [],
    },
    staticPaging: {
      type: Boolean,
      default: false,
    },
    currentPage: {
      type: Number,
      default: 0,
    },
    totalPages: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      sortProp: "",
      sortDirection: "",
      filterData: {
        id: "",
        email: "",
        postId: "",
        name: "",
      },
    };
  },
  computed: {
    sortedRows() {
      let result;
      if (!this.sortProp) {
        result = this.rows;
      }
      result = orderBy(this.rows, [this.sortProp], [this.sortDirection]);
      if (this.filterData) {
        console.log(Object.keys(this.filterData));
        result = result.filter((item) => {
          let findValue = true;
          for (let key of Object.keys(this.filterData)) {
            if (
              String(item[key])
                .toLowerCase()
                .indexOf(this.filterData[key].toLowerCase()) < 0
            ) {
              findValue = false;
              break;
            }
          }
          if (findValue) {
            return item;
          }
        });
      }
      return result;
    },
  },
  components: {
    TableHeader,
    Paginator,
  },
  methods: {
    setSortData(data) {
      this.sortProp = data.prop;
      this.sortDirection = data.direction;
    },
    setFilterData(data) {
      this.filterData[data.prop] = data.text;
    },
  },
};
</script>

<style scoped>
td,
th {
  vertical-align: middle;
  text-align: left;
}
.bi {
  margin-left: 10px;
  cursor: pointer;
}
</style>
