<template>
  <div style="margin: 56px;">

      <button class="btn btn-primary pull-right" style="margin-bottom: 10px;" @click="redirect('/add_product')">Add Product</button>

      <filter-product v-bind:category="category" 
      :activeCategoryIndex="0"
      :activeSortingIndex="0"
      @changeSortEvent="retrieve($event.sort, $event.filter)"
      :grid="['list']">
    </filter-product>

    <table v-if="data" class="table table-bordered table-responsive">
      <thead>
        <tr>
          <td>Title
            <i class="fas fa-chevron-up pull-right action-link" @click="sortArrayTitle('desc')" v-if="activeSortTitle === 'asc'"></i>
            <i class="fas fa-chevron-down  pull-right action-link" @click="sortArrayTitle('asc')" v-if="activeSortTitle === 'desc'"></i>
          </td>
          <td>Type</td>
          <td>Status</td>
          <td>Action</td>
        </tr>
      </thead>
      <tbody v-if="data">
        <tr v-for="(item, index) in data" :key="index">
          <td>
            {{item.name}}
          </td>
          <td>{{item.type}}</td>
          <td>{{item.status}}</td>
          <td>
            <button class="btn btn-primary" @click="redirect('/edit_product')">EDIT</button>
            <button class="btn btn-danger" @click="removeItem()">DELETE</button>
          </td>
        </tr>
      </tbody>
    </table>
    <confirmation
    :title="'Confirmation Modal'"
    :message="'Are you sure you want to delete ?'"
    ref="confirms"
    @onConfirm="remove(id)"
    >
    </confirmation>
    <empty v-if="data === null" :title="title" :action="guide"></empty>
  </div>
</template>
<style>
.underline-on-hover:hover{
  cursor: pointer;
  text-decoration: underline;
}
.btn-warning:hover{
  color: #fff !important;
}
</style>
<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import CONFIG from 'src/config.js'
import axios from 'axios'
import COMMON from 'src/common.js'
export default {
  mounted(){
    this.filterBy('all')
  },
  data(){
    return {
      data: [{
        name: 'Product 1',
        type: 'Regular',
        status: 'PENDING'
      }, {
        name: 'Product 2',
        type: 'Regular',
        status: 'PENDING'
      }, {
        name: 'Product 3',
        type: 'Regular',
        status: 'PUBLISHED'
      }],
      user: AUTH.user,
      config: CONFIG,
      productId: null,
      sorted: [],
      activePage: 'all',
      title: 'Empty Products!',
      guide: 'No activity at the moment.',
      activeSortTitle: 'asc',
      activeSortInventory: 'asc',
      category: [{
        title: 'Products',
        sorting: [{
          title: 'Name ascending',
          payload: 'name',
          payload_value: 'asc',
          type: 'text'
        }, {
          title: 'Name descending',
          payload: 'name',
          payload_value: 'desc',
          type: 'text'
        }, {
          title: 'Type ascending',
          payload: 'type',
          payload_value: 'asc',
          type: 'text'
        }, {
          title: 'Type descending',
          payload: 'type',
          payload_value: 'desc',
          type: 'text'
        }, {
          title: 'Status ascending',
          payload: 'status',
          payload_value: 'asc',
          type: 'text'
        }, {
          title: 'Status descending',
          payload: 'status',
          payload_value: 'desc',
          type: 'text'
        }]
      }]
    }
  },
  components: {
    'filter-product': require('components/increment/ecommerce/filter/Product.vue'),
    'empty': require('components/increment/generic/empty/Empty.vue'),
    'confirmation': require('components/increment/generic/modal/Confirmation.vue')
  },
  props: ['type'],
  watch: {
    data (to, from){
      this.filterBy('all')
    }
  },
  methods: {
    sortArrayTitle(sort){
      this.activeSortTitle = sort
      if(sort === 'desc'){
        this.data = this.data.sort((a, b) => {
          return a.title < b.title ? -1 : 1
        })
      }else{
        this.data = this.data.sort((a, b) => {
          return a.title > b.title ? -1 : 1
        })
      }
    },
    sortArrayInventory(sort){
      this.activeSortInventory = sort
      if(sort === 'desc'){
        this.data = this.data.sort((a, b) => {
          return a.qty < b.qty ? -1 : 1
        })
      }else{
        this.data = this.data.sort((a, b) => {
          return a.qty > b.qty ? -1 : 1
        })
      }
    },
    redirect(parameter){
      ROUTER.push(parameter)
    },
    filterBy(type){
      this.activePage = type
      if(type === 'all'){
        this.sorted = this.data
      }else{
        this.sorted = this.data.filter((item) => {
          if(item.type === type){
            return item
          }
        })
      }
    },
    retrieve(sort){
      console.log('retrieve')
    },
    removeItem() {
      $('#connectionError').modal('show')
    }
  }
}
</script>
