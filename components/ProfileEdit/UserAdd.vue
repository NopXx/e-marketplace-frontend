<template>
  <div>
    <v-row>
      <v-col cols="12" md="6">
        <v-card>
          <v-card-title> ที่อยู่ </v-card-title>
          <v-card-text>
            <v-expansion-panels>
              <v-expansion-panel v-for="(item, i) in userAddData" :key="i">
                <v-expansion-panel-header disable-icon-rotate>
                  <p>
                    <v-chip outlined pill>{{ item.address_title }}</v-chip>
                    - {{ item.address }}
                  </p>
                  <template #actions>
                    <v-icon v-if="item.address_default === 1" color="teal">
                      mdi-check
                    </v-icon>
                    <v-icon v-else color="primary"> $expand </v-icon>
                  </template>
                </v-expansion-panel-header>
                <v-expansion-panel-content class="text-body-1">
                  {{ item.fullname }}
                  <br />
                  บ้านเลขที่: <b>{{ item.address }}</b>
                  <br />
                  ตำบล: <b>{{ item.sub_district }}</b> - อำเภอ:
                  <b>{{ item.district }}</b>
                  <br />
                  จังหวัด: <b>{{ item.province }}</b>
                  <br />
                  รหัสไปรษณีย์: <b>{{ item.zipcode }}</b>
                  <br />
                  เบอร์โทร: <b>{{ item.tel }}</b>
                  <br />
                  <div class="mt-4">
                    <v-btn
                      v-if="item.address_default === 1"
                      outlined
                      color="teal"
                    >
                      <v-icon>mdi-pin-outline</v-icon>
                      ที่อยู่เริ่มต้น
                    </v-btn>
                    <v-btn
                      v-else
                      outlined
                      color="info"
                      @click="setDefault(item.user_a_id)"
                    >
                      <v-icon>mdi-pin-outline</v-icon>
                      เปลี่ยนที่อยู่เริ่มต้น
                    </v-btn>
                    <v-btn
                      outlined
                      color="warning"
                      @click="btnEdit(item.user_a_id)"
                    >
                      <v-icon>mdi-file-edit-outline</v-icon>
                      แก้ไข
                    </v-btn>
                    <v-btn
                      v-show="item.address_default !== 1"
                      outlined
                      color="error"
                      @click="deleteAddress(item.user_a_id)"
                    >
                      <v-icon>mdi-delete-circle-outline</v-icon>
                      ลบ
                    </v-btn>
                  </div>
                </v-expansion-panel-content>
              </v-expansion-panel>
            </v-expansion-panels>
          </v-card-text>
          <v-card-actions>
            <v-btn
              v-if="!showUserEditAdd"
              outlined
              color="primary"
              @click="showUserAdd = !showUserAdd"
            >
              เพิ่มข้อมูล
            </v-btn>
            <v-btn
              v-else
              outlined
              color="warning"
              @click="showUserEditAdd = !showUserEditAdd"
            >
              แก้ไขข้อมูล
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col v-if="showUserAdd === false && showUserEditAdd === false" cols="12" md="6">
        <theme-changer />
      </v-col>
      <v-col v-show="showUserAdd" cols="12" md="6">
        <!-- form add -->
        <v-row>
          <v-col cols="12">
            <v-card>
              <form @submit.prevent="btnSave">
                <v-card-text>
                  <v-row>
                    <v-col cols="12">
                      <v-select
                        v-model="userAdd.address_title"
                        :items="items_title"
                        attach
                        chips
                        label="ชื่อที่อยู่"
                        placeholder="เช่น บ้าน, ที่ทำงาน"
                        prepend-icon="mdi-tag-outline"
                        required
                      ></v-select>
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        v-model="userAdd.fullname"
                        label="ชื่อ - นามสกุล"
                        prepend-icon="mdi-account-outline"
                        required
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        v-model="userAdd.address"
                        label="ที่อยู่"
                        placeholder="บ้านเลขที่, ซอย, ถนน"
                        prepend-icon="mdi-map-marker-outline"
                        required
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                      <v-autocomplete
                        v-model="modelDistrictRaw"
                        :items="item_district"
                        label="ตำบล"
                        item-text="district_name"
                        item-value="district_id"
                        prepend-icon="mdi-map-marker-outline"
                        return-object
                        required
                        @change="setZipData"
                      ></v-autocomplete>
                    </v-col>
                    <v-col cols="12">
                      <v-autocomplete
                        v-model="modelAmphuresRaw"
                        :items="item_amphure"
                        label="อำเภอ"
                        item-text="amphur_name"
                        item-value="amphur_id"
                        prepend-icon="mdi-map-marker-outline"
                        return-object
                        required
                        @change="setDistrictData"
                      ></v-autocomplete>
                    </v-col>
                    <v-col cols="12">
                      <v-autocomplete
                        v-model="modelProvincRaw"
                        :items="provincs"
                        label="จังหวัด"
                        item-text="province_name"
                        item-value="province_id"
                        prepend-icon="mdi-map-marker-outline"
                        return-object
                        required
                        @change="setAmphureData"
                      ></v-autocomplete>
                    </v-col>
                    <v-col cols="12" md="6">
                      <v-text-field
                        v-model="zipcode"
                        label="รหัสไปรษณีย์"
                        prepend-icon="mdi-mailbox-open-outline"
                        required
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" md="6">
                      <v-text-field
                        v-model="userAdd.tel"
                        label="เบอร์โทร"
                        prepend-icon="mdi-phone-outline"
                        required
                      ></v-text-field>
                    </v-col>
                  </v-row>
                  <v-card-actions>
                    <v-btn outlined color="primary" type="submit">
                      บันทึกข้อมูล
                    </v-btn>
                  </v-card-actions>
                </v-card-text>
              </form>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
      <v-col v-show="showUserEditAdd" cols="12" md="6">
        <!-- form edit -->
        <v-row>
          <v-col cols="12">
            <v-card>
              <form @submit.prevent="btnEditSave">
                <v-card-text>
                  <v-row>
                    <v-col cols="12">
                      <v-select
                        v-model="userEditAdd.address_title"
                        :items="items_title"
                        attach
                        chips
                        label="ชื่อที่อยู่"
                        placeholder="เช่น บ้าน, ที่ทำงาน"
                        prepend-icon="mdi-tag-outline"
                        required
                      ></v-select>
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        v-model="userEditAdd.fullname"
                        label="ชื่อ - นามสกุล"
                        prepend-icon="mdi-account-outline"
                        required
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        v-model="userEditAdd.address"
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
                        v-model="userEditAdd.zipcode"
                        label="รหัสไปรษณีย์"
                        prepend-icon="mdi-mailbox-open-outline"
                        required
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" md="6">
                      <v-text-field
                        v-model="userEditAdd.tel"
                        label="เบอร์โทร"
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
      </v-col>
    </v-row>

    <v-snackbar v-model="snackbar">
      {{ mesText }}

      <template #action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
          ปิด
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import ThemeChanger from '../ThemeChanger.vue'
import provincsData from '~/static/autoprovince1-2/json/provinces.json'
import amphuresData from '~/static/autoprovince1-2/json/amphures.json'
import districtsData from '~/static/autoprovince1-2/json/districts.json'
import zipcodesData from '~/static/autoprovince1-2/json/zipcodes.json'
export default {
  name: 'UserAddressComponent',
  components: { ThemeChanger },
  middleware: 'auth',
  data() {
    return {
      showUserAdd: false,
      showUserEditAdd: false,
      // json object to javascript object
      provincs: provincsData,
      amphures: amphuresData,
      districts: districtsData,
      zips: zipcodesData,
      // item autocomplete
      item_amphure: [],
      item_district: [],
      zipcode: '',
      items_title: ['บ้าน', 'ที่ทำงาน', 'หอพัก', 'โรงเรียน'],
      // autocomplete return value
      modelProvincRaw: [],
      modelAmphuresRaw: [],
      modelDistrictRaw: [],

      modelProvinc: {
        province_id: '',
        province_name: '',
      },
      modelAmphure: {
        amphur_id: '',
        amphur_name: '',
      },
      modelDistrict: {
        district_id: '',
        district_name: '',
        district_code: '',
      },

      userAdd: {
        address_title: '',
        fullname: '',
        address: '',
        sub_district: '',
        district: '',
        province: '',
        zipcode: '',
        tel: '',
      },

      // edit
      user_a_id: '',
      userEditAdd: {
        address_title: '',
        fullname: '',
        address: '',
        sub_district: '',
        district: '',
        province: '',
        zipcode: '',
        tel: '',
      },
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
      //
      mesText: '',
      snackbar: false,
      userAddData: [],
    }
  },
  async created() {
    await this.getUserAdd()
    await this.getUserData()
  },
  methods: {
    setDefaultData() {
      this.item_amphure = []
      this.item_district = []
      this.zipcode = ''
      this.modelProvinc.province_id = ''
      this.modelProvinc.province_name = ''
      //
      this.modelAmphure.amphur_id = ''
      this.modelAmphure.amphur_name = ''
      //
      this.modelDistrict.district_id = ''
      this.modelDistrict.district_name = ''
      this.modelDistrict.district_code = ''

      //
    },
    setAmphureData() {
      this.setDefaultData()
      this.modelProvinc.province_id = this.modelProvincRaw.province_id
      this.modelProvinc.province_name = this.modelProvincRaw.province_name
      // eslint-disable-next-line no-console
      // console.log(this.modelProvinc)
      this.amphures.forEach((val) => {
        if (val.province_id === this.modelProvinc.province_id) {
          this.item_amphure.push(val)
          // eslint-disable-next-line no-console
          // console.log(val)
        }
      })
    },
    setDistrictData() {
      this.item_district = []
      this.modelAmphure.amphur_id = this.modelAmphuresRaw.amphur_id
      this.modelAmphure.amphur_name = this.modelAmphuresRaw.amphur_name
      this.districts.forEach((val) => {
        if (val.amphur_id === this.modelAmphure.amphur_id) {
          this.item_district.push(val)
          // eslint-disable-next-line no-console
          // console.log(val)
        }
      })
    },
    setZipData() {
      this.zipcode = ''
      this.modelDistrict.district_id = this.modelDistrictRaw.district_id
      this.modelDistrict.district_name = this.modelDistrictRaw.district_name
      this.modelDistrict.district_code = this.modelDistrictRaw.district_code
      this.zips.forEach((val) => {
        if (val.district_code === this.modelDistrict.district_code) {
          this.zipcode = val.zipcode_name
          // eslint-disable-next-line no-console
          console.log(val)
        }
      })
    },
    async getUserData() {
      try {
        const respo = await this.$axios.get('/user')
        respo.data.forEach((val) => {
          this.userAdd.tel = val.tel
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async btnSave() {
      try {
        this.userAdd.sub_district = this.modelDistrict.district_name
        this.userAdd.district = this.modelAmphure.amphur_name
        this.userAdd.province = this.modelProvinc.province_name
        this.userAdd.zipcode = this.zipcode
        // eslint-disable-next-line no-console
        console.log(this.userAdd)
        const respo = await this.$axios.post('/useradd', this.userAdd)
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.snackbar = true
          this.mesText = 'บันทึกข้อมูลแล้ว'
          this.setDefaultData()
          this.userAdd = ''
          this.getUserData()
          this.getUserAdd()
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getUserAdd() {
      try {
        const respo = await this.$axios.get('/useradd')
        this.userAddData = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async setDefault(useraddressid) {
      try {
        const respo = await this.$axios.patch(
          `/useradd/default/${useraddressid}`
        )
        setTimeout(() => {
          this.snackbar = true
          this.mesText = 'แก้ไขข้อมูลแล้ว'
          this.getUserAdd()
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async deleteAddress(useraddressid) {
      try {
        if (confirm('ต้องการลบที่อยู่นี้ ??')) {
          const respo = await this.$axios.delete(`/useradd/${useraddressid}`)
          setTimeout(() => {
            this.snackbar = true
            this.mesText = 'ลบข้อมูลแล้ว'
            this.getUserAdd()
          }, respo)
        }
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async btnEdit(useraddressid) {
      this.user_a_id = useraddressid
      this.showUserAdd = false
      this.showUserEditAdd = true
      try {
        const respo = await this.$axios.get(`/useradd/${useraddressid}`)
        // eslint-disable-next-line no-console
        respo.data.forEach((val) => {
          this.userEditAdd.address_title = val.address_title
          this.userEditAdd.address = val.address
          this.userEditAdd.fullname = val.fullname
          this.userEditAdd.tel = val.tel
          this.userEditAdd.province = val.province
          this.userEditAdd.district = val.district
          this.userEditAdd.sub_district = val.sub_district
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
        if (val.province_name === this.userEditAdd.province) {
          this.modelEditProvincRaw = val
        }
      })
    },
    setEditAmphureData() {
      this.setEditDefaultData()
      this.modelEditProvinc.province_id = this.modelEditProvincRaw.province_id
      this.modelEditProvinc.province_name =
        this.modelEditProvincRaw.province_name
      this.amphures.forEach((val) => {
        if (val.amphur_name === this.userEditAdd.district) {
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
    },
    setEditDistrictData() {
      this.item_district = []
      this.modelEditAmphure.amphur_id = this.modelEditAmphuresRaw.amphur_id
      this.modelEditAmphure.amphur_name = this.modelEditAmphuresRaw.amphur_name

      this.districts.forEach((val) => {
        if (val.district_name === this.userEditAdd.sub_district && val.amphur_id === this.modelEditAmphure.amphur_id) {
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
    },
    setEditZipData() {
      this.zipcode = ''
      this.modelEditDistrict.district_id = this.modelEditDistrictRaw.district_id
      this.modelEditDistrict.district_name = this.modelEditDistrictRaw.district_name
      this.modelEditDistrict.district_code = this.modelEditDistrictRaw.district_code
      this.zips.forEach((val) => {
        if (val.district_code === this.modelEditDistrict.district_code) {
          this.userEditAdd.zipcode = val.zipcode_name
          // eslint-disable-next-line no-console
          // console.log(val)
        }
      })
    },
    async btnEditSave() {
      this.userEditAdd.sub_district = this.modelEditDistrict.district_name
      this.userEditAdd.district = this.modelEditAmphure.amphur_name
      this.userEditAdd.province = this.modelEditProvinc.province_name
      // eslint-disable-next-line no-console
      console.log(this.userEditAdd)
      try {
        const respo = await this.$axios.patch(`/useradd/${this.user_a_id}`, this.userEditAdd)
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.snackbar = true
          this.mesText = 'แก้ไขข้อมูลแล้ว'
          this.showUserEditAdd = false
          this.getUserAdd()
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
      
    },
  },
}
</script>