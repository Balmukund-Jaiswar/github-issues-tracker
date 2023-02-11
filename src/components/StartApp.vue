<template>
    <div>
      <form @submit.prevent="fetchIssuesData()">
        <div>
          <label for="username">Username:</label>
          <input type="text" id="username" v-model="username"/>
        </div>
        <div>
          <label for="repo">Repository:</label>
          <input type="text" id="repo" v-model="repo"/>
        </div>
        <button>Find</button>
      </form>
      <table>
        <tr>
          <th>Number</th>
          <th>Title of issues</th>
          <th>Created</th>
          <th>Status</th>
        </tr>
        <tr v-if="isLoading">
          <td>Loading...</td>
        </tr>
        <tr v-else>
          <td></td>
          <td>No data Available</td>
        </tr>
        <template v-if="!isLoading">
          <tr v-for="issue in currentPageData" :key="issue.number">
            <td>{{ issue.number }}</td>
            <td>{{ issue.title }}</td>
            <td>{{ issue.created_at }}</td>
            <td>{{ issue.state }}</td>
          </tr>
        </template>
      </table>
      <div>
        <label for="row-per-page">row per page:</label>
        <select id="row-per-page" v-model="showPerPage">
          <option value="5">5</option>
          <option value="10">10</option>
          <option value="30">30</option>
          <option value="50">50</option>
        </select>
      </div>
      <div>
        <button @click="previousPage" :disabled="currentPage === 1">Previous</button>
        <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
      </div>
    </div>
</template>
  

<!--Script Part-->

<script>
  import axios from 'axios';
  export default {
    data() {
      return {
        username: '',
        repo: '',
        issuesData: [],
        isLoading: false,
        currentPage: 1,
        loadPage:1,
        showPerPage: 10,
        maxPerPage: 100
      };
    },
    computed: {
      currentPageData() {
        const start = (this.currentPage - 1) * this.showPerPage;
        const end = start + this.showPerPage;
        return this.issuesData.slice(start, end);
      },
      totalPages() {
        return Math.ceil(this.issuesData.length / this.showPerPage);
      }
    },
    methods: {
      fetchIssuesData() {
        if(this.loadPage===1){
          this.issuesData = []
          this.currentPage = 1
          this.isLoading = true
        }
        axios.get(`https://api.github.com/repos/${this.username}/${this.repo}/issues?page=${this.loadPage}&per_page=${this.maxPerPage}&state=closed`)
          .then(response => {
            this.issuesData = this.issuesData.concat(response.data);
            if(response.data.length===100){
              this.loadPage++;
              console.log(this.issuesData.length,this.loadPage)
              this.fetchIssuesData()
            }else{
              this.isLoading=false;
              this.loadPage=1;
            }
          })
          .catch(error => {
            this.isLoading=false
            console.error(error);
          });
      },
      previousPage() {
        if (this.currentPage > 1) {
          this.currentPage--;
        }
      },
      nextPage() {
        if (this.currentPage < this.totalPages) {
          this.currentPage++;
        }
      }
    },
  };
</script>
