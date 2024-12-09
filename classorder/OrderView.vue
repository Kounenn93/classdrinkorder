<template>
    <div>
        <!-- 頭部區域 -->
        <div class="header">
            <h2></h2>
        </div>

        <!-- 主要區域 -->
        <div class="main">
            <!-- banner區塊 -->
            <div class="banner wrapper">
                <img src="./images/banner.jpg" alt="">
            </div>
            <!-- 點餐區 -->
            <div class="order wrapper">
                <h4>ORDER HERE</h4>
                <h5>店家：<a
                        href="https://hechaloutea.com.tw/wp-content/uploads/2024/04/2024-HCL-A3-%E4%B8%ADMENU_%E6%AD%A3%E9%9D%A2-4.2-scaled.jpg"
                        title="menu" target="blank">鶴茶樓</a></h5>
                <form @submit.prevent="submitOrder">
                    <label><b>姓名：</b><input type="text" v-model="newOrder.name" placeholder="你是誰？"
                            required="required"></label>
                    <label><b>品項：</b><input type="text" v-model="newOrder.item" placeholder="點什麼？"
                            required="required"></label>
                    <label><b>加料區：</b><input type="text" v-model="newOrder.addition" placeholder="加料哦"></label>                    
                    <label>
                        <b>尺寸：</b>
                        <select v-model="newOrder.size">
                            <option selected>L</option>
                            <option>M</option>
                        </select>
                    </label>                    
                    <label>
                        <b>甜度：</b>
                        <select v-model="newOrder.sugar">
                            <option>無糖</option>
                            <option selected>微糖</option>
                            <option>半糖</option>
                            <option>少糖</option>
                            <option>全糖</option>
                        </select>
                    </label>
                    <label>
                        <b>冰量：</b>
                        <select v-model="newOrder.ice">
                            <option>去冰</option>
                            <option selected>微冰</option>
                            <option>少冰</option>
                            <option>正常冰</option>
                        </select>
                    </label>
                    <label>
                        <b>金額：</b>
                        <input type="number" v-model="newOrder.price" placeholder="$$$" required="required">
                    </label>
                    <br><br><br>
                    <button type="submit">送出</button>
                </form>

                <!-- 訂餐結果顯示區 -->
                <div class="view">
                    <table class="wrapper">
                        <thead>
                            <tr>
                                <th>序</th>
                                <th>姓名</th>
                                <th>品項名</th>                                
                                <th>加料</th>
                                <th>尺寸</th>
                                <th>甜度</th>
                                <th>冰量</th>
                                <th>金額</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(order, index) in ordersArray" :key="index">
                                <td>{{ index + 1 }}</td>
                                <td>{{ order.name }}</td>
                                <td>{{ order.item }}</td>
                                <td>{{ order.addition }}</td>
                                <td>{{ order.size }}</td>
                                <td>{{ order.sugar }}</td>
                                <td>{{ order.ice }}</td>
                                <td>{{ order.price }}</td>
                            </tr>
                        </tbody>
                        <tfoot>
                            <tr>
                                <td></td>
                                <td></td>
                                <td></td>
                                <td></td>
                                <td></td>
                                <td></td>
                                <td>加總：</td>
                                <td>{{ totalPrice }}</td>
                            </tr>
                        </tfoot>
                    </table>
                </div>
            </div>
        </div>
        <div class="foot">
            <h6>Copyright | Kounenn </h6>
        </div>
    </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getDatabase, ref, push, onValue } from "firebase/database";

const firebaseConfig = {
  apiKey: "AIzaSyAUUlBk_1Qynuf57TjpCa1iVwF551VCHTc",
  authDomain: "orderdemo-948c3.firebaseapp.com",
  projectId: "orderdemo-948c3",
  storageBucket: "orderdemo-948c3.appspot.com",
  messagingSenderId: "799614623104",
  appId: "1:799614623104:web:92afecd0123b25b5c33565",
  measurementId: "G-ZFVTVVKK3S"
};

const app = initializeApp(firebaseConfig);
const database = getDatabase(app);
const ordersRef = ref(database, 'orders');

