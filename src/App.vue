<template>
  <div class="page_container" v-if="pergunta != undefined">
    <section class="placar_container">
      <div class="placar">
        <div>
          <h3 class="pontuacao">{{ player }}</h3>
          <p>Player</p>
        </div>

        <div>
          <h3 class="pontuacao">{{ computador }}</h3>
          <p>Computer</p>
        </div>
      </div>

      <div class="temporizador_container">
        <p>Tempo restante</p>
        <p class="temporizador">{{ tempoRestante }}s</p>
      </div>
    </section>

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
          Enviar Resposta
        </button>
      </div>
    </section>

    <section v-if="respostaSubmetida">
      <h3 v-if="alternativaEscolhida === respostaCorreta">
        Parabéns! Sua resposta está correta!
      </h3>

      <h3
        v-else
        v-html="
          'Sua resposta está incorreta! A resposta correta seria  ' +
          respostaCorreta
        "
      ></h3>

      <div class="position_buton">
        <button class="btn" @click="novaPergunta()">Avançar</button>
      </div>
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
        }, 3000); // atraso 2 segundos para carregar a pergunta
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
  width: 800px;

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
  width: 850px;
  height: 150px;
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.position_buton {
  display: flex;
  justify-content: center;
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

h3 {
  font-family: Georgia, "Times New Roman", Times, serif;
  font-weight: 400;
}

.placar_container {
  display: flex;
  flex-direction: row;
  column-gap: 80px;
}
.placar {
  display: flex;
  flex-direction: row;
  gap: 30px;
  text-align: center;
}

.pontuacao {
  border: 2px solid #ff7f11;
  padding: 3px;

  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  width: 55px;
  height: 50px;
}
.temporizador_container {
  padding-top: 5px;
  text-align: center;
}

.temporizador {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  font-size: xx-large;
  font-weight: 600;
  margin-top: -5px;
}
</style>
