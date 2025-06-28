<template>
    <div class="voucher-generator">
        <h2>Voucher Generator</h2>
        <form @submit.prevent="generateVoucher">
            <div>
                <label for="recipient">Recipient Name:</label>
                <input id="recipient" v-model="recipient" placeholder="Enter name" required />
            </div>
            <div>
                <label for="amount">Amount:</label>
                <input id="amount" type="number" placeholder="Enter amount" v-model.number="amount" min="1" max="100000" required/>
            </div>
            <div>
                <label for="description">Description:</label>
                <input id="description" placeholder="Enter a description" v-model="description" />
            </div>
            <button type="submit">Generate Voucher</button>
        </form>

        <div v-if="voucher" class="voucher-preview">
            <h3>Voucher Preview</h3>
            <p><strong>Voucher Code:</strong> {{ voucher.code }}</p>
            <p><strong>Recipient:</strong> {{ voucher.recipient }}</p>
            <p><strong>Amount:</strong> ${{ voucher.amount }}</p>
            <p><strong>Description:</strong> {{ voucher.description }}</p>
            <p><strong>Date:</strong> {{ voucher.date }}</p>
            <button @click="downloadPDF">Download PDF</button>
        </div>
    </div>
</template>

<script>
import jsPDF from "jspdf";

export default {
    name: 'VoucherGenerator',
    data() {
        return {
            recipient: '',
            amount: null,
            description: '',
            voucher: null
        };
    },
    methods: {
        generateVoucher() {
            const code = 'VCH-' + Math.random().toString(36).substr(2, 8).toUpperCase();
            const date = new Date().toLocaleDateString();
            this.voucher = {
                code,
                recipient: this.recipient,
                amount: this.amount,
                description: this.description,
                date
            };
            // Optionally, reset form fields
            this.recipient = '';
            this.amount = null;
            this.description = '';
        },
        downloadPDF() {
            if (!this.voucher) return;
            const doc = new jsPDF();
            doc.setFontSize(16);
            doc.text("Fee Voucher", 10, 10);
            doc.setFontSize(12);
            doc.text(`Voucher Code: ${this.voucher.code}`, 10, 25);
            doc.text(`Recipient: ${this.voucher.recipient}`, 10, 35);
            doc.text(`Amount: $${this.voucher.amount}`, 10, 45);
            doc.text(`Description: ${this.voucher.description}`, 10, 55);
            doc.text(`Date: ${this.voucher.date}`, 10, 65);
            doc.save(`voucher_${this.voucher.code}.pdf`);
        }
    }
};
</script>

<style scoped>
.voucher-generator {
    max-width: 400px;
    margin: 0 auto;
    padding: 1rem;
    border: 1px solid #ddd;
    border-radius: 8px;
    background: #fafafa;
}
.voucher-generator form > div {
    margin-bottom: 1rem;
}
.voucher-generator label {
    display: block;
    margin-bottom: 0.25rem;
}
.voucher-generator input {
    width: 100%;
    padding: 0.5rem;
    box-sizing: border-box;
}
.voucher-preview {
    margin-top: 2rem;
    padding: 1rem;
    border: 1px dashed #aaa;
    background: #fff;
}
</style>