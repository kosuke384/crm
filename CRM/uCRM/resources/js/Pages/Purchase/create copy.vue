<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';

import{reactive} from 'vue'
import{Inertia} from '@inertiajs/inertia'
import{getToday} from '@/commmon'
import { onMounted } from 'vue';
import { ref,computed } from 'vue';

onMounted(()=>{
    form.date=getToday()
    props.items.forEach(item=>{
        itemList.value.push({
            id:item.id,
            name:item.name,
            price:item.price,
            quantity:0
        })
    })
})

const totalPrice=computed(()=>{
    let total=0

    itemList.value.forEach(item=>{
        total+=item.price*item.quantity
    })

    return total
})

const quantity=["0","1","2","3","4","5","6","7","8","9"]
const props= defineProps({
    customers:Array,
    items:Array
})

const itemList=ref([])


const form=reactive({
    date:null,
    customer_id:null,
    status:true,
    items:[]
})

const storePurchase=()=>{
    itemList.value.forEach(item=>{
        if(item.quantity>0){
            form.items.push({
                id:item.id,
                quantity:item.quantity
            })
        }
    })
    Inertia.post(route('purchases.store'),form)
}
</script>

<template>
    <form @submit.prevent="storePurchase">
        日付<br>
        <input type="date" name="date" v-model="form.date"><br>
        会員名<br>
        <select name="customer" v-model="form.customer_id">
            <option v-for="customer in customers" :value="customer.id" :key="customer.id">
            {{ customer.id }}
            {{ customer.name }}
            </option>
        </select>
        <br>
        商品サービス<br>
        <table>
            <thead>
                <tr>
                <th>id</th>
                <th>商品名</th>
                <th>金額</th>
                <th>数量</th>
                <th>小計</th>
            </tr>
            </thead>
            <tbody>
                <tr v-for="item in itemList">
                    <td>{{ item.id }}</td>
                    <td>{{ item.name }}</td>
                    <td>{{ item.price }}</td>
                    <td>
                        <select name="quantity" v-model="item.quantity">
                        <option v-for="q in quantity" :value="q">{{ q }}</option>
                        </select>
                    </td>
                    <td>{{ item.price*item.quantity }}</td>
                </tr>
            </tbody>
        </table>
        <br>
        合計:{{ totalPrice }}円<br>
        <button>登録</button>
</form>
</template>