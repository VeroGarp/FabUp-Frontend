<template>
  <div id="main">
    <v-container class="fill-height mt-1">
      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="2" class="text-left">
          <v-btn text to="/my-ads">
            <v-icon>mdi-chevron-left</v-icon>
          </v-btn>
        </v-col>
        <v-col cols="10" class="text-right">
          <h1 class="fabup-title-font display-1">Create ad</h1>
        </v-col>
      </v-row>

      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="12">
          <v-text-field
            v-model="title"
            label="Title"
            outlined
            dense
            validate-on-blur
            :rules="[rules.required]"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="12">
          <v-text-field
            v-model="price"
            label="Price in euro"
            outlined
            dense
            validate-on-blur
            :rules="[rules.required]"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="12">
          <v-text-field
            v-model="location"
            label="Location"
            outlined
            dense
            validate-on-blur
            :rules="[rules.required]"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="12">
          <v-text-field
            v-model="description"
            label="Description"
            outlined
            dense
            validate-on-blur
            :rules="[rules.required]"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="12">
          <v-overflow-btn
            v-model="selectedCategory"
            class="my-2"
            :items="categories"
            item-text="category"
            item-value="_id"
            label="Select Category"
            target="#dropdown-example"
          ></v-overflow-btn>
        </v-col>
      </v-row>

      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="12">
          <label for="file">
            <v-alert type="warning" icon="mdi-image-plus" color="#f9a825" dense
              >UPLOAD PHOTO</v-alert
            >
          </label>
        </v-col>
      </v-row>

      <v-row align="center" justify="center" class="mx-0">
        <v-col cols="12">
          <v-btn large width="100%" tile color="primary" @click="postAd"
            >POST AD</v-btn
          >
        </v-col>
      </v-row>
    </v-container>
    <input
      id="file"
      ref="file"
      name="file"
      type="file"
      @change="handleFileUpload"
    />
  </div>
</template>

<script>
import axios from '~/plugins/axios'
export default {
  data() {
    return {
      file: '',
      title: '',
      price: '',
      location: '',
      description: '',
      rules: {
        required: v => !!v || 'Item is required'
      },
      categories: [],
      selectedCategory: ''
    }
  },
  mounted() {
    this.getAllCategories()
  },
  methods: {
    handleFileUpload() {
      const reader = new FileReader()
      reader.onloadend = () => {
        this.file = reader.result
      }
      reader.onerror = Promise.reject
      reader.readAsDataURL(this.$refs.file.files[0])
    },
    async postAd() {
      try {
        const ad = {
          title: this.title,
          price: this.price,
          location: this.location,
          description: this.description,
          image: this.file,
          category: this.selectedCategory,
          author: this.$store.state.email
        }
        this.$store.commit('spinnerOn')
        await axios.post('ads/create', ad)
        this.$store.commit('spinnerOff')
        this.$router.push('/my-ads')
      } catch (e) {
        console.error(e.response.data.message)
      }
    },
    async getAllCategories() {
      const response = await axios.get('/categories')
      this.categories = response.data
    }
  }
}
</script>

<style scoped lang="scss">
#file {
  visibility: hidden;
}
</style>
