<template>
  <div>
    <div class="wrapper">
      <h1>
        Create Order
      </h1>
      <div class="order-wrapper">
        <a-form-model :layout="form.layout" :model="form" :rules="rules" ref="order">
          <a-row>
            <a-col :span="8">
              <h2>Detail</h2>
            </a-col>
            <a-col :span="16" style="padding-right:4em">
              <a-row>
                <a-form-model-item prop="name" ref="name">
                  <span>
                    Name*
                  </span>
                  <!-- <a-input v-model="form.name" placeholder="Name" /> -->
                  <a-select v-model="form.name"
                    placeholder="Select name"
                    @change="inputName"
                  >
                    <a-select-option v-for="opt in option.name" :key="opt.id">
                      {{ opt.employee_name }}
                    </a-select-option>
                  </a-select>
                </a-form-model-item>
              </a-row>
              <a-row>
                <a-form-model-item prop="distribution" ref="distribution">
                  <span>
                    Distribution Center*
                  </span>
                  <a-select v-model="form.distribution"
                    :placeholder="form.distributionDisable ? 'No Data Available' : 'Select Distribution Center'"
                    :disabled="form.distributionDisable"
                    @change="inputDistribution"
                  >
                    <a-select-option v-for="opt in option.distribution" :key="opt.id">
                      {{ opt.name }}
                    </a-select-option>
                  </a-select>
                </a-form-model-item>
              </a-row>
              <div v-if="!form.distributionDisable && form.distribution">
                <a-row :gutter="32">
                  <a-col :span="12">
                    <a-form-model-item prop="paymentType" ref="paymentType">
                      <span>
                        Payment Type*
                      </span>
                      <a-select v-model="form.paymentType"
                        placeholder="Select payment type"
                        @change="inputPayment"
                      >
                        <a-select-option v-for="opt in option.paymentType" :key="opt.id">
                          {{ opt.name }}
                        </a-select-option>
                      </a-select>
                    </a-form-model-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-model-item prop="expiredDate" ref="expiredDate">
                      <span>
                        Expired Date*
                      </span>
                      <a-date-picker style="width:100%" v-model="form.expiredDate" placeholder="Expired Date" @change="inputExpiredDate"/>
                    </a-form-model-item>
                  </a-col>
                </a-row>
                <a-row>
                  <a-form-model-item label="Notes">
                    <a-textarea
                      v-model="form.notes"
                      :rows="4"
                    />
                  </a-form-model-item>
                </a-row>
              </div>
            </a-col>
          </a-row>
          <a-divider/>
          <a-row v-if="!form.distributionDisable && form.distribution">
            <a-col :span="8">
              <h2>Products</h2>
            </a-col>
            <a-col :span="16" style="padding-right:4em">
              <div v-for="(t, index) in form.products" :key="index">
                <a-row type="flex" :gutter="32">
                  <a-col v-if="index != 0" :span="1" class="flex-center">
                    <span>
                      <a-icon type="minus-circle" @click="removeProduct(index, t.product, t.unit)" />
                    </span>
                  </a-col>
                  <a-col :span="index != 0 ? 15 : 16">
                    <a-form-model-item :rules="rules.product" :prop="'products.' + index + '.product'">
                      <span>
                        Product*
                      </span>
                      
                      <a-select v-model="t.product"
                        placeholder="Select product"
                        @change="inputProduct(t.product, index)"
                      >
                        <a-select-option v-for="opt in option.products" :key="opt.id">
                          {{ opt.name }}
                        </a-select-option>
                      </a-select>
                    </a-form-model-item>
                  </a-col>
                  <a-col :span="8">
                    <a-form-model-item :rules="rules.unit" :prop="'products.' + index + '.unit' ">
                      <span>
                        Unit*
                      </span>
                      <a-select v-model="t.unit"
                        :placeholder="t.unitDisabled ? 'No Data Available' : 'Select Unit'"
                        :disabled="t.unitDisabled"
                        @change="selectUnit(t.unit, index, t.product)"
                      >
                        <a-select-option v-for="opt in option.unit" :key="opt.id">
                          {{ opt.name }}
                        </a-select-option>
                      </a-select>
                    </a-form-model-item>
                  </a-col>
                </a-row>

                <a-row :gutter="32">
                  <a-col :span="6">
                    <a-form-model-item :rules="rules.qty" :prop="'products.' + index + '.qty' ">
                      <span>
                        Quantity*
                      </span>
                      <a-input v-model="t.qty" placeholder="Quantity" @blur="inputQty(index)"/>
                    </a-form-model-item>
                  </a-col>
                  <a-col :span="6">
                    <a-form-model-item>
                      <span>
                        Price*
                      </span>
                      <a-input v-model="t.price" placeholder="Price" />
                    </a-form-model-item>
                  </a-col>
                  <a-col :span="12">
                    <a-form-model-item >
                      <span>
                        Total Price*
                      </span>
                      <a-input v-model="t.totalPrice" placeholder="Total Price" :disabled="true" />
                    </a-form-model-item>
                  </a-col>
                </a-row>

                <a-row :gutter="32">
                  <a-col :span="12"></a-col>
                  <a-col :span="12" style="text-align:right;">
                    <a-row style="border-top: 3px solid #e5e5e5;">
                      <a-col :span="12" style="text-align:left;">
                        <span>Total Nett Price</span>
                      </a-col>
                      <a-col :span="12" style="text-align:right;">
                        <span>{{ formatThousand(t.totalPrice) }}</span>
                      </a-col>
                    </a-row>
                  </a-col>
                </a-row>
              </div>
              <a-button @click="addNewProduct">
                NEW ITEM
              </a-button>
              <a-row :gutter="32" style="margin-top:5em;">
                  <a-col :span="12"></a-col>
                  <a-col :span="12" style="text-align:right;">
                    <a-row style="border-top: 3px solid #e5e5e5;">
                      <a-col :span="12" style="text-align:left;">
                        <span>Total</span>
                      </a-col>
                      <a-col :span="12" style="text-align:right;">
                        <span>{{ formatThousand(calcTotal) }}</span>
                      </a-col>
                    </a-row>
                  </a-col>
                </a-row>
            </a-col>
          </a-row>
          <a-divider v-if="!form.distributionDisable && form.distribution"/>
          <a-row style="text-align:right;">
            <a-button>
              Cancel
            </a-button>
            <a-button :disabled="isConfirm" @click.prevent="handleSubmit">
              Confirm
            </a-button>
          </a-row>
        </a-form-model>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      isConfirm: true,
      isCheck: false,
      form : {
        layout: 'vertical',
        name: undefined,
        distribution: undefined,
        distributionDisable: true,
        paymentType: undefined,
        expiredDate: '',
        notes: '',
        product: undefined,
        unit: undefined,
        unitDisabled: true,
        qty: 0,
        price: 0,
        totalPrice: 0,
        products : [
          {
            id: 1,
            product: undefined,
            unit: undefined,
            unitDisabled: true,
            qty: 0,
            price: 0,
            totalPrice: 0,
          }
        ],
      },
      option: {
        name: [],
        // name: [
        //   {
        //     id:1,
        //     employee_name:"Tiger Nixon",
        //     employee_salary:320800,
        //     employee_age:61,
        //     profile_image:""
        //   },
        //   {
        //     id:2,
        //     employee_name:"Garrett Winter",
        //     employee_salary:320800,
        //     employee_age:51,
        //     profile_image:""
        //   },
        //   {
        //     id:3,
        //     employee_name:"Ashton Cok",
        //     employee_salary:320800,
        //     employee_age:55,
        //     profile_image:""
        //   },
        //   {
        //     id:4,
        //     employee_name:"Cedric Kelly",
        //     employee_salary:320800,
        //     employee_age:64,
        //     profile_image:""
        //   },
        //   {
        //     id:5,
        //     employee_name:"Airi Satou",
        //     employee_salary:320800,
        //     employee_age:59,
        //     profile_image:""
        //   },
        //   {
        //     id:6,
        //     employee_name:"Brielle Williamson",
        //     employee_salary:320800,
        //     employee_age:56,
        //     profile_image:""
        //   }
        // ],
        distribution : [
          {
            id: 1,
            name:"DC Tangerang"
          },
          {
            id: 2,
            name:"DC Cikarang"
          }
        ],
        paymentType : [
          {
            id: 1,
            name:"Cash H+1"
          },
          {
            id: 2,
            name:"Cash H+3"
          },
          {
            id: 3,
            name:"Cash H+7"
          },
          {
            id: 4,
            name:"Transfer H+1"
          },
          {
            id: 5,
            name:"Transfer H+3"
          },
          {
            id: 6,
            name:"Transfer H+7"
          }
        ],
        unit : [
          {
            id: 1,
            name:""
          }
        ],
        products : [
          {
            id: 1,
            name:"Danone",
            units:[
              {
                id: 1,
                name: "Karton",
                price: 100000
              },
              {
                id: 2,
                name: "Pcs",
                price: 10000
              },
              {
                id: 3,
                name: "Pak",
                price: 75000
              },
            ]
          },
          {
            id: 2,
            name:"Nestle",
            units:[
              {
                id: 1,
                name: "Karton",
                price: 100000
              },
              {
                id: 2,
                name: "Pcs",
                price: 10000
              }
            ]
          },
          {
            id: 3,
            name:"Le Minerale",
            units:[
              {
                id: 1,
                name: "Karton",
                price: 100000
              },
              {
                id: 2,
                name: "Pcs",
                price: 10000
              }
            ]
          }
        ],
      },
      rules: {
        name: [
          {
            required: true,
            message: 'Please fill this column',
            trigger: 'change'
          }
        ],
        distribution: [
          {
            required: true,
            message: 'Please fill this column',
            trigger: 'change'
          }
        ],
        paymentType: [
          {
            required: true,
            message: 'Please fill this column',
            trigger: 'change'
          }
        ],
        expiredDate: [
          {
            type: 'object',
            required: true,
            message: 'Please fill this column',
          }
        ],
        product: [
          {
            required: true,
            message: 'Please fill this column',
            trigger: 'change'
          }
        ],
        unit: [
          {
            required: true,
            message: 'Please fill this column',
            trigger: 'change'
          }
        ],
        qty: [
          {
            required: true,
            message: 'Quantity tidak boleh 0',
            trigger: 'change'
          },
          {
            validator: (rule, value, callback) => {
              if (value > 0) {
                callback()
                return
              }
              callback(new Error('Quantity tidak boleh 0')
              )
            }
          }
        ],
      }
    }
  },
  mounted (){
    this.fetchData()
  },
  computed: {
    calcTotal (){
      let result = 0

      this.form.products.forEach(e => {
        result += e.totalPrice
      });
      return result
    }
  },
  watch: {
    isCheck(){
      this.$refs.order.validate((valid) => {
        if(valid){
          this.isConfirm = false
        } else {
          this.isConfirm = true
        }
      })
    }
  },
  methods : {
    inputName (){
      this.form.distributionDisable = false
      this.$refs.name.clearValidate()
    },
    inputDistribution (){
      this.$refs.distribution.clearValidate()
    },
    inputProduct (value, index){
      this.$refs.order.validate((valid)=>{})
      this.form.products[index].unitDisabled = false
      let temp = this.option.products.find(Q => Q.id === Number(value))
      
      this.option.unit = temp.units
      this.isCheck = !this.isCheck
    },
    inputPayment (){
      this.isCheck = !this.isCheck
      this.$refs.paymentType.clearValidate()
    },
    inputExpiredDate (){
      this.isCheck = !this.isCheck
      this.$refs.expiredDate.clearValidate()
    },
    selectUnit (idUnit, index, idProduct){
      this.$refs.order.validate((valid)=>{})
      let temp = this.option.unit.find(Q => Q.id === idUnit)
      this.form.products[index].price = temp.price
      
      this.option.products.forEach(e => {
        if (e.id === idProduct) {
          e.units = e.units.filter(Q => Q.id !== idUnit)
        }
      });
      this.isCheck = !this.isCheck
    },
    inputQty(index){
      this.$refs.order.validate((valid)=>{})
      this.form.products[index].totalPrice = this.form.products[index].qty * this.form.products[index].price
      this.isCheck = !this.isCheck
    },
    formatThousand (value) {
      const rounded = Math.round(value * 100) / 100
      return rounded
        .toString()
        .replace('.', ',')
        .replace(/\B(?=(\d{3})+(?!\d))/g, '.')
    },
    addNewProduct (){
      this.form.products.push({
        id: this.form.products.length + 1,
        product: undefined,
        unit: undefined,
        unitDisabled: true,
        qty: 0,
        price: 0,
        totalPrice: 0,
      })
      this.isConfirm = true
    },
    removeProduct (index, idProduct, idUnit) {
      if(idProduct && idUnit){
        let temp = this.option.unit.find(Q => Q.id === idUnit)
        this.option.products.forEach(e => {
          if (e.id === idProduct) {
            e.units.push(temp)
          }
        });
      }
      this.isConfirm = false
      this.form.products.splice(index, 1)
    },
    handleSubmit (){
      this.$refs.order.validate((valid) => {
        if(valid){
          console.log(this.form)
        } else {
          this.$notification.error({
            message: 'Terjadi Kesalahan',
            description: 'Silahkan periksa kembali form yang Anda isi'
          })
        }
      })
    },
    fetchData(){
      this.$axios.get('http://dummy.restapiexample.com/api/v1/employees')
      .then((response) => {
        console.log(response)
        this.option.name = response.data.data
      })
      .catch(err => {
        console.log(err)
        // set local karena sering error to many request.
        
        this.option.name = [{id:1,employee_name:"Tiger Nixon",employee_salary:320800,employee_age:61,"profile_image":""},{id:2,employee_name:"Garrett Winters",employee_salary:170750,employee_age:63,profile_image:""},{id:3,employee_name:"Ashton Cox",employee_salary:86000,employee_age:66,profile_image:""},{id:4,employee_name:"Cedric Kelly",employee_salary:433060,employee_age:22,profile_image:""},{id:5,employee_name:"Airi Satou",employee_salary:162700,employee_age:33,profile_image:""},{id:6,employee_name:"Brielle Williamson",employee_salary:372000,employee_age:61,profile_image:""},{id:7,employee_name:"Herrod Chandler",employee_salary:137500,employee_age:59,profile_image:""},{id:8,employee_name:"Rhona Davidson",employee_salary:327900,employee_age:55,profile_image:""},{id:9,employee_name:"Colleen Hurst",employee_salary:205500,employee_age:39,profile_image:""},{id:10,employee_name:"Sonya Frost",employee_salary:103600,employee_age:23,profile_image:""},{id:11,employee_name:"Jena Gaines",employee_salary:90560,employee_age:30,profile_image:""},{id:12,employee_name:"Quinn Flynn",employee_salary:342000,employee_age:22,profile_image:""},{id:13,employee_name:"Charde Marshall",employee_salary:470600,employee_age:36,profile_image:""},{id:14,employee_name:"Haley Kennedy",employee_salary:313500,employee_age:43,profile_image:""},{id:15,employee_name:"Tatyana Fitzpatrick",employee_salary:385750,employee_age:19,profile_image:""},{id:16,employee_name:"Michael Silva",employee_salary:198500,employee_age:66,profile_image:""},{id:17,employee_name:"Paul Byrd",employee_salary:725000,employee_age:64,profile_image:""},{id:18,employee_name:"Gloria Little",employee_salary:237500,employee_age:59,profile_image:""},{id:19,employee_name:"Bradley Greer",employee_salary:132000,employee_age:41,profile_image:""},{id:20,employee_name:"Dai Rios",employee_salary:217500,employee_age:35,profile_image:""},{id:21,employee_name:"Jenette Caldwell",employee_salary:345000,employee_age:30,profile_image:""},{id:22,employee_name:"Yuri Berry",employee_salary:675000,employee_age:40,profile_image:""},{id:23,employee_name:"Caesar Vance",employee_salary:106450,employee_age:21,profile_image:""},{id:24,employee_name:"Doris Wilder",employee_salary:85600,employee_age:23,profile_image:""}]

        this.$notification.error({
          message: 'Terjadi Kesalahan',
          description: 'Define name secara local'
        })
      })
    }
  }
}
</script>

<style scoped>
.wrapper {
  padding: 2em 4em;
  background-color: #e5e5e5;
}
.order-wrapper {
  padding: 2em;
  width: 100%;
  overflow: hidden;
  background-color: white;
}
.flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
