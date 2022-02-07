<template>
  <div class="may">
    <el-form ref="form" :model="personForm" label-width="150px" label-position="right">
      <el-row :gutter="10">
        <el-col :span="6">
          <el-form-item label="Person fullname">
            <el-input v-model="personForm.fullName" placeholder="Person fullname"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="4">
          <el-button @click="addPerson" type="primary">Add person</el-button>
        </el-col>
      </el-row>
    </el-form>
    <el-form ref="form" :model="personProductForm" label-width="150px" label-position="right">
      <el-row :gutter="10">
        <el-col :span="6">
          <el-form-item label="Person">
            <el-select style="width: 100%" v-model="personProductForm.personIndex" placeholder="Person">
              <el-option
                  v-for="(item, index) in persons"
                  :key="index"
                  :label="item.fullName"
                  :value="index">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="Product">
            <el-select style="width: 100%" v-model="personProductForm.productIndex" placeholder="Product">
              <el-option
                  v-for="(item, index) in products"
                  :key="index"
                  :label="item.productName"
                  :value="index">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="Product count">
            <el-input type="number" min="0" v-model="personProductForm.productCount"
                      placeholder="Product count"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="4">
          <el-button @click="addPersonProduct" type="primary">Add product to person</el-button>
        </el-col>
        <el-col :span="2">
          <el-input type="number" min="0" v-model="feePercentage" placeholder="%">
            <template slot="append"><span>%</span></template>
          </el-input>
        </el-col>
      </el-row>
    </el-form>
    <el-row :gutter="10">
      <el-col :span="6" v-for="(person, index) of personReceipt" :key="index">
        <div style="border: 1px solid dodgerblue; background-color: azure; padding: 10px; position:relative;">
          <el-button @click="personUsingProducts.splice(index, 1)" style="position:absolute; right: 10px" type="warning" icon="el-icon-remove" size="mini"></el-button>
          <div style="font-size: 16px; font-weight: bold; color: dodgerblue; margin: 5px">{{ person.fullName }}</div>
          <div style=" margin: 5px" v-for="(product, pIndex) of person.products" :key="'p-' + pIndex">
            {{ `${product.productName} (${product.productCount}): ${product.productPrice}` }}
          </div>
          <div style=" margin: 5px">Others: {{ person.othersPrice }}</div>
          <div style=" margin: 5px">Fee ({{feePercentage}}%): {{ calculateFee(person) }}</div>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: "CalculatePriceEachPersonFromProducts",
  props: {
    products: Array
  },
  data() {
    return {
      feePercentage: 10,
      persons: [],
      personForm: {
        fullName: ""
      },
      personUsingProducts: [],
      personProductForm: {
        personIndex: null,
        productIndex: null,
        productCount: 1
      }
    }
  },
  methods: {
    addPerson() {
      if (!this.personForm.fullName) {
        this.$message.warning("You need fill person fullname inputs!")
        return
      }
      this.persons.push(Object.assign({}, this.personForm));
      this.personForm.fullName = ""
    },
    addPersonProduct() {
      if (!this.personProductForm.productCount || this.personProductForm.productIndex == null || this.personProductForm.personIndex == null){
        this.$message.warning("You need fill all person and product inputs!")
        return
      }
      let perProductIndex = this.personUsingProducts.findIndex(item => item.personIndex === this.personProductForm.personIndex)
      if (perProductIndex > -1) {
        let productIndex = this.personUsingProducts[perProductIndex].products.findIndex(item => item.productIndex === this.personProductForm.productIndex)
        if (productIndex > -1) {
          this.personUsingProducts[perProductIndex].products[productIndex].productCount += this.personProductForm.productCount
        } else {
          this.personUsingProducts[perProductIndex].products.push(
              {
                productIndex: this.personProductForm.productIndex,
                productCount: this.personProductForm.productCount
              }
          )
        }
      } else {
        this.personUsingProducts.push(
            {
              personIndex: this.personProductForm.personIndex,
              products: [
                {
                  productIndex: this.personProductForm.productIndex,
                  productCount: this.personProductForm.productCount
                }
              ]
            }
        )
      }
    },
    calculateFee(person) {
      let fee = 0
      fee += person.othersPrice
      fee += person.products.reduce((next, current) => {
        next += current.productPrice
        return next
      }, 0)
      return fee * this.feePercentage / 100
    }
  },
  computed: {
    personReceipt() {
      let personsReceipt = []
      let othersSumm = 0
      this.products.filter((product, pIndex) => {
        return this.personUsingProducts.findIndex(person => {
          return person.products.find(x => x.productIndex === pIndex)
        }) === -1
      }).forEach(item => {
        othersSumm += item.productPrice
      })
      personsReceipt = this.personUsingProducts.map((item) => {
        return {
          fullName: this.persons[item.personIndex].fullName,
          products: item.products.map((product) => {
            return {
              productName: this.products[product.productIndex].productName,
              productCount: product.productCount,
              productPrice: this.products[product.productIndex].productPrice
                  / this.products[product.productIndex].productCount
                  * product.productCount
            }
          }),
          othersPrice: othersSumm / this.persons.length
        }
      })
      return personsReceipt;
    }
  }
}
</script>

<style scoped>
.may{
  margin-top: 200px;
}
</style>