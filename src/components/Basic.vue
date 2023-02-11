<template>
    <div>
      <ul>
        <li v-for="issue in currentPageData" :key="issue.number">
          {{ issue.title }}
        </li>
      </ul>
      <div>
        <button @click="previousPage" :disabled="currentPage === 1">Previous</button>
        <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  export default {
    data() {
      return {
        issuesData: [],
        currentPage: 1,
        itemsPerPage: 10
      };
    },
    computed: {
      currentPageData() {
        const start = (this.currentPage - 1) * this.itemsPerPage;
        const end = start + this.itemsPerPage;
        return this.issuesData.slice(start, end);
      },
      totalPages() {
        return Math.ceil(this.issuesData.length / this.itemsPerPage);
      }
    },
    methods: {
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
    created() {
      axios.get('https://api.github.com/repos/axios/axios/issues')
        .then(response => {
          this.issuesData = response.data;
          console.log(this.issuesData.length)
        })
        .catch(error => {
          console.error(error);
        });
    }
  };
  </script>