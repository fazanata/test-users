<template>
  <div class="main">
    <h3>Список пользователей</h3>
    <label for="fio">ФИО</label>
    <v-text-field type="text" v-model="fio" id="fio" @input="filter" ></v-text-field>
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
    
    <v-btn
      class="ma-2"
      outlined
      color="indigo"
       @click="filter"
    >
      Выбрать данные
    </v-btn>
    <v-btn
      class="ma-2"
      outlined
      color="indigo"
       @click="clearFilter"
    >
      Очистить фильтр
    </v-btn>


    <v-data-table
    :headers="headers"
    :items="users"
    :sort-by="['id', 'fio']"
    :sort-desc="[false, true]"
    multi-sort
    class="elevation-1"
    :footer-props="{
      'items-per-page-options': [5,10, 20, 30, 40, 50],
        showFirstLastPage: true,
           'items-per-page-text':'Записей на странице'
      }"
  ></v-data-table>

  </div>
</template>

<script>
import axios from "axios";
import { VBtn } from 'vuetify/lib'
import { VInput } from 'vuetify/lib'

export default {
  extends: {
    VBtn, VInput,
  },
  data() {
    return {
      headers: [
          { text: 'id', value: 'id' },
          { text: 'ФИО', align: 'start', value: 'fio' },
          { text: 'Активность', value: 'active' },
          { text: 'Время активности', align: 'start', value: 'active_time' },
        ],

      users: [],
      filterUsers: [],

      fio: "",
      active: null,
      active_time_start: "",
      active_time_end: "",
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      axios
        .get(`/api/testuser/`)
        .then((res) => {
          if (res.status == 200) {
            this.users = res.data.map(s=> (
              { id: s.id,
                fio: s.fio,
                active: s.active,
                active_time: s.active_time.substring(0,16)}));
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
      
      axios
        .post(`/api/testuser/search`, formData)
        .then((res) => {
          if (res.status == 200) {
            this.users = res.data;
          }
        })
        .catch((err) => {
          console.log(err);
        });
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