<script lang="ts">
import type IReceita from "@/interfaces/IReceita";
import BotaoPrincipal from "./BotaoPrincipal.vue";
import { obterReceitas } from "@/http";
import CardReceita from "./CardReceita.vue";
import type { PropType } from "vue";
import { itensDeLista1EstaoEmLista2 } from "@/operations/listas";

export default {
  props: {
    ingredientes: { type: Array as PropType<string[]>, required: true },
  },
  data() {
    return {
      receitasEncontradas: [] as IReceita[],
    };
  },
  async created() {
    const receitas = await obterReceitas();
    this.receitasEncontradas = receitas.filter((receita) => {
      const possoFazerReceita = itensDeLista1EstaoEmLista2(
        receita.ingredientes,
        this.ingredientes
      );
      return possoFazerReceita;
    });
  },
  components: { BotaoPrincipal, CardReceita },
  emits: ["editarReceitas"],
};
</script>

<template>
  <h1 class="cabecalho titulo-receitas">Receitas</h1>

  <p class="paragrafo-lg resultados-encontrados">
    Resultados encontrados: {{ receitasEncontradas.length }}
  </p>

  <div v-if="receitasEncontradas.length" class="receitas-wapper">
    <p class="paragrafo-lg informacoes">
      Veja as opcoes de receitas que enconrtamos com os ingredientes que voce
      tem por ai!
    </p>

    <ul class="receitas">
      <li v-for="receita of receitasEncontradas" :key="receita.nome">
        <CardReceita :receita="receita" />
      </li>
    </ul>
  </div>

  <BotaoPrincipal texto="Editar lista" @click="$emit('editarReceitas')" />
</template>

<style scoped>
.mostrar-receitas {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.titulo-receitas {
  color: var(--verde-medio, #3d6d4a);
  margin-bottom: 0.5rem;
}

.receitas {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  flex-wrap: wrap;
}

.receitas-wrapper {
  margin-bottom: 2rem;
}

.informacoes {
  margin-bottom: 2rem;
}

.receitas-nao-encontradas {
  margin-bottom: 2rem;
}

.receitas-nao-encontradas__info {
  margin-bottom: 0.5rem;
}
</style>
