<template>
  <main>
      <table>
        <caption>Liste des produits - page {{ data.page.number + 1 }} / {{ data.page.totalPages }}</caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <!-- Si le tableau des produits est vide -->
        <tr v-if="data.listeProduits.length === 0">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des produits n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit.nom">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
      </table>
    <div class="option">
      <button @click="chargeProduits(0)">
        ⇇
      </button>
      <button @click="data.page.number + 1 > 1 ? chargeProduits(data.page.number - 1) : ''">
        ←
      </button>
      <button @click="data.page.number + 1 < data.page.totalPages ? chargeProduits(data.page.number + 1) : ''">
        →
      </button>
      <button @click="chargeProduits(data.page.totalPages - 1)">
        ⇉
      </button>
    </div>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest, APIError } from "../api";


let data = reactive({
  // Les données saisies dans le formulaire
  // La liste des produits affichée sous forme de table
  listeProduits: [],
  // Informations de la page
  page: {}
});

function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}

function chargeProduits(nPage) {
  // Appel à l'API pour avoir la liste des produits
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(`/api/produits?page=${nPage}&size=5&sort=nom,desc`)
      .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.page = json.page;
      })
      .catch(showError);
}

// A l'affichage du composant, on affiche la liste
onMounted(() => {  chargeProduits(0);});
</script>


<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}
th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
.option button:hover{
  background: #00bd7e;
}
</style>