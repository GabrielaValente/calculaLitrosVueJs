<template>
  <div class="container">
    <div class="card-step">
      <div class="box-text">
        <label>Insira o volume do galão</label>
        <input v-model="volumeGalao" type="number" min="1" />
      </div>

      <div class="box-text">
        <label>Insira a quantidade de garrafas</label>
        <input v-model="quantidadeGarrafas" type="number" min="1" />
      </div>

      <button @click="armazenaQuantidadeGarrafas">Salvar quantidade</button>
    </div>

    <div class="card-step" v-if="exibirGarrafas">
      <div
        class="box-text"
        v-for="garrafa in quantidadeGarrafas"
        :key="garrafa.idGarrafa"
      >
        <label>Insira o volume da garrafa {{ garrafa.idGarrafa }}</label>
        <input v-model.number="garrafa.litro" type="number" />
      </div>

      <button @click="armazenaLitrosGarrafas">Salvar garrafas</button>
    </div>

    <div class="card-result" v-if="exibirResultado">
      <h1>Volumes utilizados: {{ resultadoVolumesUtilizados }}</h1>
      <h1>Sobra: {{ resultadoSobra }}</h1>
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    volumeGalao: "",
    quantidadeGarrafas: [],
    exibirGarrafas: false,
    exibirResultado: false,
    resultadoVolumesUtilizados: "",
    resultadoSobra: "",
  }),

  methods: {
    // Função que realiza a construção de um array com os dados das garrafas com ID e LITRO de cada item.
    armazenaQuantidadeGarrafas() {
      // Construção do array
      const listaDeGarrafas = [];

      // For para criação do objeto para o array
      for (let garrafa = 1; garrafa <= this.quantidadeGarrafas; garrafa++) {
        // Criação do objeto
        const novaGarrafa = {
          idGarrafa: garrafa,
          litro: "",
        };

        // Atribui o objeto dentro do array (listaDeGarrafas)
        listaDeGarrafas.push(novaGarrafa);
      }

      // Atribui para a variavel quantidadeGarrafas os valores do array listaDeGarrafas.
      this.quantidadeGarrafas = listaDeGarrafas;

      // Ativa a exibição dos inputs para inserir os volumes de cada garrafa.
      this.exibirGarrafas = true;
    },

    // Realiza o calculo dos litros para preencher o galão.
    armazenaLitrosGarrafas() {
      // Array de garrafas e o volume de cada uma.
      var garrafasVolume = [];
      var volumeGalao = this.volumeGalao;

      // ForEach para preencher o garrafasVolume apenas com o volume de cada garrafa.
      this.quantidadeGarrafas.forEach((element) => {
        // Push para inserir os volumes no array. / if para verificar se o volume é maior que o desejado.
        if (element.litro <= volumeGalao) {
          garrafasVolume.push(element.litro);
        }
      });

      // Função que retorna a garrafa com o volume mais próximo volume do galão
      var garrafaVolumeMaisProximoDoGalao = garrafasVolume.reduce(
        (prev, curr) => {
          return Math.abs(curr - volumeGalao) < Math.abs(prev - volumeGalao)
            ? curr
            : prev;
        }
      );

      // Realiza a subtração do volume do galão pelo retorno do volume da garrafa mais próxima.
      var volumeRestanteGalao = volumeGalao - garrafaVolumeMaisProximoDoGalao;

      // Armazena o volume utilizados na subtração do volume do galão.
      var volumesUtilizados = [garrafaVolumeMaisProximoDoGalao];

      // Verifica se o galão foi preenchido na primeira tentativa.
      if (volumeRestanteGalao > 0) {
        do {
          var garrafaUtilizada = garrafasVolume.reduce((prev, curr) => {
            return Math.abs(curr - volumeRestanteGalao) <
              Math.abs(prev - volumeRestanteGalao)
              ? curr
              : prev;
          });

          volumesUtilizados.push(garrafaUtilizada);
          volumeRestanteGalao = volumeRestanteGalao - garrafaUtilizada;
        } while (
          // Enquanto o valor restante do galão for maior do que 0 continue buscando o valor mais próximo para preencher o galão.
          volumeRestanteGalao > 0
        );

        // Exibe o componente do resultado com a sobra da garrafa.
        this.exibirResultado = true;

        // Realiza a soma dos volumes utilizados / A = Primeira posição do array, B = Segunda posição do array.
        // Método 'REDUCE' é utilizado para armazenar e somar valores em um array
        // (Exemplo reduce: [10, 7, 4] => A = 10 + B = 7 = 17 => [17, 3] => A = 17 + B = 4 => [21]).
        var somaDoVolumeUtilizado = volumesUtilizados.reduce(
          (a, b) => a + b,
          0
        );

        this.resultadoVolumesUtilizados = volumesUtilizados;
        this.resultadoSobra = somaDoVolumeUtilizado - volumeGalao;
      }
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  font-family: "Montserrat", sans-serif;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 100vh;
  height: 100%;
  background: #eee4ab;
}

.card-step {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 25%;
  padding: 10px;
  background: #fafafa;
  margin-top: 25px;
  border-radius: 10px;
}

.card-step > .box-text {
  display: flex;
  flex-direction: column;
  width: 100%;
  margin-bottom: 10px;
}

.card-step > .box-text > label {
  font-weight: 500;
  margin-bottom: 5px;
}

.card-step > .box-text > input {
  height: 30px;
  border: 1px solid gray;
  border-radius: 7px;
  margin-bottom: 10px;
  padding: 0 10px;
  font-size: 16px;
  font-weight: 700;
}

.card-step > button {
  height: 35px;
  width: 100%;
  background: #99c4c8;
  color: #175e65;
  border: 1px solid transparent;
  border-radius: 7px;
  font-weight: 700;
  cursor: pointer;
  transition: 500ms;
}

.card-step > button:hover {
  opacity: 0.8;
  transition: 500ms;
}

.card-result {
  background: #e5cb9f;
  width: 45%;
  padding: 15px;
  border-radius: 10px;
  margin-top: 25px;
}

.card-result > h1 {
  color: #121212;
}
</style>