class Order {
    constructor(name, item, addition, size, sugar, ice, price) {
        this.name = name;
        this.item = item;
        this.addition = addition;
        this.size = size;
        this.sugar = sugar;
        this.ice = ice;
        this.price = price;
    }
}

export default {
    data() {
        return {
            newOrder: {
                name: '',
                item: '',
                addition: '',
                size: 'L',
                sugar: '微糖',
                ice: '微冰',
                price: ''
            },
            ordersArray: [],
            totalPrice: 0
        };
    },
    methods: {
        submitOrder() {
            const order = new Order(
                this.newOrder.name,
                this.newOrder.item,
                this.newOrder.addition,
                this.newOrder.size,
                this.newOrder.sugar,
                this.newOrder.ice,
                this.newOrder.price
            );
            
            // 將訂單推送到 Firebase
            push(ordersRef, order);

            // 重置表單
            this.newOrder = {
                name: '',
                item: '',
                addition: '',
                size: 'L',
                sugar: '微糖',
                ice: '微冰',
                price: ''
            };
        },
        calTotalPrice() {
            let sum = 0;
            for (let i = 0; i < this.ordersArray.length; i++) {
                sum += parseFloat(this.ordersArray[i].price || 0);
            }
            this.totalPrice = sum;
        }
    },
    mounted() {
        // 監聽 Firebase 中的訂單變化
        onValue(ordersRef, (snapshot) => {
            const data = snapshot.val();
            if (data) {
                this.ordersArray = Object.values(data);
                this.calTotalPrice();
            } else {
                this.ordersArray = [];
                this.totalPrice = 0;
            }
        });
    }
};
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: "Ubuntu", sans-serif;
}

li {
    list-style: n;
}

.wrapper {
    margin: 0 auto;
    width: 1200px;
}

/* 頭部區域 */
.header {
    height: 50px;
    background-color: rgb(250, 177, 22);
}

.header h2 {
    text-align: center;
    line-height: 50px;
    color: #fff;
    font-size: 30px;
    font-weight: 600;
}

/* banner區塊 */
.banner {
    background-color: rgb(33, 33, 33);
    text-align: center;
    margin-bottom: 80px;
}

.banner img {
    vertical-align: middle;
    height: 496px;
}

/* 點餐區 */
.order h4 {
    text-align: center;
    margin: 15px;
    font-size: 36px;
}

.order h5 {
    text-align: center;
    margin-bottom: 30px;
    font-size: 20px;
}

.order h5 a {
    text-decoration: none;
    color: rgb(33, 33, 33);
    background-color: rgba(250, 177, 22, 0.2);
}

.order h5 a:hover {
    color: rgb(250, 177, 22);
}

.order form {

    padding-bottom: 30px;
    text-align: center;
    border-bottom: rgba(33, 33, 33, 0.5) solid 1px;

}

.order form label {
    margin: 9px;
}

.order form b {
    font-weight: 400;
}

.order form input,
.order form select {
    padding: 5px;
    width: 128px;
    height: 40px;
    border: rgba(33, 33, 33, 0.5) solid 1px;
    border-radius: 10px;
}

.order form select {
    padding: 0;
    width: 70px;
    cursor: pointer;
}

.order form button {
    width: 80px;
    height: 40px;
    background-color: rgb(250, 177, 22);
    border: 0;
    border-radius: 10px;
    color: #fff;
    cursor: pointer;
}

.view {
    margin-top: 30px;
    padding: 15px;
    height: 1000px;
    /* background-color: pink; */
}

.view table {
    border-collapse: collapse;
    width: 800px;
}



.view table tr th {
    height: 50px;
    border-bottom: rgba(33, 33, 33, 0.5) solid 1px;
}

.view table tr td {
    height: 50px;
    text-align: center;
}

.view table tbody td:last-child {
    text-align: right;
}

.view table tfoot td {
    height: 100px;
    text-align: right;
    font-size: 18px;
    font-weight: 600;
    color: rgb(250, 177, 22);
}

/* 版權區 */
.foot {
    padding-top: 42px;
    height: 80px;
    background-color: rgb(33, 33, 33);
    text-align: center;
}

.foot h6 {
    color: #fff;
    font-size: 14px;
}
</style>