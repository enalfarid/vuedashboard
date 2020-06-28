<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <card class="strpied-tabled-with-hover"
                body-classes="table-full-width table-responsive"
          >
            <template slot="header">
              <h4 class="card-title">Data Global Rank Domain</h4>
              <p class="card-category">Data global domain rank in the world</p>
            </template>
            <l-table class="table-hover table-striped table-md"
                     :columns="columns"
                     :data="displayedData">
            </l-table>


              <nav>
              <ul class="pagination">
                <li class="page-item">
                  <button type="button" class="page-link" v-if="page != 1" @click="page = 1"> First </button>
                </li>
                <li class="page-item">
                  <button type="button" class="page-link" v-if="page != 1" @click="page--"> Previous </button>
                </li>
                <li class="page-item">
                  <button type="button" class="page-link" v-for="pageNumber in pages.slice(page-1, page+5)" @click="page = pageNumber"> {{pageNumber}} </button>
                </li>
                <li class="page-item">
                  <button type="button" @click="page++" v-if="page < pages.length" class="page-link"> Next </button>
                </li>
                <li class="page-item">
                  <button type="button" class="page-link" v-if="page != 1" @click="page = pages.length"> Last </button>
                </li>
              </ul>
              </nav>
          </card>

        </div>

      </div>
    </div>
  </div>
</template>
<script>
  import axios from 'axios'
  import qs from 'qs'
  import LTable from 'src/components/Table.vue'
  import Card from 'src/components/Cards/Card.vue'

  const columns = ['domain', 'GlobalRank', 'TLD', 'IDNDomain']

  export default {
    components: {
      LTable,
      Card
    },
    data () {
      return {
        posts : [],
        page: 1,
        perPage: 50,
        offset: 0,
        limit: 20000,
        pages: [],
        columns: [...columns],

      }
    },
    methods: {

          getPosts () {
              axios.post('http://localhost:9000/domainrank',
              qs.stringify({
                limit : this.limit,
                offset: this.offset
              }),
              {'content-type': 'application/x-www-form-urlencoded;charset=utf-8'}
              )
              .then(res => {
                  this.posts = res.data.data
                })
          },
          setPages () {
            let numberOfPages = Math.ceil(this.posts.length / this.perPage);
            for (let index = 1; index <= numberOfPages; index++) {
              this.pages.push(index);
            }
          },
          paginate (posts) {
            let page = this.page;
            let perPage = this.perPage;
            let from = (page * perPage) - perPage;
            let to = (page * perPage);
            return  posts.slice(from, to);
          }
    },

    computed: {
		displayedData() {
      return this.paginate(this.posts);
		}
	},
	watch: {
		posts () {
			this.setPages();
		}
	},
	created(){
		this.getPosts();
	},
	filters: {
		trimWords(value){
			return value.split(" ").splice(0,20).join(" ") + '...';
    }
  }
  }
</script>
<style>
    button.page-link {
	display: inline-block;
}
button.page-link {
    font-size: 20px;
    color: #29b3ed;
    font-weight: 500;
}
.offset{
  width: 500px !important;
  margin: 20px auto;
}
</style>
