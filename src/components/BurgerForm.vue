<template>
  <Message v-bind:msg="msg" v-show="msg" />
  <div>
    <form id="burger-form" v-on:submit.stop.prevent="createBurger($event)">
      <div class="input-container">
        <label for="nome">Nome do cliente:</label>
        <input
          type="text"
          id="nome"
          name="nome"
          v-model="nome"
          placeholder="Digite o seu nome aqui"
        />
      </div>
      <div class="input-container">
        <label for="pao">Escolha o pão:</label>
        <select name="pao" id="pao" v-model="pao">
          <option value="null">Selecione o seu p&atilde;o</option>
          <option
            v-bind:value="pao.tipo"
            v-for="pao in paes"
            v-bind:key="pao.id"
          >
            {{ pao.tipo }}
          </option>
        </select>
      </div>
      <div class="input-container">
        <label for="carne">Escolha a carne:</label>
        <select name="carne" id="carne" v-model="carne">
          <option value="null">Selecione o tipo de carne</option>
          <option
            v-for="carne in carnes"
            v-bind:key="carne.id"
            v-bind:value="carne.tipo"
          >
            {{ carne.tipo }}
          </option>
        </select>
      </div>
      <div id="opcionais-container" class="input-container">
        <label id="optionais-title">Selecione os opcionais:</label>
        <div
          class="checkbox-container"
          v-for="op in opcionaisdata"
          v-bind:key="op.id"
        >
          <input
            type="checkbox"
            name="opcionais"
            v-model="opcionais"
            v-bind:value="op.tipo"
          />
          <span>{{ op.tipo }}</span>
        </div>
      </div>
      <div class="input-container">
        <input type="submit" value="Criar meu burger" class="submit-btn" />
      </div>
    </form>
  </div>
</template>

<script>
import Message from "./Message";

var msgTimeout;

export default {
  name: "BurgerForm",
  components: {
    Message,
  },
  data() {
    return {
      paes: null,
      carnes: null,
      opcionaisdata: null,
      nome: null,
      pao: null,
      carne: null,
      opcionais: [],
      status: "Solicitado",
      msg: null,
    };
  },
  methods: {
    async getIngredientes() {
      const req = await fetch("http://localhost:3000/ingredientes");
      const data = await req.json();
      this.paes = data.paes;
      this.carnes = data.carnes;
      this.opcionaisdata = data.opcionais;
    },
    async createBurger(event) {
      const data = {
        nome: this.nome,
        carne: this.carne,
        pao: this.pao,
        opcionais: Array.from(this.opcionais),
        status: "Solicitado",
      };

      const dataJson = JSON.stringify(data);

      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: dataJson,
      });
      const res = await req.json();

      // colocar uma msg de sistema
      this.msg = `Pedido nº${res.id} realizado com sucesso`;
      clearTimeout(msgTimeout);
      msgTimeout = setTimeout(() => this.msg = null, 5000);

      // limpar os campos
      this.nome = "";
      this.carne = null;
      this.pao = null;
      this.opcionais = [];
    },
  },
  mounted() {
    this.getIngredientes();
  },
};
</script>

<style scoped>
#burger-form {
  max-width: 400px;
  margin: 0 auto;
}
.input-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}
label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #fcba03;
}
input,
select {
  padding: 5px 10px;
  width: 100%;
}

#opcionais-container {
  flex-direction: row;
  flex-wrap: wrap;
}
#optionais-title {
  width: 100%;
}

.checkbox-container {
  display: flex;
  align-items: flex-start;
  width: 50%;
  margin-bottom: 20px;
}

.checkbox-container span,
.checkbox-container input {
  width: auto;
}
.checkbox-container span {
  margin-left: 6px;
  font-weight: bold;
}
.submit-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16xp;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}
.submit-btn:hover {
  background-color: transparent;
  color: #222;
}
input[type="checkbox"] {
  cursor: pointer;
}
</style>
