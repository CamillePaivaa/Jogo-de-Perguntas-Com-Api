<template>
  <div class="page_container" v-if="pergunta != undefined">
    <h2 class="pergunta" v-html="pergunta"></h2>
    <div class="opc_respostas">
      <ul
        v-for="(resposta, index) in respostasEmbaralhadas"
        :key="index"
        @click="!respostaSubmetida && selecionarResposta(resposta)"
      >
        <li
          v-html="resposta"
          :style="{
            backgroundColor: respostaSubmetida
              ? alternativaEscolhida === resposta
                ? alternativaEscolhida === respostaCorreta
                  ? 'green'
                  : 'red'
                : ''
              : alternativaEscolhida === resposta
              ? '#ff7f11'
              : '',
          }"
        ></li>
      </ul>
    </div>

    <button class="btn" @click="verificaResposta()" v-if="!respostaSubmetida">
      Enviar Resposta
    </button>

    <section v-if="respostaSubmetida">
      <h2 v-if="alternativaEscolhida === respostaCorreta">
        Parabéns! Sua resposta está correta!
      </h2>

      <h2 v-else>
        Sua resposta está incorreta! A resposta correta seria
        {{ respostaCorreta }}
      </h2>

      <button class="btn" @click="incrementaPosicao()">Avançar</button>
    </section>
  </div>
</template>

<script>
export default {
  name: "App",

  data() {
    return {
      pergunta: undefined,
      respostasErradas: undefined,
      respostaCorreta: undefined,
      respostasEmbaralhadas: undefined,
      alternativaEscolhida: undefined,
      respostaSubmetida: false,
      index: 0,
    };
  },
  mounted() {
    this.axios
      .get(
        "https://opentdb.com/api.php?amount=10&category=11&difficulty=medium&type=multiple"
      )
      .then((response) => {
        this.pergunta = response.data.results[this.index].question;
        console.log(this.pergunta);
        this.respostasErradas =
          response.data.results[this.index].incorrect_answers;
        this.respostaCorreta = response.data.results[this.index].correct_answer;
        this.embaralhaRespostas();
      });
  },

  methods: {
    embaralhaRespostas() {
      this.respostasEmbaralhadas = [
        ...this.respostasErradas,
        this.respostaCorreta,
      ];
      this.respostasEmbaralhadas.sort(() => Math.random() - 0.5);
    },

    incrementaPosicao() {
      this.index++;
      this.alternativaEscolhida = undefined;
      this.respostaSubmetida = false;
      this.embaralhaRespostas();
    },

    selecionarResposta(resposta) {
      this.alternativaEscolhida = resposta;
    },

    verificaResposta() {
      if (this.alternativaEscolhida !== undefined) {
        this.respostaSubmetida = true;
        if (this.alternativaEscolhida === this.respostaCorreta) {
          console.log("Resposta correta!");
        } else {
          console.log("Resposta errada!");
        }
      } else {
        alert("Nenhuma alternativa foi escolhida.");
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.page_container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 50px;
  row-gap: 20px;
  min-width: 100vw;
  min-height: 100vh;
  background-color: #d4d2cb;
}

.opc_respostas {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  width: 780px;

  li {
    width: 325px;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgb(255, 255, 255);
    box-shadow: 0 5px 8px rgba(0, 0, 0, 0.1), 0 2px 4px rgba(0, 0, 0, 0.08);
    border-radius: 10px;
    font-size: larger;
    font-family: "Gowun Dodum", sans-serif;
    font-weight: 300;
    font-style: normal;
    cursor: pointer;
  }
}

.pergunta {
  font-family: Georgia, "Times New Roman", Times, serif;
  font-size: large;
  font-weight: 500;
  color: rgb(0, 0, 0);
  background-color: #ecce6afe;
  border: 2px solid rgb(255, 255, 255);
  width: 750px;
  height: 150px;
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 5px;
}

.btn {
  width: 300px;
  height: 50px;
  border: none;
  background-color: #094074;
  color: white;
  border-radius: 5px;
  margin-top: 30px;
  cursor: pointer;
}
</style>
