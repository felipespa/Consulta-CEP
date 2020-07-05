<template>
  <div class="container">
    <h1>Consulta Automática por Cep</h1>
    <form class="inputContainer">
      <div class="input cep">
        <input
          id="cep"
          ref="cep"
          class="input"
          pattern=".+"
          required
          type="text"
          v-mask="mask"
          v-model="endereco.cep"
          @keyup="search"
        />
        <label for="cep">CEP</label>
      </div>

      <div class="input">
        <input
          id="logradouro"
          class="input"
          pattern=".+"
          required
          type="text"
          ref="logradouro"
          :value="endereco.logradouro"
        />
        <label for="logradouro">Logradouro</label>
      </div>

      <div class="input">
        <input id="numero" class="input" pattern=".+" type="text" required ref="numero" />
        <label for="numero">Número</label>
      </div>

      <div class="input">
        <input id="complemento" class="input" pattern=".+" required type="text" />
        <label for="complemento">Complementos</label>
      </div>

      <div class="input">
        <input id="bairro" class="input" pattern=".+" required type="text" :value="endereco.bairro" />
        <label for="bairro">Bairro</label>
      </div>

      <div class="input">
        <input id="cidade" class="input" pattern=".+" required type="text" :value="endereco.cidade" />
        <label for="cidade">Cidade</label>
      </div>

      <div class="input">
        <input id="uf" class="input" pattern=".+" required type="text" :value="endereco.uf" />
        <label for="uf">UF</label>
      </div>
    </form>

    <div class="errorContainer" v-if="error == true">
      <div class="error">
        <div class="card-header">
          <h1>Erro</h1>
        </div>
        <div class="card-body">
          <h2>Não foi possível localizar um endereco com o CEP digitado, verifique os números e tente novamente.</h2>
        </div>
        <div class="card-footer">
          <button @click="fechar(error = !error)">FECHAR</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { mask } from "vue-the-mask";

export default {
  data() {
    return {
      mask: "#####-###",
      error: false,
      endereco: {
        cep: "",
        cidade: "",
        logradouro: "",
        bairro: "",
        uf: ""
      }
    };
  },
  methods: {
    search() {
      if (this.endereco.cep.length >= 9) {
        axios
          .get(
            "https://webmaniabr.com/api/1/cep/" +
              this.endereco.cep +
              "/?app_key=Ol6XxZRGNWRmpYJAU9B6ae80IeNpFMLC&app_secret=kaeemkiVGmjcsyyB58x2c7aaGwfvFWnj4EJq2I5BJrcovQ5v"
          )
          .then(response => {
            if (response.data.cep == null) {
              Object.keys(this.endereco).forEach(key => {
                this.endereco[key] = "";
              });

              return (this.error = !this.error);
            } else {
              this.endereco.uf = response.data.uf;
              this.endereco.cidade = response.data.cidade;
              this.endereco.logradouro = response.data.endereco;
              this.endereco.bairro = response.data.bairro;
              this.endereco.uf = response.data.uf;

              if (this.endereco.logradouro == "") {
                this.$refs.logradouro.focus();
              } else {
                this.$refs.numero.focus();
              }
            }
          });
      }
    }
  },
  fechar(msg) {
    this.error = msg;
    this.$refs.cep.focus();
  },
  directives: {
    mask
  }
};
</script>

<style lang="sass" scoped>
.container
    text-align: center
    max-width: 70%
    margin: 70px auto 0 auto
    h1
        color: #41B883
    h2
        margin: 0

.inputContainer
    display: grid
    grid-template-columns: 1fr 1fr 1fr
    grid-template-rows: 5vh 5vh
    grid-gap: 40px

.input
    position: relative

.cep
    grid-column: 1 / 4
    width: 31.3%

input
    border: 0
    border-bottom: 2px solid #FFF
    outline: none
    transition: .2s ease-in-out
    box-sizing: border-box
    background: transparent
    color: #FFF

label
    top: 0
    left: 0
    right: 0
    color: #616161
    display: flex
    align-items: center
    position: absolute
    font-size: 1rem
    cursor: text
    transition: .2s ease-in-out
    box-sizing: border-box
    color: #FFF

input, label
    width: 100%
    height: 3rem
    font-size: 1rem

input:valid, input:focus
    border-bottom: 2px solid #3FA77D

input:valid + label, input:focus + label
    color: #3FA77D
    font-size: .8rem
    top: -30px
    pointer-events: none

.errorContainer
    width: 100%
    height: 100vh
    position: absolute
    top: 0
    left: 0
    background: rgb(38, 38, 38, 0.6)
    transition: 2s ease-out
.error
    width: 40%
    position: relative
    top: 20%
    margin: 20px auto 0 auto
    border-radius: 4px
    box-shadow: 0 3px 1px -2px rgba(0,0,0,.2), 0 2px 2px 0 rgba(0,0,0,.14), 0 1px 5px 0 rgba(0,0,0,.12)
    background: #E6E6E6
    .card-header
        text-align: left
        background: rgb(230, 230, 230)
        padding: 10px 24px 10px
        border-color: #e0e0e0
        background: #35495E
        color: #FFF
    .card-body
        text-align: center
        padding: 40px
        background: #e6e6e6
        border-bottom: 2px solid rgb(53, 73, 94, 0.4)
        color: rgb(38, 38, 38, 0.8)
    .card-footer
        padding: 10px
    .card-footer button
        height: 36px
        min-width: 84px
        font-size: 14px
        background-color: #41B883
        color: #FFF
        border: 0
        font-weight: 600
        cursor: pointer
</style>
