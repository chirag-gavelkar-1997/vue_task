<template>
  <main class="tableData">
    <section>
      <header>
        <input class="searchInput" v-model="searchTerm1" placeholder="search by title" />
      </header>
      <div class="loading" v-if="loading">Loading...</div>
      <div v-else>
        <div class="tableContainer">
          <table v-if="filteredData.length > 0" border>
            <thead>
              <tr>
                <th>ID</th>
                <th>Title</th>
                <th>Description</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in filteredData" :key="item.id">
                <td>{{ item.id }}</td>
                <td>{{ item.title }}</td>
                <td>{{ item.description }}</td>
              </tr>
            </tbody>
          </table>
          <div class="noData" v-else>No Data Found</div>
        </div>
      </div>
      <footer class="footer" v-if="filteredData.length > 0">
        <button class="btnPre" @click="prevPage" :disabled="currentPage == 1">Pre</button>
        <span class="currentPage">{{currentPage}}</span>
        <button class="btnNext" @click="nextPage" :disabled="currentPage === totalPages">Next</button>
      </footer>
    </section>
  </main>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      itemPerPage: 10,
      data: [],
      currentPage: 1,
      searchTerm1: '',
      loading: true,
    }
  },
  computed: {
    filteredData() {
      let start = (this.currentPage - 1) * this.itemPerPage;
      let end = start + this.itemPerPage;
      return this.data.filter(item => item.title.toLowerCase().includes(this.searchTerm1.toLowerCase())).slice(start, end);
    },
    totalPages() {
      return Math.ceil(this.data.length / this.itemPerPage);
    },
  },
  methods: {
    fetchData() {
      this.loading = true;
      axios.get('https://dummyjson.com/products').then(res => {
        this.data = res.data.products;
        this.loading = false;
      }).catch(error => {
        console.error('Error fetching data:', error);
        this.loading = false;
      })
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
  },
  mounted() {
    this.fetchData();
  }
}
</script>

<style scoped>
.tableData {
  width: 97%;
  height: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 20px;
}

.header,
.footer {
  width: 100%;
  margin-top: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.footer {
  border-top: 1px solid black;
  padding-top: 10px;
}

.btnPre, .btnNext {
  background: green;
  color: white;
  padding: 5px 10px;
  cursor: pointer;
  border: none;
  margin: 0 5px;
}

.searchInput {
  width: 100%;
  margin-bottom: 10px;
  padding: 10px;
  box-sizing: border-box;
}

.noData {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

.loading {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.tableContainer {
  width: 100%;
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ddd;
  padding: 2px;
}

th {
  background-color: #f2f2f2;
  text-align: left;
}

@media (max-width: 1024px) {
  .tableData, .footer, .header {
    padding: 15px;
  }

  .searchInput, .btnPre, .btnNext {
    padding: 8px;
  }

  .currentPage {
    margin: 0 8px;
  }

  table, th, td {
    font-size: 16px;
  }
}

@media (max-width: 768px) {
  .tableData, .footer, .header {
    padding: 10px;
  }

  .searchInput {
    width: calc(100% - 20px);
  }

  .btnPre, .btnNext {
    width: auto;
    margin: 5px 0;
  }

  .currentPage {
    margin: 0 10px;
  }

  table, th, td {
    font-size: 14px;
  }

  table {
    width: 100%;
    display: block;
    overflow-x: auto;
  }
}

@media (max-width: 480px) {
  .tableData, .footer, .header {
    padding: 5px;
  }

  .searchInput, .btnPre, .btnNext {
    width: 90%;
  }

  table, th, td {
    font-size: 12px;
  }
}
</style>
