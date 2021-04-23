<template>
  <div class="main">
    <h3>Список пользователей</h3>
    <label for="fio">ФИО</label>
    <input type="text" v-model="fio" id="fio" @input="filter" />
    <label for="active">Активен</label
    ><input
      type="checkbox"
      id="active"
      v-model="active"
      @change="filter"
    />
    <VueCtkDateTimePicker
      input-class="form-control"
      v-model="active_time_start"
      name="active_time_start"
      label="Начало периода"
      hint="Введите начало периода, дату и время"
      locale="ru"
    >
    </VueCtkDateTimePicker>
    <VueCtkDateTimePicker
      input-class="form-control"
      v-model="active_time_end"
      name="active_time_end"
      label="Конец периода"
      hint="Введите конец периода, дату и время"
      locale="ru"
      @change="changeDate"
    ></VueCtkDateTimePicker>
    <table>
      <thead>
        <tr>
          <th>id</th>
          <th>ФИО</th>
          <th>Активность</th>
          <th>Последний вход</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.fio }}</td>
          <td align="center">
            <input
              type="checkbox"
              v-model="user.active"
              v-bind:id="user.id"
              onclick="this.checked=!this.checked;"
            />
          </td>
          <td>{{ user.active_time }}</td>
        </tr>
      </tbody>
    </table>

    <button
      v-for="pageN in pageCount"
      @click="pageNumber = pageN - 1"
      :class="{ current: pageNumber + 1 === pageN }"
    >
      {{ pageN }}
    </button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      users: [],
      size: 5,
      length: 0,
      pageNumber: 0,
      filterUsers: [],

      fio: "",
      active: false,
      active_time_start: "",
      active_time_end: "",
    };
  },
  mounted() {
    this.fetchData();
    console.log(this.pageCount);
  },
  methods: {
    fetchData() {
      //get(`/api/testusers?order=id&page=${this.pageNumber+1},${this.size}`)
      axios
        .get(`/api/testuser/`)
        .then((res) => {
          if (res.status == 200) {
            this.users = res.data;
            //this.length = res.data.results;
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    nextPage() {
      this.pageNumber++;
    },
    prevPage() {
      this.pageNumber--;
    },
    changeDate() {
      console.log("1111");
    },
    filter() {
      let fd = new FormData();
      fd.append('fio', this.fio);
      fd.append('active', this.active);
      fd.append('active_time_start', this.active_time_start);
      fd.append('active_time_end', this.active_time_end);
      //console.log(fd);
      axios
        .post(`/api/testuser/search`, {
          'fio': this.fio,
          'active': this.active
        })
        .then((res) => {
          if (res.status == 200) {
            this.filterUsers = res.data;
            console.log(res.data);
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
  watch: {
    pageNumber() {
      this.fetchData();
    },
  },
  computed: {
    pageCount() {
      return Math.ceil(this.length / this.size);
    },
  },
};
</script>
<style scoped>
.main {
  padding: 16px;
  margin: auto;
  width: 800px;
}
table {
  margin: 10px;
}

th,
td {
  padding: 5px 10px;
  border: 1px solid silver;
}

th {
  background: #eee;
}

a {
  color: #999;
}
.current {
  color: red;
}
ul {
  padding: 0;
  list-style-type: none;
}
li {
  display: inline;
  margin: 5px 5px;
}

a.first::after {
  content: "...";
}

a.last::before {
  content: "...";
}
</style>