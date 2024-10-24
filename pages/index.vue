<template>
  <v-app>
    <v-app-bar app>
      <div>
        <v-img src="logo.png" max-width="120" max-height="80"></v-img>

      </div>

      <v-spacer></v-spacer>
      <v-btn icon @click="cartDialog = true">
        <v-icon>mdi-cart</v-icon>
        <v-badge v-if="cart.length" :content="cartTotalQuantity" color="red" overlap>
        </v-badge>
      </v-btn>

      <v-btn icon v-if="!isLoggedIn" to="/auth/login">
        <v-icon>mdi-account-plus</v-icon>
      </v-btn>
      <!-- <v-btn icon v-else to="/auth/profile">
        <v-icon>mdi-account</v-icon>
      </v-btn> -->

      <v-menu v-else v-model="menu" :close-on-content-click="false" :nudge-width="500" offset-x>
        <template v-slot:activator="{ on }">
          <v-btn icon v-on="on">
            <v-icon>mdi-account-circle</v-icon>
          </v-btn>
        </template>
        <v-card>
          <v-list-item>
            <v-list-item-avatar>
              <img v-if="user" :src="user.photoURL" alt="Profile Picture" class="drawer-profile-pic" />
            </v-list-item-avatar>
            <v-list-item-content>
              <h1 v-if="user">{{ user.displayName || 'User' }}</h1>
              <v-list-item-subtitle v-if="user">{{ user.email || 'No email available' }}</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-divider></v-divider>
          <v-card-actions>
            <v-btn @click="logout">Logout</v-btn>
          </v-card-actions>
        </v-card>
      </v-menu>
    </v-app-bar>


    <v-container>
      <v-row>
        <v-col v-for="product in products" :key="product.id" cols="12" md="4">
          <v-card>
            <v-card-title>{{ product.attributes.name }}</v-card-title>
            <v-card-subtitle>₱{{ product.attributes.price }}</v-card-subtitle>
            <v-card-text>{{ product.attributes.description }}</v-card-text>
            <v-img :src="product.attributes.image" height="150px"></v-img>
            <v-card-actions>
              <v-btn @click="addToCart(product)"><v-icon>mdi-basket-check</v-icon></v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-container>


    <v-dialog v-model="cartDialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Cart</span>
        </v-card-title>
        <v-card-subtitle>Total: ₱{{ cartTotal }}</v-card-subtitle>
        <v-list>
          <v-list-item-group v-if="cart.length" v-model="cartModel">
            <v-list-item v-for="(item, index) in cart" :key="item.id" :value="item">
              <v-list-item-content>
                <v-list-item-title>{{ item.attributes.name }}</v-list-item-title>
                <v-list-item-subtitle>{{ item.attributes.subTotal }}</v-list-item-subtitle>

              </v-list-item-content>
              <v-list-item-action>
                <div class="quantity-toggle">
                  <button @click="decrement(index)">&mdash;</button>
                  <input type="text" :value="item.attributes.quantity" readonly>
                  <button @click="increment(index)">&#xff0b;</button>
                </div>
              </v-list-item-action>
              <v-icon @click="removeFromCart(item)">mdi-delete</v-icon>
            </v-list-item>
          </v-list-item-group>
          <v-list-item-group v-else>
            <v-list-item>No items in cart</v-list-item>
          </v-list-item-group>
        </v-list>
        <v-card-actions>
          <v-btn @click="checkout">Checkout</v-btn>
          <v-btn @click="cartDialog = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>


    <v-dialog v-model="checkoutDialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Checkout</span>
        </v-card-title>

        <v-simple-table>
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-left">Product</th>
                <th class="text-left">Price</th>
                <th class="text-left">Quantity</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in checkoutDetails.products" :key="item.id">
                <td>{{ item.attributes.name }}</td>
                <td>₱{{ item.attributes.subTotal }}</td>
                <td>{{ item.attributes.quantity }}</td>
              </tr>
              <tr>
                <td colspan="2" class="text-right"><strong>Total:</strong></td>
                <td>₱{{ checkoutDetails.total }}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>

        <!-- Order Type Selection -->
        <v-card-subtitle>
          <span>Select Order Type:</span>
        </v-card-subtitle>
        <v-radio-group v-model="orderType">
          <v-radio label="Pick Up Order" value="pickup"></v-radio>
          <v-radio label="Order and Repair on Mechanic" value="mechanic"></v-radio>
        </v-radio-group>

        <!-- Payment Method Selection -->
        <v-card-subtitle>
          <span>Select Payment Method:</span>
        </v-card-subtitle>
        <v-radio-group v-model="selectedPaymentMethod">
          <v-radio label="GCash" value="gcash"></v-radio>
          <v-radio label="Pay Through Counter" value="counter"></v-radio>
        </v-radio-group>

        <v-card-actions>
          <v-btn @click="processCheckout">Checkout</v-btn>
          <v-btn @click="checkoutDialog = false">Close</v-btn>
        </v-card-actions>


        <v-dialog v-model="receiptDialog" max-width="600px">
          <v-card>
            <v-card-title>
              <span class="headline">Receipt</span>
            </v-card-title>

            <v-simple-table>
              <thead>
                <tr>
                  <th class="text-left">Product</th>
                  <th class="text-left">Price</th>
                  <th class="text-left">Quantity</th>
                  <th class="text-left">Status</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in checkoutDetails.products" :key="item.id">
                  <td>{{ item.attributes.name }}</td>
                  <td>₱{{ item.attributes.subTotal }}</td>
                  <td>{{ item.attributes.quantity }}</td>
                  <td>{{ paymentStatus }}</td> <!-- Paid or Not Paid -->
                </tr>
                <!-- Add this row to display the total and the payment status -->
                <tr>
                  <td colspan="2" class="text-right"><strong>Total:</strong></td>
                  <td>₱{{ checkoutDetails.total }}</td>
                  <td>{{ paymentStatus }}</td> <!-- Paid or Not Paid -->
                </tr>
              </tbody>
            </v-simple-table>

            <div>
              <img :src="qrCodeUrl" alt="QR Code" v-if="qrCodeUrl" />
            </div>

            <v-card-actions>
              <v-btn @click="receiptDialog = false">Close</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>





        <!-- Mechanic Order Confirmation (Visible for Mechanic Orders) -->
        <div v-if="orderType === 'mechanic' && mechanicOrderSent">
          <v-card-title>
            <span class="headline">Order sent to Mechanic</span>
          </v-card-title>
          <v-card-text>
            Your order has been successfully sent to the mechanic for repair.
          </v-card-text>
        </div>
      </v-card>
    </v-dialog>



  </v-app>
