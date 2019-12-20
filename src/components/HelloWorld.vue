<template>
  <div class="hello">
    <form>
      <div>
        <h5>ORDEN: {{order_id}}</h5>
      </div>
      <div>
        <h5>PRECIO: {{currency_code}} {{amount}}</h5>
      </div>
      <div>
        <h5>DETALLE: {{description}}</h5>
      </div>
      <div>
        <label>
          <span>Correo Electrónico</span>
          <input type="text" size="50" data-culqi="card[email]" id="card[email]" v-model="email" />
        </label>
      </div>
      <div>
        <label>
          <span>Número de tarjeta</span>
          <input
            type="text"
            size="20"
            data-culqi="card[number]"
            id="card[number]"
            v-model="creditcard"
          />
        </label>
      </div>
      <div>
        <label>
          <span>CVV</span>
          <input type="text" size="4" data-culqi="card[cvv]" id="card[cvv]" v-model="cvv" />
        </label>
      </div>
      <div>
        <label>
          <span>Fecha expiración (MM/YYYY)</span>
          <input size="2" data-culqi="card[exp_month]" id="card[exp_month]" v-model="month" />
          <span>/</span>
          <input size="4" data-culqi="card[exp_year]" id="card[exp_year]" v-model="year" />
        </label>
      </div>
      <div>
        <input type="number" size="4" v-model="installments" />
      </div>
    </form>
    <button id="btn_pagar" @click="payment">Pagar</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'HelloWorld',

  data() {
    return {
      order_id: 123,
      description: 'PRODUCTO ABC',
      amount: 1200,
      installments: 2,
      telefono: 986193329,
      currency_code: 'PEN',
      email: 'hola@trivelapp.com',
      /* data sensible */
      creditcard: '4111 1111 1111 1111',
      month: '09',
      year: '2020',
      cvv: '123',
    };
  },
  methods: {
    async getToken() {
      return new Promise((resolve) => {
        let c = 0;
        _Culqi.createToken();
        const checkToken = setInterval(() => {
          c++;
          if (c > 20) clearInterval(checkToken);
          if (_Culqi.token) {
            clearInterval(checkToken);
            console.log('token en variable', _Culqi.token);
            resolve(_Culqi.token);
          } else {
          }
        }, 1000);
      });
    },
    async payment() {
      const resultado = await this.getToken();
      if (resultado) {
        // ¡Objeto Token creado exitosamente!
        const token = resultado.id;
        const response = await axios({
          method: 'post',
          url: '/api/process/pay', // YOUR BACKEND
          data: {
            order_id: this.order_id,
            description: this.description,
            amount: this.amount,
            installments: this.installments,
            telefono: this.telefono,
            currency_code: this.currency_code,
            token,
          },
          headers: {
            'content-type': 'application/json',
          },
        });
        console.log(response.data);
      } else {
        console.log(_Culqi.error);
      }
    },
  },
};
</script>
