<template>
  <nav class="navbar navbar-expand-sm bg-dark navbar-dark">
  <a class="navbar-brand" href="#">Tutorial Sharing Order Item</a>
  
</nav>
<div class="container-fluid py-4">
  <h2 class="py-2 text-center">Tutorail Sharing Order System</h2>
  <div class="row">
    <div class="col-md-7 border">
      <div class="row">
        <div class="col-sm-2 p-1 mb-1" v-for="val in item" :key="val.id">
          <a href="#" class="text-decoration-none" v-on:click="orderItem(val.id)">
            <div class="card">
              <div class="card-body text-center p-1">
                <img :src="require(`@/assets/${val.img}`)" width="50" height="50">
                <p class="text-muted mt-2">{{ val.title }}</p>
              </div>
            </div>
          </a>
        </div>
      </div>
    </div>
    <div class="col-md-5">
      <table class="table">
        <thead>
          <tr>
            <th>No</th>
            <th>Title</th>
            <th>Qty</th>
            <th>Price $</th>
            <th>Total $</th>
            <th>Del</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(obj, index) in tableData" :key="obj.id">
            <td>{{ obj.id }}</td>
            <td>{{ obj.title }}</td>
            <td>{{ obj.qty }}</td>
            <td>{{ obj.price }}</td>
            <td>{{ obj.total }}</td>
            <td class="text-right">
              <button class="btn btn-danger btn-sm" v-on:click="removeRow(index)">Del</button>
            </td>
          </tr>
          <tr>
            <td>Total $</td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td class="text-right">{{ total_amount }}</td>
          </tr>
        </tbody>
      </table>
      <button class="btn btn-primary float-right" data-toggle="modal" data-target="#myModal"
        :disabled="disable_payment == false" v-on:click="payment()">Payment</button>
    </div>
  </div>
  <div class="modal" id="myModal">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header p-2">
          <h4 class="modal-title">Payment</h4>
          <button type="button" class="close" data-dismiss="modal">&times;</button>
        </div>
        <div class="modal-body">
          <div class="form-group">
            <label for="deposit">Deposit</label>
            <input type="number" class="form-control" v-model="deposit" v-on:keyup="depositkey">
          </div>
          <div class="form-group">
            <label for="deposit">Deposit</label>
            <input type="number" class="form-control" disabled placeholder="return amount" v-model="total_return">
          </div>
          <div class="form-group">
            <label for="totalamount">Total Amount</label>
            <input type="number" class="form-control" disabled v-model="total_amount">
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-primary" :disabled="disable_save == false" v-on:click="printInvoice">Save</button>
        </div>

      </div>
    </div>
  </div>
  <div id="printInvoice" style="display: none;">
    <div style="display: flex;">
      <img :src="require('@/assets/logo.png')" alt="" width="45" height="50">
      <h3 style="margin-left: 25px;">Tutorail Sharing Order System</h3>
    </div>
    <br>
    <div>Deposit: {{ deposit }} $</div>
    <div>Total Return {{ total_return }} $</div>
    <div>Total Amount {{ total_amount }} $</div>
    <table class="table">
        <thead>
          <tr>
            <th>No</th>
            <th>Title</th>
            <th>Qty</th>
            <th>Price $</th>
            <th>Total $</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="obj in tableData" :key="obj.id">
            <td>{{ obj.id }}</td>
            <td>{{ obj.title }}</td>
            <td>{{ obj.qty }}</td>
            <td>{{ obj.price }}</td>
            <td>{{ obj.total }}</td>
          </tr>
          <tr>
            <td>Total $</td>
            <td></td>
            <td></td>
            <td></td>
            <td>{{ total_amount }}</td>
          </tr>
        </tbody>
      </table>

  </div>
</div>
</template>

<script>
import itemdata from './json/item.json';
export default {
  name: 'App',
  data() {
    return {
      item: itemdata,
      tableData: [],
      total_amount: 0.00,
      total_return: 0.00,
      deposit : "",
      disable_payment: false,
      disable_save :false
    }
  },
  methods: {
    orderItem(val) {
      var app = this;
      let itemData = app.item.find(x => x.id == val);
      var index = app.tableData.findIndex(item => item.id === val);

      if (index != -1) {
        var getqty = parseInt(app.tableData[index].qty);
        app.tableData[index].qty = getqty += 1;
        app.tableData[index].total = Number(app.tableData[index].price * getqty).toFixed(2);
      } else {
        app.tableData.push({
          id: itemData.id,
          title: itemData.title,
          price: itemData.price,
          qty: 1,
          total: (itemData.price * 1)
        })
      }
      app.updateLastTotal();
      app.disable_payment = true;
    },
    removeRow(index) {
      this.tableData.splice(index, 1);
      this.updateLastTotal();
    },
    updateLastTotal() {
      var app = this;
      var total = 0;
      for (var i = 0; i < app.tableData.length; i++) {
        total += Number(app.tableData[i].price * app.tableData[i].qty);
      }
      app.total_amount = Number(total).toFixed(2);
    },
    depositkey(e){
      var val =+ e.target.value;
      if(val > this.total_amount){
        this.disable_save = true;
        this.total_return = Number(val - this.total_amount).toFixed(2);
      }else{
        this.disable_save = false;
        this.total_return = 0;
      }
    },
    payment() {
      this.total_return = 0,
      this.deposit = 0
    },
    printInvoice(){
      const printContents = document.getElementById('printInvoice').innerHTML;
      const originalContents = document.body.innerHTML;
        document.body.innerHTML = printContents;
        window.print();
        document.body.innerHTML = originalContents;

        //after print
        window.location.reload(true);
    }
  }
}
</script>