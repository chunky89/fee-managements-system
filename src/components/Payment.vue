<template>
  <div class="payment">
    <h2>Pay Your Fee</h2>
    <form @submit.prevent="processPayment">
      <div>
        <label for="amount">Amount:</label>
        <input id="amount" v-model.number="amount" type="number" min="1" required />
      </div>
      <div>
        <label>Payment Method:</label>
        <select v-model="method" required>
          <option disabled value="">Select a method</option>
          <option value="bank">Bank Transfer</option>
          <option value="credit">Credit Card (Visa/MasterCard)</option>
          <option value="paypal">PayPal</option>
          <option value="easypaisa">Easypaisa</option>
          <option value="jazzcash">JazzCash</option>
        </select>
      </div>
      <div v-if="method === 'credit'">
        <input v-model="cardNumber" placeholder="Card Number" maxlength="16" required />
        <input v-model="expiry" placeholder="MM/YY" maxlength="5" required />
        <input v-model="cvv" placeholder="CVV" maxlength="3" required />
      </div>
      <button type="submit">Pay Now</button>
    </form>
    <div v-if="message" class="message">{{ message }}</div>
  </div>
</template>

<script>
export default {
  name: "Payment",
  data() {
    return {
      amount: null,
      method: "",
      cardNumber: "",
      expiry: "",
      cvv: "",
      message: ""
    };
  },
  computed: {
    methodDisplay() {
      switch (this.method) {
        case "bank": return "Bank Transfer";
        case "credit": return "Credit Card";
        case "easypaisa": return "Easypaisa";
        case "jazzcash": return "JazzCash";
        case "paypal": return "PayPal";
        default: return "";
      }
    }
  },
  methods: {
    processPayment() {
      // Basic validation for credit card fields
   if (this.method === "credit") {
    if (!this.cardNumber || !this.expiry || !this.cvv) {
      alert('Please fill in all credit card fields.');
      return;
    }
    if (this.cardNumber.length !== 16 || !/^\d{16}$/.test(this.cardNumber)) {
      alert('Card number must be exactly 16 digits.');
      return;
    }
  }
    if (this.expiry && !/^(0[1-9]|1[0-2])\/\d{2}$/.test(this.expiry)) {
        alert('Expiry date must be in MM/YY format.');
        return;
        }
    if (this.cvv && !/^\d{3}$/.test(this.cvv)) {
        alert('CVV must be exactly 3 digits.');
        return;
      }
      // Check if amount and method are filled
  if (!this.amount || !this.method) {
    alert('Please fill in all fields.');
    return;
  }
      // Mock payment success
      this.message = `Payment of $${this.amount} via ${this.methodDisplay} successfully!`;
      // Reset form
      this.amount = null;
      this.method = "";
      this.cardNumber = "";
      this.expiry = "";
      this.cvv = "";
    }
  }
};
</script>

<style scoped>
.payment {
  max-width: 400px;
  margin: 0 auto;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  background: #fafafa;
}
.payment form > div {
  margin-bottom: 1rem;
}
.payment input, .payment select {
  width: 100%;
  padding: 0.5rem;
  box-sizing: border-box;
}
.message {
  margin-top: 1rem;
  color: green;
  font-weight: bold;
}
</style>