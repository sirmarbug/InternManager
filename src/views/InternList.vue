<template>
  <div class="intern-list pb-3">
    <div class="container">
      <div class="row">
        <div class="col">
          <b-card class="mt-3" header="Stażyści">
            <table class="table table-striped table-bordered table-hover" v-if="data.length > 0">
              <thead class="thead-dark">
                <tr>
                  <th scope="col">Imię</th>
                  <th scope="col">Nazwisko</th>
                  <th scope="col">Avatar</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="intern in data" :key="intern.id" @click="onEditClick(intern.id)">
                  <td>{{ intern.first_name }}</td>
                  <td>{{ intern.last_name }}</td>
                  <td>
                    <b-img class="avatar"
                      :src="intern.avatar"
                      fluid alt="avatar"></b-img>
                  </td>
                </tr>
              </tbody>
            </table>
            <b-pagination v-model="currentPage" :total-rows="rows" :per-page="perPage" @change="changedPage">
            </b-pagination>
          </b-card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'InternList',
  data() {
    return {
      currentPage: 1,
      rows: 12,
      perPage: 6,
      data: []
    }
  },
  methods: {
    changedPage(event) {
      this.$http.get(`https://reqres.in/api/users?page=${event}`)
        .then((response) => {
          this.perPage = response.data.per_page;
          this.rows = response.data.total;
          this.data = response.data.data;
        })
        .catch(() => {
          this.makeFailedToast();
        })
    },
    onEditClick(id) {
      this.$log.debug(id);
      this.$router.push(`/intern-edit/${id}`)
    },
    makeFailedToast() {
      this.$bvToast.toast('Spróbuj ponownie później', {
        title: 'Błąd pobierania danych',
        variant: 'danger',
        solid: true
      })
    }
  },
  mounted() {
    this.$http.get('https://reqres.in/api/users?page=1')
      .then((response) => {
        this.currentPage = response.data.page;
        this.perPage = response.data.per_page;
        this.rows = response.data.total;
        this.data = response.data.data;
      })
      .catch(() => {
        this.makeFailedToast();
      })
  }
}
</script>

<style scoped lang="scss">
.avatar {
  max-width: 100px;
}

.table {
  tr {
    cursor: pointer;
  }

  td {
    vertical-align: middle
  }
}
</style>