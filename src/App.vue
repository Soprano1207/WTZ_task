<template>
    <div class="section-images">
      <div class="container">
        <div class="section-images__wrapper">
          <figure class="section-images__item" v-for:="obj in images">
            <div class="section-images__title">
              <a :href="obj.profile_user" target="_blank">
                <img class="section-images__user-photo" :src="obj.profile_image" :alt="obj.name">
                <div class="section-images__user-links">
                  <div>{{obj.name}}</div>
                  <div>@{{obj.username}}</div>
                </div>
              </a>
            </div>
            <div class="section-images__photo-container">
              <img :src="obj.img" alt="photo">
            </div>
            <div class="section-images__views">
              <div>{{obj.views}}</div>
              <img src="./assets/icons/Eye.svg" alt="eye">
            </div>
          </figure>
        </div>
      </div>
    </div>

    <div class='section-paginator'>
      <div class="container">
        <ul class="section-paginator__list">
          <li @click="changePage(page.id)" class="section-paginator__item" :class="{'section-paginator__current-page': currentPage === page.id}" v-for:="page in pages">{{ page.id }}</li>
        </ul>
      </div>
    </div>
</template>

<script>
import { createApi } from 'unsplash-js'; 

export default {
  data() {
    return {
      unsplash: createApi({
        accessKey: 'this is my access key)'
      }),

      pages: [], //массив страницы для отображения
      images: [], //данные изображений для вывода

      currentPage: 1, //текущая страница
      totalPages: 0, //максимальное кол-во страниц

      countImages: 4, //кол-во изображений для вывода

    }
  },
  methods: {
    getPhotos() {
      //Получение изображений и их информации с Unsplash

      try {
        this.unsplash.
        collections.list({page: this.currentPage, perPage: this.countImages}).then(result => {
          this.totalPages = Math.ceil((result.response.total / this.countImages) / 20);

          for (let i of result.response.results) {
            // console.log(result);
            this.unsplash.photos.getStats({ photoId: 'mtNweauBsMQ' }).then(result2 => {
              this.images.push({
                img: i.cover_photo.urls.regular,
                name: i.user.name,
                username: i.user.username,
                profile_user: i.user.links.html,
                profile_image: i.user.profile_image.large,
                views: result2.response.views.total,
                
              });
            });
          }
          this.pagesToDisplay();
        });
      } catch(error) {
        console.log(error);
      }
    },
    pagesToDisplay() {
      //страницы для вывода, основываясь на текущей выбранной странице

      switch (this.currentPage) {
        case 1:
        case 2: 
          for (let i = 1; i <= 3; i++) {
            this.pages.push({id: i});
          }
          this.pages.push({id: "..."});
          this.pages.push({id: this.totalPages});
        break;

        case 3:
          for (let i = 1; i <= 4; i++) {
            this.pages.push({id: i});
          }
          this.pages.push({id: "..."});
          this.pages.push({id: this.totalPages});
        break;

        case this.totalPages - 2:
          this.pages.push({id: 1});
          this.pages.push({id: "..."});
          for (let i = this.totalPages - 3; i <= this.totalPages; i++) {
            this.pages.push({id: i});
          }
        break;

        case this.totalPages - 1:
        case this.totalPages:
          this.pages.push({id: 1});
          this.pages.push({id: "..."});
          for (let i = this.totalPages - 2; i <= this.totalPages; i++) {
            this.pages.push({id: i});
          }
        break;

        default:
          this.pages.push({id: 1});
          this.pages.push({id: "..."});
          for (let i = this.currentPage - 1; i <= this.currentPage + 1; i++) {
             this.pages.push({id: i});
          }
          this.pages.push({id: "..."});
          this.pages.push({id: this.totalPages});
        break;
      }
    },
    changePage(changePageNum) {
      //переход на другую страницу

      if (changePageNum === "...") return false;
      if (changePageNum === this.currentPage) return false;

      this.currentPage = changePageNum;
      this.pages.length = 0;
      this.images.length = 0;
      this.getPhotos();
    }
  },
  mounted() {
    //первая загрузка страницы, вывод фотографий 1ой страницы

    this.getPhotos();
  }
}

</script>

<style lang='scss'>
  @import 'assets/scss/main.scss';

  .section-images {
    background-color: #ffffff;
    &__wrapper {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
    }
    &__item:nth-child(even) {
      margin-left: 20px;
    }
    &__item {
      width: 320px;
    }
    &__title {
      display: flex;
      padding: 10px;
      a {
        display: flex;
        text-decoration: none;
        cursor: pointer;
      }
    }
    &__user-photo {
      width: 30px;
      height: 30px;
      border-radius: 50%;
    }
    &__user-links {
      margin-left: 10px;
      div:nth-child(1) {
        font-weight: 700;
        font-size: 12px;
        color: #333333;
        line-height: 12px;
      }
      div:nth-child(2) {
        margin-top: 3px;
        font-weight: 400;
        font-size: 12px;
        line-height: 12px;
        color: #8D8D8D;
      }
    }
    &__photo-container {
      width: 100%;
      height: 230px;
      img {
        width: 100%;
        height: 100%;
        object-fit: cover;
      }
    }
    &__views {
      display: flex;
      justify-content: flex-end;
      padding: 10px;
      div {
        font-weight: 700;
        font-size: 12px;
        margin-right: 5px;

        color: #8D8D8D;
      }
    }
  }
  
  .section-paginator {
    background-color: #000000;
    &__list {
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;
    }
    &__item {
      list-style-type: none;
      font-size: 14px;
      font-weight: 400;
      color: #ffffff;
      padding: 17px 7.5px 17px 7.5px;
      cursor: pointer;
    }
    &__current-page {
      font-weight: 700;
      font-size: 18px;
    }
  }


</style>