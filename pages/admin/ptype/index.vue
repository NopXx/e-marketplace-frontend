<template>
  <div>
    <v-row>
      <!-- data-table -->
      <v-col cols="12" md="6">
        <v-card>
          <v-card-text>
            <v-card-title>
              ประเภทสินค้า
              <v-spacer></v-spacer>
              <v-btn
                outlined
                color="primary"
                class="mx-2"
                @click="
                  showAdd = true
                  showEdit = false
                "
              >
                เพิ่มข้อมูล
              </v-btn>
              <v-btn
                v-show="showAdd === true || showEdit === true"
                outlined
                color="warning"
                @click="
                  showAdd = false
                  showEdit = false
                "
              >
                ปิด
              </v-btn>
            </v-card-title>

            <v-data-table
              :headers="headers"
              :items="ptype"
              :search="search"
              no-data-text="ไม่มีข้อมูล"
              class="elevation-1 mt-2"
              :loading="loadingdata"
            >
              <template #top>
                <v-text-field
                  v-model="search"
                  append-icon="mdi-magnify"
                  label="ค้นหาข้อมูล"
                  class="mb-2"
                  single-line
                  hide-details
                ></v-text-field>
              </template>
              <!-- image -->
              <template #[`item.image`]="{ item }">
                <v-img
                  v-if="item['image'] !== null"
                  :src="item['image']"
                  aspect-ratio="1"
                  max-height="125"
                  contain
                >
                  <template #placeholder>
                    <v-row
                      class="fill-height ma-0"
                      align="center"
                      justify="center"
                    >
                      <v-progress-circular
                        indeterminate
                        color="grey lighten-5"
                      ></v-progress-circular>
                    </v-row>
                  </template>
                </v-img>
                <v-img
                  v-else
                  src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
                  aspect-ratio="1"
                  max-height="100"
                  contain
                >
                  <template #placeholder>
                    <v-row
                      class="fill-height ma-0"
                      align="center"
                      justify="center"
                    >
                      <v-progress-circular
                        indeterminate
                        color="grey lighten-5"
                      ></v-progress-circular>
                    </v-row>
                  </template>
                </v-img>
              </template>
              <!-- action -->
              <template #[`item.actions`]="{ item }">
                <v-row>
                  <v-col cols="12" md="6">
                    <v-btn
                      outlined
                      color="warning"
                      @click="setPtypeID(item.product_type_id)"
                    >
                      <v-icon>mdi-pencil-outline</v-icon>
                    </v-btn>
                  </v-col>
                </v-row>
              </template>
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-col>
      <!-- add-data & edit-data -->
      <v-col cols="12" md="6">
        <!-- add -->
        <v-card v-show="showAdd">
          <form @submit.prevent="btnAddSave">
            <v-card-text>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    v-model="ptypeAdd.name"
                    label="ประเภท"
                    prepend-icon="mdi-account-outline"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-btn type="submit" outlined color="primary"> บันทึก </v-btn>
                </v-col>
              </v-row>
            </v-card-text>
          </form>
        </v-card>
        <!-- edit -->
        <v-card v-show="showEdit">
          <form @submit.prevent="btnEditSave">
            <v-card-text>
              <v-row>
                <!-- image -->
                <v-col cols="12">
                  <v-img
                    v-if="ptypeEdit.image !== null"
                    :src="ptypeEdit.image"
                    aspect-ratio="1"
                    max-height="125"
                    contain
                  >
                    <template #placeholder>
                      <v-row
                        class="fill-height ma-0"
                        align="center"
                        justify="center"
                      >
                        <v-progress-circular
                          indeterminate
                          color="grey lighten-5"
                        ></v-progress-circular>
                      </v-row>
                    </template>
                  </v-img>
                  <v-img
                    v-else
                    src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
                    aspect-ratio="1"
                    max-height="100"
                    contain
                  >
                    <template #placeholder>
                      <v-row
                        class="fill-height ma-0"
                        align="center"
                        justify="center"
                      >
                        <v-progress-circular
                          indeterminate
                          color="grey lighten-5"
                        ></v-progress-circular>
                      </v-row>
                    </template>
                  </v-img>
                </v-col>
                <!-- upload file -->
                <v-col cols="12" md="6">
                  <v-file-input
                    v-model="file"
                    truncate-length="15"
                    accept="image/png, image/jpeg, image/jpg"
                    prepend-icon="mdi-camera"
                    label="อัพโหลดรูป"
                  ></v-file-input>
                </v-col>
                <v-col cols="12" md="6">
                  <v-btn
                    outlined
                    color="primary"
                    :disabled="file !== null ? false : true"
                    :loading="loadbar"
                    @click="uploadfile"
                    >อัพโหลดรูป</v-btn
                  >
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    v-model="ptypeEdit.name"
                    label="ประเภท"
                    prepend-icon="mdi-account-outline"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-btn type="submit" outlined color="primary"> บันทึก </v-btn>
                </v-col>
              </v-row>
            </v-card-text>
          </form>
        </v-card>
      </v-col>
    </v-row>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ text }}
      <template #action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          ปิด
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  name: 'ProductType',
  layout: 'AdminLayout',
  data() {
    return {
      ptype: [],
      search: '',
      loadingdata: false,
      headers: [
        {
          text: 'รูปประเภทสินค้า',
          value: 'image',
          sortable: false,
        },
        {
          text: 'ประเภทสินค้า',
          value: 'product_type_name',
        },
        { text: 'ตัวเลือก', value: 'actions', sortable: false },
      ],
      ptype_id: '',
      showEdit: false,
      showAdd: false,
      loadbar: false,
      file: null,
      ptypeEdit: {
        name: '',
        image: null,
      },
      ptypeAdd: {
        name: '',
      },
      snackbar: false,
      timeout: 3000,
      text: '',
    }
  },
  async created() {
    await this.getPType()
  },
  methods: {
    async getPType() {
      this.loadingdata = true
      try {
        const respo = await this.$axios.get('/product-type')

        setTimeout(() => {
          this.ptype = respo.data
          this.loadingdata = false
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loadingdata = false
      }
    },
    async setPtypeID(id) {
      this.showEdit = true
      this.showAdd = false
      this.ptype_id = id
      try {
        const respo = await this.$axios.get(`/product-type/${id}`)
        respo.data.forEach((val) => {
          this.ptypeEdit.name = val.product_type_name
          this.ptypeEdit.image = val.image
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async uploadfile() {
      this.loadbar = true
      try {
        const img = this.file
        const formData = new FormData()
        formData.append('image', img)
        this.loading1 = true
        const response = await this.$axios.post(
          `/upload/product-type/${this.ptype_id}`,
          formData
        )
        setTimeout(async () => {
          await this.getPType()
          await this.setPtypeID(this.ptype_id)
          this.file = null
          this.loadbar = false
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loadbar = false
      }
    },
    async btnEditSave() {
      try {
        const respo = await this.$axios.patch(
          `/product-type/${this.ptype_id}`,
          {
            name: this.ptypeEdit.name,
          }
        )
        setTimeout(async () => {
          await this.getPType()
          await this.setPtypeID(this.ptype_id)
          this.snackbar = true
          this.text = 'แก้ไขข้อมูลแล้ว'
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async btnAddSave() {
      try {
        const respo = await this.$axios.post(`/product-type`, {
          name: this.ptypeAdd.name,
        })
        setTimeout(async () => {
          await this.getPType()
          this.snackbar = true
          this.text = 'บันทึกข้อมูลแล้ว'
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e.response)
        if (e.response.status) {
            this.snackbar = true
          this.text = 'มีประเภทสินค้านี้แล้ว'
        }

      }
    },
  },
}
</script>

<style lang="scss">
tbody {
  tr {
    background-color: transparent !important;
  }
}
</style>