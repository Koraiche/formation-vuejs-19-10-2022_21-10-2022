<template>
    <div class="row">
        <div class="col-md-8">
            <table class="table table-hover">
                <thead>
                    <tr>
                        <th scope="col">ID</th>
                        <th scope="col">Name</th>
                        <th scope="col">Price</th>
                        <th scope="col">Supplier</th>
                        <th scope="col">Image</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="product in dataList" :key="product.id" @click="detailedProduct(product)"
                        :class="product.id%2==0?'table-light':'table-active'">
                        <th scope="row">{{product.id}}</th>
                        <td>{{product.name}}</td>
                        <td>{{product.price}}</td>
                        <td>{{product.supplier}}</td>
                        <td><img height="50px" width="50px" :src="product.imageUrl" /></td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div v-if="productDetails!=null" class="col-md-4 mt-4">  
            <h2>Details</h2>        
            <div class="card border-secondary mb-3 col-md-12" style="max-width: 20rem;">
                <div class="card-header">Product Details</div>
                <div class="card-body">
                    <h4 class="card-title">{{productDetails.name}}</h4>
                    <p class="card-text">Price : {{productDetails.price}} - {{productDetails.supplier}}</p>
                    <button class="ml-4 btn btn-primary" @click="addToChart(productDetails)">Add to chart</button>
                </div>
            </div>
            <div class="col-md-12" v-if="chart.length>0">
                <table class="table table-hover">
                <thead>
                    <tr>
                        <th scope="col">Product</th>
                        <th scope="col">Quantity</th>
                        <th scope="col">Action</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="product in chart" :key="product.id"
                        :class="product.id%2==0?'table-light':'table-active'">
                        <th scope="row">{{product.name}}</th>
                        <td>{{product.quantity}}</td>
                        <td><button class="btn btn-danger" @click="removeFromChart(product)">Remove</button></td>
                    </tr>
                </tbody>
            </table>
            </div>
        </div>

    </div>
</template>

<script>

import axios from "axios";

export default {
    data() {
        return {
            dataList: [],
            productDetails: null,
            chart: [],
            exists: false
        }
    },
    methods: {
        fetch() {
            axios.get("http://localhost:9090/products").then((res) => {
                this.dataList = res.data;

            }).catch((error) => {
                console.log("error" + error);
            });
        },
        detailedProduct(product){
            this.productDetails = product;
        },
        addToChart(product){
            this.exists = false;
            this.chart.map(s=>{
                if(s.id ==product.id){
                    s.quantity++;
                    this.exists =true;
                }
                return s;
            })
            if(!this.exists){
                product.quantity = 1;
                this.chart.push(product);
            }
            this.$forceUpdate();  
        },
        removeFromChart(product){
            this.chart.forEach(s=>{
                if(s.id ==product.id){
                    if(s.quantity>1){
                        this.chart[this.chart.indexOf(s)].quantity--;
                    }
                    else if(s.quantity == 1){
                        this.chart.splice(this.chart.indexOf(s),1);
                        this.$forceUpdate();
                    }  
                    this.$forceUpdate();      
                }
            })
        }
    },
    beforeMount() {
        this.fetch();
    }
}
</script>
<style scoped>
* {
    font-family: Arial, Helvetica, sans-serif;
    font-size: 1.06em;
}
</style>
