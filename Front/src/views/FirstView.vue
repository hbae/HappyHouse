<template>
  <div>
    <div
      id="carouselExampleCaptions"
      class="carousel slide"
      data-bs-ride="carousel"
    >
      <div class="carousel-inner" style="background-color: rgb(54, 54, 54)">
        <div class="carousel-item active">
          <img
            src="../assets/houselogo.webp"
            style="object-fit: cover"
            class="d-block w-100"
            height="10%"
            alt="header image"
          />
          <div class="carousel-caption d-flex flex-column align-items-center">
            <div class="text-re mb-3">Happy House</div>
            <div class="input-group w-75">
              <input
                @keyup.enter="search"
                type="text"
                v-model="keyword"
                class="form-control form-control-lg"
                placeholder="원하시는 건물명 또는 동을 입력해주세요"
              />
              <button
                @click="search"
                class="btn btn-warning"
                type="button"
                style="background-color: #4e7c93; border: 0"
              >
                <i class="bi bi-search text-white" style="font-size: 1.5rem">
                </i>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Main Content -->
    <div class="body_area py-4 py-md-5" style="background-color: white">
      <div class="container">
        <!-- <div class="row g-3 row-deck">
          <div class="col-lg-3 col-md-6 col-sm-6 text-center">
						<div class="card border-0">
							<div class="card-body">
								<h3>25</h3>
								<span>Active Projects</span>
							</div>
						</div>
					</div>
					<div class="col-lg-3 col-md-6 col-sm-6 text-center">
						<div class="card border-0">
							<div class="card-body">
								<h3>40</h3>
								<span>Today Tasks</span>
							</div>
						</div>
					</div>
					<div class="col-lg-3 col-md-6 col-sm-6 text-center">
						<div class="card border-0">
							<div class="card-body">
								<h3>05</h3>
								<span>Today Expenses</span>
							</div>
						</div>
					</div>
					<div class="col-lg-3 col-md-6 col-sm-6 text-center">
						<div class="card border-0">
							<div class="card-body">
								<h3>03</h3>
								<span>Today Invoices</span>
							</div>
						</div>
					</div>
        </div> -->
        <div class="row g-3 my-3">
          <div class="overflow-hidden col-lg-6 col-md-12">
            <h4>부동산 관련 뉴스</h4>
            <table
              class="myDataTable table align-middle table-bordered mb-0 custom-table nowrap dataTable"
              style="border-style: solid; border-width: 10px"
            >
              <tbody>
                <tr v-for="(item, index) in news" :key="index">
                  <td class="text-nowrap">
                    <a
                      class="news-link"
                      :href="item.url"
                      target="_blank"
                      v-html="item.title"
                    >
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <div class="col-lg-6 col-md-12">
            <h4>공지사항</h4>
            <table
              class="myDataTable table align-middle table-bordered mb-0 custom-table nowrap dataTable"
              style="border-style: solid; border-width: 10px; margin-left: 2px"
            >
              <tbody>
                <tr v-for="(article, index) in articles" v-bind:key="index">
                  <td v-if="index < 5">
                    <a @click="boardDetail(article.articleno)">{{
                      article.subject
                    }}</a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import http from "@/util/http-common.js";
import { listArticle, getArticle } from "@/api/board.js";
import { mapActions, mapMutations } from "vuex";

export default {
  name: "MainView",
  data() {
    return {
      latestTradeData: [],
      news: [],
      noticeList: [],
      articles: [],
      keyword: "",
    };
  },
  methods: {
    ...mapActions("houseOnGoingStore", ["onGoingDetail"]),
    ...mapActions("boardNoticeStore", ["boardDetail"]),
    ...mapMutations("houseStore", ["SET_KEYWORD"]),
    search() {
      if (this.keyword == "") {
        this.$swal("키워드를 입력하세요.", { icon: "warning" });
        return;
      }
      this.SET_KEYWORD(this.keyword);
      this.$router.push({ name: "apt" });
    },
    houseOngoingDetail(id) {
      this.onGoingDetail(id);
    },
    ...mapMutations("boardStore", ["SET_BOARD_DETAIL"]),
    boardDetail(articleno) {
      let param = articleno;
      console.log(param);
      getArticle(
        param,
        (response) => {
          console.log(response.data);
          this.SET_BOARD_DETAIL(response.data);
          this.$router.push({ name: "boardDetail" });
        },
        (error) => {
          console.log(error);
        },
      );
    },
  },
  created() {
    http
      .get("/news")
      .then(({ data }) => {
        this.news = data;
      })
      .catch((error) => {
        console.log(error);
        this.$swal("서버에 문제가 발생하였습니다.", { icon: "error" });
      });

    http
      .get("/board")
      .then(({ data }) => {
        console.log(data);
        this.noticeList = data;
      })
      .catch((error) => {
        // console.log("Main: error ");
        console.log(error);
        this.$swal("서버에 문제가 발생하였습니다.", { icon: "error" });
      });

    let param = {
      pg: 1,
      spp: 20,
      key: null,
      word: null,
    };
    listArticle(
      param,
      (response) => {
        this.articles = response.data;
        console.log(response.data);
      },
      (error) => {
        console.log(error);
      },
    );
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Merriweather:wght@900&display=swap");
.carousel-item {
  height: 500px;
}

.carousel-item img {
  position: absolute;
  top: 0;
  left: 0;
  min-height: 500px;
}

.text-re {
  color: black;
  font-size: 80px;
  word-spacing: -25px;
  letter-spacing: 2px;
  font-family: "Merriweather", serif;
}
.news-link {
  text-decoration: none;
  color: #333;
}
a,
td {
  font-size: 15px;
  font-weight: bold;
}
</style>