</template>

<script>

import { auth } from '/plugins/firebase.js';
import { getAuth, onAuthStateChanged } from "firebase/auth";


export default {
  name: "/",
  layout: "DefaultLayout",
  data() {
    return {
      receiptDialog: false,
      menu: false,
      user: null,
      isLoggedIn: false,


      cartModel: [],
      products: [],
      cart: [],
      cartDialog: false,
      checkoutDialog: false,
      checkoutDetails: {
        products: [],
        total: null,
        userId: null
      },
      tempCurrentIndex: null,
      cartTotal: null,
      selectedPaymentMethod: null,
      showReceipt: false, // new add
      paymentStatus: "Not Paid", // Default status
      qrCodeUrl: "", // URL for the QR code // until here
    };

  },


  created() {
    onAuthStateChanged(auth, async (currentUser) => {
      if (currentUser) {
        console.log(currentUser);
        this.user = currentUser;
        this.isLoggedIn = true;
      } else {
        this.$router.push('/auth/login');
      }
    });

  },

  props: {
    value: Object
  },
  computed: {
    cartTotalQuantity() {
    return this.cart.reduce((total, item) => total + item.attributes.quantity, 0);
  }

  },
  async created() {
    try {
      const response = await this.$axios.$get('http://localhost:1337/api/products')
        .then(response => (this.products = response.data))
      console.log(response)
    } catch (error) {
      console.error('Error fetching products:', error);
      console.log(response);

    }
  },

  mounted() {
    this.isLoggedIn = this.checkUserIfExist();
    console.log(this.user)
  },


  methods: {
    processCheckout() {
      if (this.orderType === 'pickup') {
        this.showReceipt = true;
        this.receiptDialog = true;
        // Generate QR code logic here
        this.qrCodeUrl = 'https://example.com/qr-code'; // Replace with actual QR code URL
      } else if (this.orderType === 'mechanic') {
        this.mechanicOrderSent = true;
        // Logic to send order to the mechanic
        this.sendOrderToMechanic();
      }
    },
    sendOrderToMechanic() {
      // API call or logic to send order details to the mechanic
      console.log('Order sent to mechanic:', this.checkoutDetails.products);
    },
    updatePaymentStatus(isPaid) {
      this.paymentStatus = isPaid ? 'Paid' : 'Not Paid';
    },

    checkUserIfExist() {
      const user = auth.currentUser;
      let retVal = true
      if (!user) {
        retVal = false
      }
      return retVal
    },

    addToCart(product) {
      if (this.checkUserIfExist()) {
        if (this.isProductExist(product)) {

          this.cart[this.tempCurrentIndex].attributes.quantity++;
          this.calculateSubtotal(this.tempCurrentIndex)
          console.log("Product is Existing");
        } else {
          product.attributes.quantity = 1;
          product.attributes.subTotal = product.attributes.price
          console.log("Product is not Existing");
          this.cart.push(product);
        }
        console.log(this.cart);
        this.tempCurrentIndex = null
        this.calculateCartTotal()

      } else {
        this.$router.push('/auth/login')
      }
    },

    removeFromCart(product) {
      this.cart = this.cart.filter(item => item.id !== product.id);
      this.calculateCartTotal()
    },
    checkout() {
      // this.cart = [];
      this.cartDialog = false;
      this.checkoutDialog = true;
      this.checkoutDetails.products = this.cart
      console.log(this.checkoutDetails.products)
      this.checkoutDetails.total = this.checkoutDetails.products.reduce((total, item) => total + item.attributes.subTotal, 0);
    },

    isProductExist(product) {
      let retVal = false
      let index = 0;
      this.tempCurrentIndex = 0
      this.cart.map(item => {

        if (item.id == product.id) {
          retVal = true
          this.tempCurrentIndex = index;
        }
        index++;
      })
      return retVal
    },
    calculateCartTotal() {
      console.log(this.cart)
      this.cartTotal = this.cart.reduce((total, item) => total + item.attributes.subTotal, 0);
    },
    calculateSubtotal(index) {
      this.cart[index].attributes.subTotal =
        this.cart[index].attributes.price *
        this.cart[index].attributes.quantity;

    },

    increment(index) {
      console.log(index)
      console.log('increment')
      this.cart[index].attributes.quantity++
      this.calculateSubtotal(index)
      this.calculateCartTotal()
      console.log(this.cart[index].attributes)
    },
    decrement(index) {
      console.log('decrement')
      console.log(index)
      if (this.cart[index].attributes.quantity === 1) {
        alert('Negative quantity not allowed')
      } else {
        this.cart[index].attributes.quantity--
        this.calculateSubtotal(index)
        this.calculateCartTotal()
      }

      console.log(this.cart[index].attributes)
    },






    logout() {
      auth.signOut().then(() => {
        this.isLoggedIn = false;
        this.user = null;
        this.$router.push('/');
      });
    },




  },
};
</script>


<style scoped>
.centered-img {
  display: flex;
  justify-content: center;
  align-items: center;
}


#app {
  display: flex;
  width: 100%;
  height: 100vh;
  justify-content: center;
  align-items: center;
}

pre {
  background: #eee;
  padding: 1rem;
  border-radius: 5px;
}



.quantity-toggle {
  display: flex;

  input {
    border: 0;
    border-top: 2px solid #ddd;
    border-bottom: 2px solid #ddd;
    width: 2.5rem;
    text-align: center;
    padding: 0 .5rem;
  }

  button {
    border: 2px solid #ddd;
    padding: .5rem;
    background: #f5f5f5;
    color: #888;
    font-size: 1rem;
    cursor: pointer;
  }
}
</style>
