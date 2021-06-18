<template>
  <div class="picture-wrapper">
    <div class="picture-preview">
      <img :src="activePic" alt="BigPic" />
    </div>

    <div class="pictures">
      <div
        class="picture-container"
        v-for="(picture, index) in picturesFlickr"
        :key="index"
        @click="changeActivePic(index)"
      >
        <img @click="deleteItem()" :src="picture" alt="pic" />
      </div>
    </div>
    <button :disabled="this.counter <= 0" @click="prevSlides()">PREV</button>
    <button
      :disabled="this.counter >= this.picturesFlickr.length / 9 - 1"
      @click="nextSlides()"
    >
      NEXT
    </button>
  </div>
</template>

<script>
export default {
  name: "Gallery",
  beforeMount() {
    fetch(
      `https://api.flickr.com/services/rest/?method=flickr.photos.search&format=json&nojsoncallback=1&api key=${this.api}&extras=url_m&text=nature'`
    )
      .then((response) => response.json())
      .then(({ photos: { photo: photoArr } }) => {
        photoArr.forEach((photoObj) => {
          const { url_m } = photoObj;
          this.picturesFlickr.push(url_m);
        });
        this.activePic = photoArr[0].url_m;
      });
  },
  data() {
    return {
      counter: 1,
      api: "c4657ca0f0bcefb6e4d09b69edf2dfd0",
      activePic: "",
      picturesFlickr: [],
      pictures: [
        "assets/Bubbles.jpg",
        "assets/Forest.jpg",
        "assets/Hubble_Extreme_Deep_Field.jpg",
        "assets/huntington bancshares.jpg",
        "assets/Lotus.jpg",
        "assets/Mansion.jpg",
        "assets/Moon.jpg",
        "assets/Three.jpg",
        "assets/Tie_dye.jpg",
        "assets/Vaporwave_wallpaper.jpg",
      ],
    };
  },
  methods: {
    changeActivePic(index) {
      this.activePic = this.picturesFlickr[index];
    },
    slideTransform() {
      const slider = document.querySelector(".pictures");
      slider.style.transform = `translateX(${
        -slider.clientWidth * this.counter
      }px)`;
    },
    nextSlides() {
      if (this.counter >= this.picturesFlickr.length / 9 - 1) return;
      console.log(this.counter);
      this.counter++;
      this.slideTransform();
    },
    prevSlides() {
      if (this.counter <= 0) return;
      console.log(this.counter);
      this.counter--;

      this.slideTransform();
    },
    deleteItem() {
      // console.log(this);
      // this.deleteItem();
    },
  },
};
</script>

<style lang="scss" scoped>
.picture-wrapper {
  width: 1455px;
  margin: 0 auto;
  overflow: hidden;
}
.picture-preview {
  width: 1000px;
  height: 600px;
  margin: 0 auto;
  margin-bottom: 24px;
  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
}
.pictures {
  display: grid;
  grid-template-rows: auto;
  grid-template-columns: repeat(1000, auto);
  grid-gap: 24px;
  transition: transform 0.4s ease-in-out;
  // overflow: hidden;
  .picture-container {
    width: 140px;
    height: 140px;
  }
  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  max-width: 1452px;
  margin: 0 auto;
}
</style>

