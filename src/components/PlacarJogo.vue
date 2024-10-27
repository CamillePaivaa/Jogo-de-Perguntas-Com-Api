<template>
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
      <p>Time Remaining</p>
      <p class="temporizador">{{ tempoRestante }}s</p>
    </div>
  </section>
</template>

<script>
export default {
  props: ["player", "computador", "tempoRestante"],
};
</script>

<style scoped>
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
