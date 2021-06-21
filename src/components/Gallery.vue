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
        ref="pictureCont"
        @click="changeActivePic(index)"
        @mouseover="hovering(index)"
        @mouseleave="unhover(index)"
      >
        <img :src="picture" alt="pic" />
        <div
          ref="modalWindow"
          :class="isActive(index)"
          class="inactive-window"
        ></div>
        <img
          ref="deleteBtn"
          class="delete-btn"
          src="assets/delete.png"
          alt="delete-btn"
          @click="deleteItem(index)"
        />
      </div>
    </div>
    <div class="preview-btns">
      <button class="prevBtn" :disabled="this.counter <= 0" @click="prevSlides">
        <img src="assets/Arrow-right.png" alt="arrow-right" />
      </button>
      <span>{{ this.counter + 1 }} / {{ Math.round(this.totalPics / 9) }}</span>
      <button
        class="nextBtn"
        :disabled="this.counter >= this.picturesFlickr.length / 9 - 1"
        @click="nextSlides"
      >
        <img src="assets/Arrow-left.png" alt="arrow-left" />
      </button>
    </div>
    <div class="upload-btns">
      <input
        style="display: none"
        ref="fileInput"
        type="file"
        @change="onFileSelected"
      />
      <button
        @click="$refs.fileInput.click()"
        class="upload-btn upload-btn-user"
      >
        Upload new image
      </button>
      <button @click="uploadRandomImg" class="upload-btn upload-btn-flickr">
        Upload from Flickr
      </button>
    </div>
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
        for (let i = 0; i < this.totalPics; i++) {
          const { url_m } = photoArr[i];
          this.picturesFlickr.push(url_m);
        }
        this.activePic = photoArr[0].url_m;
        console.log(photoArr);
      });
  },
  data() {
    return {
      counter: 0,
      api: "c4657ca0f0bcefb6e4d09b69edf2dfd0",
      activePic: "",
      totalPics: 36,
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
      selectedFile: null,
      isHovering: false,
    };
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
      const obj = Object.assign(event.target.files[0]);
      console.dir(obj);

      localStorage.setItem("selectedFile", obj.name);
    },
    uploadRandomImg() {
      let randomImgIndex =
        Math.floor(Math.random() * (100 - this.totalPics + 1)) + this.totalPics;
      console.log(randomImgIndex);
      fetch(
        `https://api.flickr.com/services/rest/?method=flickr.photos.search&format=json&nojsoncallback=1&api key=${this.api}&extras=url_m&text=nature'`
      )
        .then((response) => response.json())
        .then(({ photos: { photo: photoArr } }) => {
          this.activePic = photoArr[randomImgIndex].url_m;
          console.log(photoArr);
        });
    },
    changeActivePic(index) {
      this.activePic = this.picturesFlickr[index];
    },
    isActive(index) {
      return {
        "active-window": this.activePic === this.picturesFlickr[index],
      };
    },
    hovering(index) {
      this.$refs.modalWindow[index].classList.add("hover");
      this.$refs.deleteBtn[index].classList.add("active-delete");
    },
    unhover(index) {
      this.$refs.deleteBtn[index].classList.remove("active-delete");
    },
    slideTransform() {
      const slider = document.querySelector(".pictures");
      slider.style.transform = `translateX(${
        -slider.clientWidth * this.counter
      }px)`;
    },
    nextSlides() {
      if (this.counter >= this.picturesFlickr.length / 9 - 1) return;
      this.counter++;
      this.slideTransform();
    },
    prevSlides() {
      if (this.counter <= 0) return;
      this.counter--;
      this.slideTransform();
    },
    deleteItem(index) {
      this.$refs.pictureCont[index].remove();
      if (this.totalPics < 1.1) this.totalPics;
      this.totalPics--;
    },
  },
};
</script>

<style lang="scss" scoped>
button {
  border: none;
  background: 0 0;
}

.picture-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 1452px;
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

.active-window {
  top: 100% !important;
}

.pictures {
  position: relative;
  display: grid;
  grid-template-rows: auto;
  grid-template-columns: repeat(1000, auto);
  grid-gap: 24px;
  transition: transform 0.4s ease-in-out;
  max-width: 1452px;
  .delete-btn {
    width: 23px;
    height: 27px;
    position: absolute;
    bottom: -60px;
    right: 10px;
    z-index: 10;
    transition: bottom 0.3s ease-in-out;
    &:hover {
      bottom: 7px;
    }
  }

  .active-delete {
    bottom: 7px;
  }

  .picture-container {
    position: relative;
    width: 140px;
    height: 140px;
    margin: 0 auto;
    overflow: hidden;
  }

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    cursor: pointer;
  }

  .inactive-window {
    position: absolute;
    background: rgba(0, 0, 0, 0.5);
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    width: 100%;
    height: 100%;
    transition: top 0.3s ease-in-out;
    cursor: pointer;
  }
}

.preview-btns {
  margin-top: 34px;

  .prevBtn {
    margin-right: 91px;
  }

  .nextBtn {
    margin-left: 91px;
  }

  .prevBtn,
  .nextBtn {
    position: relative;
    top: 50%;
    width: 45px;
    height: 100%;

    img {
      width: 100%;
      height: 100%;
    }
  }

  span {
    display: inline-block;
    font-size: 58px;
    height: 100%;
  }
}

.hover {
  &:hover {
    top: 100px;
  }
}

.upload-btns {
  display: flex;
  min-width: 425px;
  justify-content: space-between;
}
.upload-btn {
  width: 200px;
  height: 35px;
  border-radius: 35px;
  background-color: #252525;
  color: white;
}
</style>

