<template>
  <div class="main">
    <h3>Список пользователей</h3>
      <label for="filter_name">ФИО</label>
      <input type="text" v-model="filter_name" id="filter_name" />
    <label for="checkbox">Активен</label><input type="checkbox" id="checkbox" v-model="checked">
    <VueCtkDateTimePicker
              input-class="form-control"
              v-model="startDate"
              name="startDate"
              label="Начало периода"
              hint="Введите начало периода, дату и время"
              locale="ru"
            >
            </VueCtkDateTimePicker>
<VueCtkDateTimePicker
              input-class="form-control"
              v-model="endDate"
              name="endDate"
              label="Конец периода"
              hint="Введите конец периода, дату и время"
              locale="ru"
              @input="changeDate"
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
          <td align="center"><input type="checkbox" v-model="user.active" v-bind:id="user.id" disabled></td>
          <td>{{ user.active_time }}</td>
        </tr>
      </tbody>
    </table>
    
    <button v-for="pageN in pageCount" @click="pageNumber = pageN - 1" :class="{current:  pageNumber +1 === pageN}">
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
    };
  },
  mounted() {
    this.fetchData();
    console.log(this.pageCount);
  },
  methods: {
    fetchData() {
      axios
        .get(`/api/users?order=id&page=${this.pageNumber+1},${this.size}`)
        .then((res) => {
          if (res.status == 200) {
            this.users = res.data.records;
            this.length = res.data.results;
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
    changeDate(){
      console.log("1111")
    }
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