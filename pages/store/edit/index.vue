<template>
  <div>
    <v-row justify="start">
      <v-col cols="12" md="4">
        <v-card elevation="2" outlined>
          <v-card-text>
            <v-avatar size="128">
              <v-img
                v-if="store.image === null"
                src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676141396/image/profile/profile_sgnfpv.png"
                contain
              ></v-img>
              <v-img
                v-else
                :src="store.image"
                aspect-ratio="1.4"
                max-height="125"
                max-width="110"
                contain
              ></v-img>
            </v-avatar>
            <v-col cols="12" sm="10">
              <v-file-input
                v-model="file"
                truncate-length="15"
                accept="image/png, image/jpeg, image/bmp"
                prepend-icon="mdi-camera"
                label="อัพโหลดรูป"
              ></v-file-input>
              <v-progress-linear
                v-show="loadbar"
                indeterminate
                color="green"
              ></v-progress-linear>
            </v-col>
            <v-col cols="12" sm="10">
              <v-btn
                outlined
                color="primary"
                :disabled="file !== null ? false : true"
                @click="uploadfile"
                >อัพโหลดรูป</v-btn
              >
            </v-col>
            <v-card-title>
              {{ store.store_name }}
            </v-card-title>
            <v-card-subtitle>
              {{ store.store_username }}
            </v-card-subtitle>
            <div class="buttons mt-5">
              <v-btn color="primary" elevation="2" class="primary1">
                แก้ไขข้อมูล
              </v-btn>
            </div>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" md="8">
        <v-card>
          <form @submit.prevent="btnEditSave">
            <v-card-text>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    v-model="StoreData.store_name"
                    label="ชื่อร้าน"
                    prepend-icon="mdi-account-outline"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    v-model="StoreData.store_username"
                    label="ชื่อผู้ใช้"
                    prepend-icon="mdi-account-outline"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    v-model="StoreData.address"
                    label="ที่อยู่"
                    placeholder="บ้านเลขที่, ซอย, ถนน"
                    prepend-icon="mdi-map-marker-outline"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-autocomplete
                    v-model="modelEditDistrictRaw"
                    :items="item_district"
                    label="ตำบล"
                    item-text="district_name"
                    item-value="district_id"
                    prepend-icon="mdi-map-marker-outline"
                    return-object
                    required
                    @change="setEditZipData"
                  ></v-autocomplete>
                </v-col>
                <v-col cols="12">
                  <v-autocomplete
                    v-model="modelEditAmphuresRaw"
                    :items="item_amphure"
                    label="อำเภอ"
                    item-text="amphur_name"
                    item-value="amphur_id"
                    prepend-icon="mdi-map-marker-outline"
                    return-object
                    required
                    @change="setEditDistrictData"
                  ></v-autocomplete>
                </v-col>
                <v-col cols="12">
                  <v-autocomplete
                    v-model="modelEditProvincRaw"
                    :items="provincs"
                    label="จังหวัด"
                    item-text="province_name"
                    item-value="province_id"
                    prepend-icon="mdi-map-marker-outline"
                    return-object
                    required
                    @change="setEditAmphureData"
                  ></v-autocomplete>
                </v-col>
                <v-col cols="12" md="6">
                  <v-text-field
                    v-model="StoreData.tel"
                    label="เบอร์โทร"
                    prepend-icon="mdi-phone-outline"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12" md="6">
                  <v-text-field
                    v-model="StoreData.email"
                    label="อีเมล"
                    prepend-icon="mdi-phone-outline"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-card-actions>
                <v-btn outlined color="warning" type="submit">
                  แก้ไขข้อมูล
                </v-btn>
              </v-card-actions>
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
import provincsData from '~/static/autoprovince1-2/json/provinces.json'
import amphuresData from '~/static/autoprovince1-2/json/amphures.json'
import districtsData from '~/static/autoprovince1-2/json/districts.json'
import zipcodesData from '~/static/autoprovince1-2/json/zipcodes.json'
export default {
  name: 'StoreEdit',
  layout: 'StoreLayout',
  middleware: 'auth',
  data: () => ({
    store: [],
    snackbar: false,
    text: '',
    timeout: 2000,
    loading: false,
    colors: '',
    custom: true,
    StoreData: {
      store_name: '',
      store_username: '',
      address: '',
      sub_district: '',
      district: '',
      province: '',
      tel: '',
      email: '',
    },
    dialog: false,
    file: null,
    loadbar: false,
    store_id: '',
    // address
    // json object to javascript object
    provincs: provincsData,
    amphures: amphuresData,
    districts: districtsData,
    zips: zipcodesData,
    // item autocomplete
    item_amphure: [],
    item_district: [],
    zipcode: '',
    //
    modelEditProvincRaw: [],
    modelEditAmphuresRaw: [],
    modelEditDistrictRaw: [],

    modelEditProvinc: {
      province_id: '',
      province_name: '',
    },
    modelEditAmphure: {
      amphur_id: '',
      amphur_name: '',
    },
    modelEditDistrict: {
      district_id: '',
      district_name: '',
      district_code: '',
    },
  }),
  computed: {
    progress() {
      return Math.min(100, this.userData.tel.length * 10)
    },
    color() {
      return ['accent', 'warning', 'success'][Math.floor(this.progress / 40)]
    },
  },
  async created() {
    await this.getRole()
    await this.getStoreData()
  },
  methods: {
    async getRole() {
      try {
        const response = await this.$axios.get(`/role`)
        response.data.forEach((val) => {
          if (val.store_id !== null) {
            this.store_id = val.store_id
          }
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.error(e)
      }
    },
    async getStoreData() {
      try {
        const response = await this.$axios.get(`/store/${this.store_id}`)

        // eslint-disable-next-line no-console
        response.data.forEach((val) => {
          this.store = val
          this.StoreData.store_name = val.store_name
          this.StoreData.store_username = val.store_username
          this.StoreData.address = val.address
          this.StoreData.sub_district = val.sub_district
          this.StoreData.district = val.district
          this.StoreData.province = val.province
          this.StoreData.tel = val.tel
          this.StoreData.email = val.email
        })
        this.setEditProvincData()
        this.setEditAmphureData()
        this.setEditDistrictData()
        this.setEditZipData()
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
          `/upload/store/${this.store_id}`,
          formData
        )
        setTimeout(() => {
          this.getStoreData()
          location.reload()
          this.file = null
          this.loadbar = false
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loadbar = false
      }
    },
    setEditDefaultData() {
      this.item_amphure = []
      this.item_district = []
      this.zipcode = ''
      this.modelEditProvinc.province_id = ''
      this.modelEditProvinc.province_name = ''
      //
      this.modelEditAmphure.amphur_id = ''
      this.modelEditAmphure.amphur_name = ''
      //
      this.modelEditDistrict.district_id = ''
      this.modelEditDistrict.district_name = ''
      this.modelEditDistrict.district_code = ''

      //
    },
    setEditProvincData() {
      this.provincs.forEach((val) => {
        if (val.province_name === this.StoreData.province) {
          this.modelEditProvincRaw = val
        }
      })
    },
    setEditAmphureData() {
      if (this.modelEditProvincRaw !== null) {
        this.setEditDefaultData()
        this.modelEditProvinc.province_id = this.modelEditProvincRaw.province_id
        this.modelEditProvinc.province_name =
          this.modelEditProvincRaw.province_name
        this.amphures.forEach((val) => {
          if (val.amphur_name === this.StoreData.district) {
            this.modelEditAmphuresRaw = val
          }
        })
        // eslint-disable-next-line no-console
        // console.log(this.modelEditProvinc)
        this.amphures.forEach((val) => {
          if (val.province_id === this.modelEditProvinc.province_id) {
            this.item_amphure.push(val)
            // eslint-disable-next-line no-console
            // console.log(val)
          }
        })
      }
    },
    setEditDistrictData() {
      if (this.modelEditAmphuresRaw !== null) {
        this.item_district = []
        this.modelEditAmphure.amphur_id = this.modelEditAmphuresRaw.amphur_id
        this.modelEditAmphure.amphur_name =
          this.modelEditAmphuresRaw.amphur_name

        this.districts.forEach((val) => {
          if (
            val.district_name === this.StoreData.sub_district &&
            val.amphur_id === this.modelEditAmphure.amphur_id
          ) {
            this.modelEditDistrictRaw = val
            // eslint-disable-next-line no-console
            console.log(val)
          }
        })

        this.districts.forEach((val) => {
          if (val.amphur_id === this.modelEditAmphure.amphur_id) {
            this.item_district.push(val)
            // eslint-disable-next-line no-console
            // console.log(val)
          }
        })
      }
    },
    setEditZipData() {
      if (this.modelEditDistrictRaw !== null) {
        this.modelEditDistrict.district_id =
          this.modelEditDistrictRaw.district_id
        this.modelEditDistrict.district_name =
          this.modelEditDistrictRaw.district_name
        this.modelEditDistrict.district_code =
          this.modelEditDistrictRaw.district_code
      }
    },
    async btnEditSave() {
      this.StoreData.sub_district = this.modelEditDistrict.district_name
      this.StoreData.district = this.modelEditAmphure.amphur_name
      this.StoreData.province = this.modelEditProvinc.province_name
      try {
        const respo = await this.$axios.patch(
          `/store/name/${this.store_id}`,
          this.StoreData
        )
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.snackbar = true
          this.text = 'แก้ไขข้อมูลแล้ว'
          this.getStoreData()
          //   location.reload()
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>