<template>
  <div class="paginate-cont" :class="'color-' + color">
    <div
      class="paginate-back"
      :class="page <= 1 && backupPage == page ? 'deactive' : ''"
      @click="prevPage()"
    >
      <svg
        width="30"
        height="30"
        viewBox="0 0 30 30"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
        class="paginate-icon"
      >
        <path
          d="M18.7285 23.7407C19.0591 23.4249 19.0887 22.9293 18.8097 22.5835L18.726 22.4932L10.5863 14.8038C10.2219 14.4593 9.63328 14.4605 9.27141 14.8062C9.09171 14.9778 9 15.2036 9 15.4281C9 15.6172 9.06369 15.8055 9.19034 15.9624L9.27388 16.0525L17.4136 23.7418C17.7779 24.0863 18.3666 24.0851 18.7285 23.7407ZM18.7259 8.36405C19.0915 8.01955 19.089 7.46107 18.7284 7.11658C18.3966 6.79972 17.8744 6.77233 17.509 7.0344L17.4135 7.11305L12.0076 12.2193C11.8255 12.391 11.7338 12.6179 11.7338 12.8436C11.7338 13.0682 11.8255 13.2939 12.0052 13.4656C12.3369 13.7825 12.8592 13.8099 13.2246 13.5478L13.3201 13.4691L18.7259 8.36405Z"
          fill="black"
        />
      </svg>
    </div>
    <div class="paginate-text-cont">
      <div class="paginate-input">
        <input
          type="number"
          ref="page"
          @focus="backupPage = page"
          @input="userPage($event.target.value)"
          @blur="onBlur()"
          @keyup.enter="submitPage()"
          :value="page ? (page < 10 ? `0${page}` : page) : '01'"
          class="paginate-input-text"
        />
      </div>
      <div class="paginate-text">
        /<span>{{ formattedPage }}</span>
      </div>
    </div>
    <div
      class="paginate-next"
      :class="page >= totalPages && backupPage == page ? 'deactive' : ''"
      @click="nextPage()"
    >
      <svg
        width="30"
        height="30"
        viewBox="0 0 30 30"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
        class="paginate-icon"
      >
        <path
          d="M11.2715 6.25935C10.9409 6.57513 10.9113 7.07072 11.1903 7.41648L11.274 7.50681L19.4137 15.1962C19.7781 15.5407 20.3667 15.5395 20.7286 15.1938C20.9083 15.0222 21 14.7964 21 14.5719C21 14.3828 20.9363 14.1945 20.8097 14.0376L20.7261 13.9475L12.5864 6.25817C12.2221 5.91368 11.6334 5.91485 11.2715 6.25935ZM11.2741 21.636C10.9085 21.9804 10.911 22.5389 11.2716 22.8834C11.6034 23.2003 12.1256 23.2277 12.491 22.9656L12.5865 22.8869L17.9924 17.7807C18.1745 17.609 18.2662 17.3821 18.2662 17.1564C18.2662 16.9318 18.1745 16.7061 17.9948 16.5344C17.6631 16.2175 17.1408 16.1901 16.7754 16.4522L16.6799 16.5309L11.2741 21.636Z"
          fill="black"
        />
      </svg>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    initPage: {
      type: Number,
      required: false,
      default: 1,
      validator: function (value) {
        if (value <= 1) return 1;
        return value > 0;
      },
    },
    totalPages: {
      type: Number,
      required: false,
      default: 2,
      validator: function (value) {
        if (value <= 0) return 1;
        return value > 0;
      },
    },
    color: {
      type: String,
      required: false,
      default: "green",
      validator: function (value) {
        if (
          value == "red" ||
          value == "green" ||
          value == "blue" ||
          value == "gray" ||
          value == "purple" ||
          value == "orange" ||
          value == "dark"
        )
          return value;
        return "green";
      },
    },
  },
  data() {
    return {
      page: 1,
      backupPage: 1,
    };
  },
  computed: {
    formattedPage() {
      return this.totalPages < 10 ? `0${this.totalPages}` : this.totalPages;
    },
  },
  methods: {
    reset() {
      this.page = this.initPage;
      this.backupPage = this.initPage;
    },
    onBlur() {
      this.page = this.backupPage;
    },
    nextPage() {
      if (this.page >= this.totalPages) return;
      this.page += 1;
      this.backupPage = this.page;
      this.$emit("currentPage", this.page);
    },
    prevPage() {
      if (this.page <= 1) return;
      this.page -= 1;
      this.backupPage = this.page;
      this.$emit("currentPage", this.page);
    },
    userPage(e) {
      if (parseInt(e)) {
        this.page = parseInt(e);
      }
    },
    submitPage() {
      if (this.page == 1) {
        this.$refs.page.value = "01";
      }
      if (this.page > 0 && this.page <= this.totalPages) {
        this.backupPage = this.page;
        this.$refs.page.blur();
        this.$emit("currentPage", this.page);
      } else {
        this.page = this.backupPage;
        this.$refs.page.blur();
      }
    },
  },
  watch: {
    initPage(newValue, oldValue) {
      console.log("here");
      if (newValue != oldValue) {
        if (newValue < 0) this.page = 1;
        else this.page = newValue;
      }
    },
    page() {
      if (this.page == 1) {
        this.$refs.page.value = "01";
      }
      if (this.page <= 0 || this.page > this.totalPages) {
        this.page = this.backupPage;
      }
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;500;700;900&display=swap");
.paginate-cont {
  width: 100%;
  background-color: white;
  border: solid 1px #eaeaea;
  border-radius: 10px;
  height: 40px;
  max-width: 200px;
  margin: 0 auto;
  overflow: hidden;
  text-align: left !important;
}
.paginate-cont .paginate-back {
  width: 25%;
  height: 100%;
  text-align: center;
  padding-top: 4px;
  float: left;
  transition: opacity 0.3s linear;
}
.paginate-cont .paginate-back.deactive {
  opacity: 0.3;
}
.paginate-cont .paginate-back:hover {
  background-color: #f6f6f6;
}
.paginate-cont .paginate-back:hover.deactive {
  background-color: white;
}
.paginate-cont .paginate-text-cont {
  width: 50%;
  height: 100%;
  float: left;
  box-sizing: border-box;
  border-right: solid 1px #eaeaea;
  border-left: solid 1px #eaeaea;
  padding-top: 7px;
  display: flex;
  user-select: none;
  justify-content: center;
  flex-direction: row;
  font-family: "Poppins", sans-serif;
}
.paginate-cont .paginate-text-cont .paginate-input {
  width: 43%;
  height: 25px;
}
.paginate-cont
  .paginate-text-cont
  .paginate-input
  input[type="number"]::-webkit-inner-spin-button,
.paginate-cont
  .paginate-text-cont
  .paginate-input
  input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
  display: none;
}
.paginate-cont .paginate-text-cont .paginate-input input[type="number"] {
  -moz-appearance: textfield;
  /* Firefox */
  font-family: "Poppins", sans-serif;
}
.paginate-cont .paginate-text-cont .paginate-input input[type="number"] {
  width: 100%;
  height: 100%;
  border: none;
  font-size: 16px;
  color: black;
  font-weight: 700;
  text-align: right;
  background-color: rgba(255, 255, 255, 0);
  border-radius: 5px;
  padding-top: 0;
}
.paginate-cont .paginate-text-cont .paginate-input input[type="number"]:focus {
  outline: solid 1px #e8e8e8;
}
.paginate-cont .paginate-text-cont .paginate-text {
  width: 45%;
  height: 25px;
  font-weight: 700;
  color: #bbb;
  padding-left: 5px;
  font-size: 16px;
}
.paginate-cont .paginate-next {
  width: 25%;
  height: 100%;
  text-align: center;
  float: left;
  padding-top: 4px;
  transition: 0.3s linear;
}
.paginate-cont .paginate-next.deactive {
  opacity: 0.3;
}
.paginate-cont .paginate-next:hover {
  background-color: #f6f6f6;
}
.color-red .paginate-icon path {
  fill: #ed1c16;
}
.color-red .paginate-input-text {
  color: #ed1c16 !important;
}
.color-green .paginate-icon path {
  fill: #22c5a9;
}
.color-green .paginate-input-text {
  color: #22c5a9 !important;
}
.color-blue .paginate-icon path {
  fill: #0085c3;
}
.color-blue .paginate-input-text {
  color: #0085c3 !important;
}
.color-gray .paginate-icon path {
  fill: #616161;
}
.color-gray .paginate-input-text {
  color: #616161 !important;
}
.color-purple .paginate-icon path {
  fill: #833ab4;
}
.color-purple .paginate-input-text {
  color: #833ab4 !important;
}
.color-orange .paginate-icon path {
  fill: #f90;
}
.color-orange .paginate-input-text {
  color: #f90 !important;
}
.color-dark {
  background-color: #121212;
}
.color-dark .paginate-icon path {
  fill: white;
}
.color-dark .paginate-input-text {
  color: white !important;
}
</style>