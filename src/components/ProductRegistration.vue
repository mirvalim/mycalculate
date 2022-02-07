<template>

  <div class="registration">
    <el-form ref="form" :model="form" label-width="150px" label-position="right">
      <el-row :gutter="10">
        <el-col :span="6">
          <el-form-item label="Product name">
            <el-input v-model="form.productName" placeholder="Product name"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="Product price">
            <el-input type="number" min="0" v-model="form.productPrice" placeholder="Product price"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="Product count">
            <el-input type="number" min="0" v-model="form.productCount" placeholder="Product count"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="4">
          <el-button @click="addNewProduct" type="primary">Add product</el-button>
        </el-col>
      </el-row>
    </el-form>
    <el-table
        :data="products"
        style="width: 100%">
      <el-table-column
          type="index"
          label="Tr"
          width="50">
      </el-table-column>
      <el-table-column
          prop="productName"
          label="Product name"
          width="250">
      </el-table-column>
      <el-table-column
          prop="productPrice"
          label="Product price"
          width="250">
      </el-table-column>
      <el-table-column
          prop="productCount"
          label="Product count"
          width="250">
      </el-table-column>
      <el-table-column
          label="Actions"
          width="250">
        <template slot-scope="{row}">
          <el-button size="small" type="warning" icon="el-icon-remove" @click="removeProduct(row)"></el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: "ProductRegistration",
  props: {
    products: Array
  },
  data() {
    return {
      form: {
        productName: "",
        productPrice: null,
        productCount: 1
      }
    }
  },
  methods: {
    addNewProduct() {
      if (!this.form.productName || !this.form.productPrice || !this.form.productCount) {
        this.$message.warning("You need fill all inputs!")
        return
      }
      let products_ = this.products;
      products_.push(Object.assign({}, this.form))
      this.$emit('update:products', products_)
      this.form = {
        productName: "",
        productPrice: null,
        productCount: 1
      }
    },
    removeProduct(row) {
      let products_ = this.products;
      let index = products_.findIndex(item => item === row)
      products_.splice(index, 1);
      this.$emit('update:products', products_)
    }
  }
}
</script>

<style scoped>
.registration {
  margin-top: 60px;
  font-size: 20px;
}


.registration ::v-deep .el-form-item__label {
  font-size: 20px;
}
</style>


