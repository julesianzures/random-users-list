<template>
  <h1>Random Users List</h1>
  <div id="app">
    <div class="table">
      <table>
        <tr>
          <th>GENDER</th>
          <th>NAME</th>
          <th>LOCATION</th>
          <th>EMAIL</th>
        </tr>
        <tr v-for="item in results" v-bind:key="item.id">
          <td>{{ item.gender }}</td>
          <td>
            {{ item.name.title }} {{ item.name.first }} {{ item.name.last }}
          </td>
          <td>
            {{ item.location.street.number }} - {{ item.location.street.name }},
            {{ item.location.city }}, {{ item.location.country }}
          </td>
          <td>{{ item.email }}</td>
        </tr>
      </table>
      <h3 v-show="loadingNext">Loading more...</h3>
      <button @click="loadNext">Load More</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { ref, onMounted, watch } from "vue";

export default {
  name: "UsersList",
  setup() {
    const results = ref([]);
    const _pageNumber = ref(1);

    const loadingNext = ref(false);

    watch(_pageNumber, (value) => {
      loadPage(value);
    });

    onMounted(() => {
      loadPage(_pageNumber.value);
    });

    function loadPage(pageNumber) {
      loadingNext.value = true;
      axios
        .get(
          `https://randomuser.me/api/?exc=login,registered,dob,phone,cell,id,nat,?page=${pageNumber}&results=10`
        )
        .then((response) => {
          results.value.push(...response.data.results);
          console.log(results.value);
          console.log(response.data.results);
        })
        .finally(() => (loadingNext.value = false));
    }

    function loadNext() {
      _pageNumber.value = _pageNumber.value += 1;
    }

    return {
      results,
      loadNext,
      loadingNext,
    };
  },
};
</script>

<style>
table {
  width: 80%;
  border-collapse: collapse;
  margin: 0 auto;
}

table,
td {
  border: 1px solid #ddd;
  padding-top: 10px;
  padding-bottom: 10px;
  padding-left: 10px;
  text-align: left;
  height: 20px;
  vertical-align: bottom;
}

th {
  font-weight: bold;
  padding-top: 10px;
  padding-bottom: 10px;
  padding-left: 10px;
  border: 1px solid #ddd;
  text-align: left;
  height: 20px;
  background-color: #4caf99;
  color: white;
}

tr:nth-child(even) {
  background-color: #f2f2f2;
}

button {
  margin-top: 35px;
}
</style>