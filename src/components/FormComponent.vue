<template>
  <div>
    <form @submit="checkForm">
      <div class="form-group align-left">
        <label class="left" for="inlineFormInput1"
          >Title&nbsp;
          <span class="red">&#42;</span>
        </label>
        <input
          type="text"
          class="form-control mb-2"
          id="inlineFormInput1"
          v-model="title"
          placeholder="title"
        />
        <div class="text-red-style" v-if="isErrorTitle">Title required.</div>
      </div>

      <div class="form-group align-left">
        <label class="left" for="inlineFormInput2"
          >Description&nbsp;
          <span class="red">&#42;</span>
        </label>
        <textarea
          name="message"
          class="form-control mb-2"
          id="inlineFormInput2"
          v-model="description"
          placeholder="description"
        ></textarea>
        <div class="text-red-style" v-if="isErrorDescription">
          Description required.
        </div>
      </div>

      <div class="text-left mb-3">
        <div class="form-group align-left">Category</div>
        <div class="form-group form-check inline align-left">
          <input
            type="checkbox"
            class="form-check-input"
            id="romantic"
            value="romantic"
            v-model="checkedCategories"
          />
          <label for="romantic" class="form-check-label">Romantic</label>
        </div>
        <div class="form-group form-check inline align-left">
          <input
            type="checkbox"
            class="form-check-input"
            id="comic"
            value="comic"
            v-model="checkedCategories"
          />
          <label for="comic" class="form-check-label">Comic</label>
        </div>
        <div class="form-group form-check inline align-left">
          <input
            type="checkbox"
            class="form-check-input"
            id="adventure"
            value="adventure"
            v-model="checkedCategories"
          />
          <label for="adventure" class="form-check-label">Adventure</label>
        </div>
      </div>

      <div class="form-group align-left">
        <label class="left" for="inlineFormInput2">Cast</label>
        <textarea
          name="message"
          class="form-control mb-2"
          id="inlineFormInput2"
          v-model="cast"
          placeholder="cast"
        ></textarea>
      </div>

      <div class="form-group align-left">
        <label for="inlineFormInput5"
          >Image&nbsp;
          <span class="red">&#42;</span>
        </label>
        <div>
          <input
            type="text"
            class="form-control mb-2 input-width"
            id="inlineFormInput5"
            v-model="image"
            placeholder="image url"
          />
          <span class="span-margin">or</span>
          <input
            type="file"
            class="input-button"
            id="avatar"
            name="avatar"
            @change="onFileChanged"
            accept="image/png, image/jpeg"
          />
        </div>

        <div class="text-red-style" v-if="isErrorImage">Image required.</div>
      </div>

      <div class="form-group align-left">
        <label class="left" for="inlineFormInput4">Trailer</label>
        <input
          type="text"
          class="form-control mb-2"
          id="inlineFormInput4"
          v-model="trailer"
          placeholder="trailer embeded code"
        />
      </div>

      <!-- Default switch -->
      <div class="form-group align-left custom-control custom-switch">
        <input
          type="checkbox"
          class="custom-control-input"
          id="customSwitches"
          v-model="prefer"
        />
        <label class="custom-control-label" for="customSwitches"
          >ADD TO FAVOURITES</label
        >
      </div>

      <div class="col-auto">
        <button type="submit" class="btn btn-primary mb-2">Add Movie</button>
      </div>
    </form>
  </div>
</template>

<script>
import Star from "@/components/Star.vue";
export default {
  name: "FormComponent",
  components: {
    Star
  },
  data: function () {
    return {
      title: null,
      isErrorTitle: false,
      description: null,
      isErrorDescription: false,
      checkedCategories: [],
      cast: null,
      prefer: false,
      image: null,
      isErrorImage: false,
      trailer: null,
      imageBase64: null,
      id: Math.random() * 10,
    };
  },

  methods: {
    onFileChanged(event) {
      const file = event.target.files[0];
      const reader = new FileReader();
      reader.readAsDataURL(file);

      reader.onload = () => {
        // console.info("data read:", reader.result);
        this.imageBase64 = reader.result;
      };

      reader.onerror = (error) => {
        console.error("Error reading image: ", error);
      };
    },

    checkForm: function (event) {
      event.preventDefault();

      if (this.isFormInvalid()) {
        return;
      }
      this.save();
      this.resetForm();
    },

    isFormInvalid() {
      this.compileErrors();
      return this.formHasErrors();
    },

    resetForm() {
      this.title = "";
      this.description = "";
      this.image = "";
    },
    resetErrors() {
      this.isErrorTitle = false;
      this.isErrorDescription = false;
      this.isErrorImage = false;
    },
    compileErrors() {
      this.resetErrors();

      if (!this.title || !this.hasMinLength(this.title, 5)) {
        this.isErrorTitle = true;
      }
      if (!this.description || !this.hasMinLength(this.description, 5)) {
        this.isErrorDescription = true;
      }
      if (!this.image && !this.imageBase64) {
        this.isErrorImage = true;
      }
    },
    formHasErrors() {
      var risultato = false;
      if (this.isErrorTitle || this.isErrorDescription || this.isErrorImage) {
        risultato = true;
      }
      return risultato;
    },
    hasMinLength(paramToCompare, length) {
      return paramToCompare.length >= length;
    },
    save() {
      const film = {
        title: this.title,
        description: this.description,
        categories: this.checkedCategories,
        cast: this.cast,
        prefer: this.prefer,
        image: this.image ? this.image : this.imageBase64,
        trailerPath: this.trailer,
        id: this.id,
      };

      if (this.title) {
        this.$store.dispatch("filmsStore/callActionAddFilm", film);
        this.$router.push("../movie");
      } else {
        console.log("No update Film");
      }
    },
  },
};
</script>

<style scoped>
.left {
  float: left;
}
.align-left {
  text-align: left;
}
.text-red-style {
  color: red;
}

.text-left {
  text-align: left;
}

.inline {
  display: inline;
  margin-right: 1rem;
}

.input-width {
  width: 50%;
}
.span-margin {
  margin: 10px;
  display: block;
}
.input-button {
  line-height: 2.1;
  margin-bottom: 10px;
}
.switch-margin {
  margin: 40px;
}

.red {
  color: red;
}
</style>