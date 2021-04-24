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
    ></VueCtkDateTimePicker>
    
    <button
       @click="filter"
    >
      Выбрать данные
    </button>
    <button
       @click="clearFilter"
    >
      Очистить фильтр
    </button>

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
          <td>{{ user.active_time.substring(0, 16) }}</td>
        </tr>
      </tbody>
    </table>

    <v-data-table
    :headers="headers"
    :items="users"
    :sort-by="['id', 'fio']"
    :sort-desc="[false, true]"
    multi-sort
    class="elevation-1"
  ></v-data-table>

  </div>
</template>

<script>
import axios from "axios";
import { VDataTable } from "vuetify/es5/components/VDataTable";

export default {
  data() {
    return {
      headers: [
          { text: 'id', value: 'id' },
          { text: 'ФИО', align: 'start', value: 'fio' },
          { text: 'Активность', value: 'active' },
          { text: 'Время активности', align: 'start', value: 'active_time' },
        ],

      users: [],
      size: 5,
      length: 0,
      pageNumber: 0,
      filterUsers: [],

      fio: "",
      active: null,
      active_time_start: "",
      active_time_end: "",
    };
  },
  mounted() {
    this.fetchData();
    console.log(this.pageCount);
  },
  components: {
    VDataTable
  },
  methods: {
    fetchData() {
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
    clearFilter() {
      this.fio='';
      this.active=null;
      this.active_time_start='';
      this.active_time_end='';
      this.filter();
    },
    filter() {
      
      
      let formData = new FormData();
      if (this.fio !== '') formData.append('fio', this.fio);
      if (this.active !== null) formData.append('active', this.active);
      if (this.active_time_start !== '') formData.append('active_time_start', this.active_time_start.substring(0, 16));
      if (this.active_time_end !== '') formData.append('active_time_end', this.active_time_end.substring(0, 16));
      console.log('formData', formData);
      axios
        .post(`/api/testuser/search`, formData)
        .then((res) => {
          if (res.status == 200) {
            this.users = res.data;
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