<template>
  <div class="page_container" v-if="pergunta != undefined">
    <PlagarJogo
      :player="player"
      :computador="computador"
      :tempoRestante="tempoRestante"
    />

    <section>
      <hr />
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

      <div class="position_buton">
        <button
          class="btn"
          @click="verificaResposta()"
          v-if="!respostaSubmetida"
        >
          Submit Response
        </button>
      </div>
    </section>

    <section v-if="respostaSubmetida">
      <h3 v-if="alternativaEscolhida === respostaCorreta">
        Congratulations! Your answer is correct!
      </h3>

      <h3
        v-else
        v-html="
          'Your answer is incorrect! The correct answer would be  ' +
          respostaCorreta
        "
      ></h3>

      <div class="position_buton">
        <button class="btn" @click="novaPergunta()">Next Question</button>
      </div>
    </section>
  </div>
</template>

<script>
import PlagarJogo from "./components/PlacarJogo.vue";

export default {
  name: "App",

  components: {
    PlagarJogo,
  },

  data() {
    return {
      pergunta: undefined,
      respostasErradas: undefined,
      respostaCorreta: undefined,
      respostasEmbaralhadas: undefined,
      alternativaEscolhida: undefined,
      respostaSubmetida: false,
      player: 0,
      computador: 0,
      tempoRestante: 10,
    };
  },
  created() {
    this.novaPergunta();
  },

  methods: {
    embaralhaRespostas() {
      this.respostasEmbaralhadas = [
        ...this.respostasErradas,
        this.respostaCorreta,
      ];
      this.respostasEmbaralhadas.sort(() => Math.random() - 0.5);
    },
    iniciaTemporizador() {
      this.tempoRestante = 10;
      this.intervalo = setInterval(() => {
        if (this.tempoRestante > 0) {
          this.tempoRestante -= 1;
        } else {
          clearInterval(this.intervalo);
          this.respostaSubmetida = true;
          this.computador++;
        }
      }, 1000);
    },

    novaPergunta() {
      this.alternativaEscolhida = undefined;
      this.respostaSubmetida = false;

      clearInterval(this.intervalo);
      this.iniciaTemporizador();

      this.axios
        .get(
          "https://opentdb.com/api.php?amount=10&category=11&difficulty=medium&type=multiple"
        )
        .then((response) => {
          this.pergunta = response.data.results[0].question;
          console.log(this.pergunta);
          this.respostasErradas = response.data.results[0].incorrect_answers;
          this.respostaCorreta = response.data.results[0].correct_answer;
          this.embaralhaRespostas();
        }, 3000)
        .catch(() => {
          alert("Aguarde um momento para carregar as perguntas.");
        });
    },

    selecionarResposta(resposta) {
      this.alternativaEscolhida = resposta;
    },

    verificaResposta() {
      if (this.alternativaEscolhida !== undefined) {
        this.respostaSubmetida = true;
        clearInterval(this.intervalo);
        if (this.alternativaEscolhida === this.respostaCorreta) {
          this.player++;
          console.log("Resposta correta!");
        } else {
          this.computador++;
          console.log("Resposta errada!");
        }
      } else {
        alert("Nenhuma alternativa foi escolhida.");
      }
    },

    beforeDestroy() {
      clearInterval(this.intervalo);
    },
  },
};
</script>
<style lang="scss" scoped>
.page_container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 20px;
  row-gap: 10px;
  min-width: 100vw;
  min-height: 100vh;
  background-color: #cecece;
}

.opc_respostas {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  width: 100%;
  max-width: 800px;

  li {
    width: 325px;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgb(255, 255, 255);
    box-shadow: 0 5px 8px rgba(0, 0, 0, 0.1), 0 2px 4px rgba(0, 0, 0, 0.08);
    border-radius: 10px;
    font-size: 1rem;
    font-family: "Gowun Dodum", sans-serif;
    font-weight: 300;
    font-style: normal;
    cursor: pointer;
  }
}

.pergunta {
  font-family: Georgia, "Times New Roman", Times, serif;
  font-size: 1.2rem;
  font-weight: 500;
  color: rgb(0, 0, 0);
  width: 90%;
  max-width: 850px;
  height: auto;
  padding: 20px;
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.position_buton {
  display: flex;
  justify-content: center;
  width: 100%;
}

.btn {
  width: 100%;
  max-width: 300px;
  height: 50px;
  border: none;
  background-color: #094074;
  color: white;
  border-radius: 5px;
  margin-top: 30px;
  cursor: pointer;
}

h3 {
  font-family: Georgia, "Times New Roman", Times, serif;
  font-weight: 400;
  text-align: center;
  padding: 10px;
}

/* Media Queries para responsividade */
@media (max-width: 768px) {
  .opc_respostas {
    width: 80%;
    margin-left: 5%;

    li {
      width: 250px;
      font-size: 0.9rem;
    }
  }

  .pergunta {
    font-size: 1rem;
    padding: 15px;
  }

  .btn {
    max-width: 250px;
    font-size: 0.9rem;
  }
  h3 {
    font-size: 16px;
    font-weight: 300;
    width: 90%;
  }
}

@media (max-width: 480px) {
  .opc_respostas {
    flex-direction: column;

    li {
      font-size: 0.8rem;
      padding: 2px;
      width: 95%;
      height: 40px;
    }
  }

  .pergunta {
    font-size: 0.9rem;
    padding: 10px;
    width: 90%;
  }
  h3 {
    font-weight: 300;
    font-size: small;
    width: 90%;
  }

  .btn {
    width: 95%;
    font-size: 0.8rem;
    margin-bottom: 15px;
  }
}
</style>
