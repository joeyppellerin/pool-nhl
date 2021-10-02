<script>
  import { createEventDispatcher } from 'svelte';
  import InitPoolForm from '../../models/initial-pool-form.js';
  import Category from './../../models/category.js';

  const dispatch = createEventDispatcher();

  let capHit;
  let participant;
  let participants = [];
  let categoryName;
  let categoryPlayersLimit;
  let categories = [];
  let capHitRequiredError = false;
  let fieldParticipantError = false;
  let noParticipantError = false;
  let noCategoryError = false;
  let fieldCategoryNameError = false;
  let fieldCategoryNumberError = false;

  const addParticipant = () => {
    if (participant) {
      fieldParticipantError = false;
      participants = [...participants, participant];
      participant = '';
    } else {
      fieldParticipantError = true;
    }
  }

  const deleteParticipant = (id) => {
    if (id > -1 && id < participants.length) {
      participants.splice(id, 1);
      participants = participants;
    }
  }

  const addCategory = () => {
    if (categoryName && categoryPlayersLimit && categoryPlayersLimit > 0 && categoryPlayersLimit < 100) {
      fieldCategoryNameError = false;
      fieldCategoryNumberError = false;

      categories = [...categories, new Category(categoryName, categoryPlayersLimit)];
      categoryName = '';
      categoryPlayersLimit = '';
    } else {
      if (!categoryName) {
        fieldCategoryNameError = true;
      }

      if (!categoryPlayersLimit || categoryPlayersLimit < 0 || categoryPlayersLimit > 100) {
        fieldCategoryNumberError = true;
      }
    }
  }

  const deleteCategory = (id) => {
    if (id > -1 && id < categories.length) {
      categories.splice(id, 1);
      categories = categories;
    }
  }

  const submit = () => {
    const isCapHitValid = capHit && typeof capHit == 'number' && capHit > 0;

    noParticipantError = false;
    noCategoryError = false;
    capHitRequiredError = false;

    if (isCapHitValid && participants?.length > 0 && categories?.length > 0) {
      const formulaire = new InitPoolForm(capHit.toFixed(3), participants, categories)
      dispatch('createPool', formulaire);

    } else {
      if (!isCapHitValid) {
        capHitRequiredError = true;
      }

      if (participants?.length < 1) {
        noParticipantError = true;
      }

      if (categories?.length < 1) {
        noCategoryError = true;
      }
    }
  }
</script>

<div class="container flex items-center justify-center">
  <form on:submit|preventDefault={submit}>
    <div class="p-10">
      <!--Form Card-->
      <div class="max-w-md rounded overflow-hidden shadow-lg">
        <div class="px-6 py-4 bg-yellow-500">
          <div class="mb-2">
            <h2 class="font-bold text-xl">Création de l'affichage du pool</h2>
            <p class="text-sm mt-2">Chaque participant possède une carte, dans laquelle s'affichera les joueurs choisis triés selon les catégories. (ex. Attaquants, Défensseur, Gardiens de but, etc.)</p>
          </div>
        </div>
        <div class="px-6 pt-4 pb-2">
          <!-- Champs d'entré du cap salarial -->
          <div class="mb-6">
            <div class="mb-2">
              <label class="block text-gray-700 text-sm font-bold mb-2" for="cap-hit">
                Cap salarial
              </label>
              {#if capHitRequiredError}
                <p id="cap-hit-error" class="text-red-500">Ce champs est requis et doit comporter un nombre supérieur à 0.</p>
              {/if}
              <div class="flex flex-row">
                <input id="cap-hit" type="number" step="0.001" aria-describedby="cap-hit-error" autocomplete="off" bind:value={capHit} class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                <span class="pt-2 pl-2">M$</span>
              </div>
            </div>
            <p class="text-xs">1 M$ = 1 000 000$ - Example : 81.5 M$ = 81 500 000$</p>
          </div>
          <!-- Champs d'entré des participants -->
          <div>
            <label class="block text-gray-700 text-sm font-bold mb-2" for="participant-name">
              Nom du participant
            </label>
            {#if noParticipantError}
              <p id="no-participant-error" class="text-red-500">Un minmum d'un participant est requis.</p>
            {/if}
            {#if fieldParticipantError}
              <p id="participant-required-field" class="text-red-500">Ce champs est requis.</p>
            {/if}
            <input aria-describedby="no-participant-error participant-required-field" autocomplete="off" bind:value={participant} class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="participant-name" type="text">
            <div class="text-right mt-2">
              <button on:click|preventDefault={addParticipant} class="bg-yellow-300 hover:bg-yellow-500 text-black font-bold py-2 px-4 rounded-full">
                Ajouter
              </button>
            </div>
          </div>
          <!-- Liste de participants -->
          {#if participants?.length > 0}
            <div class="pt-2 pb-4">
              <ul>
                {#each participants as participant, i}
                  <div class="pt-2">
                    <div>
                      <li class="inline mr-1">
                        <span class="rounded-full bg-yellow-500 px-3 py-1">
                          {participant}
                        </span>
                      </li>
                      <button on:click|preventDefault={() => deleteParticipant(i)} type="button" class="inline w-7 h-7 bg-red-600 text-white rounded-full hover:bg-red-900 focus:outline-none focus:shadow-outlinep-2 shadow font-bold">
                        X
                      </button>
                    </div>
                  </div>
                {/each}
              </ul>
            </div>
          {/if}
          <!-- Champs d'entré des catégories -->
          <fieldset class="mt-4" aria-labelledby="category-fields" aria-describedby="no-category-error">
            <legend>
              <p id="category-fields" class="font-bold text-lg">Catégorie</p>
            </legend>
            {#if noCategoryError}
              <p id="no-category-error" class="text-red-500">Un minimum d'une catégorie est requise.</p>
            {/if}
            <div class="mt-4">
              <label class="block text-gray-700 text-sm font-bold mb-2" for="category-name">
                Nom de la catégorie
              </label>
              {#if fieldCategoryNameError}
                <p id="required-field-name-error" class="text-red-500">Ce champs est requis.</p>
              {/if}
              <input aria-describedby="required-field-name-error" autocomplete="off" bind:value={categoryName} class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="category-name" type="text">
            </div>
            <div class="mt-4">
              <label class="block text-gray-700 text-sm font-bold mb-2" for="category-number">
                Nombre de joueurs par catégorie
              </label>
              {#if fieldCategoryNumberError}
                <p id="required-field-limit-error" class="text-red-500">Ce champs est requis.</p>
              {/if}
              <input aria-describedby="required-field-limit-error" autocomplete="off" bind:value={categoryPlayersLimit} class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="category-number" type="number">
              <div class="text-right mt-2">
                <button on:click|preventDefault={addCategory} class="bg-yellow-300 hover:bg-yellow-500 text-black font-bold py-2 px-4 rounded-full">
                  Ajouter
                </button>
              </div>
            </div>
          </fieldset>
          <!-- Liste de catégories -->
          {#if categories?.length > 0}
          <div>
            <ul>
              {#each categories as category, i}
                <div class="pt-3">
                  <li class="inline pr-1">
                    <span class="rounded-full bg-yellow-500 px-3 py-1">
                      <span class="font-bold">{category.name}</span> | Limite de joueurs: <span class="font-bold">{category.playersLimit}</span>
                    </span>
                  </li>
                  <button on:click|preventDefault={() => deleteCategory(i)} type="button" class="inline w-7 h-7 bg-red-600 text-white rounded-full hover:bg-red-900 focus:outline-none focus:shadow-outlinep-2 shadow font-bold">
                    X
                  </button>
                </div>
              {/each}
            </ul>
          </div>
        {/if}
          <!-- Bouton pour soumettre le formulaire -->
          <div class="text-center mt-8 mb-2">
              <input type="submit" value="Soumettre le formulaire" id="submit-button" class="bg-green-400 hover:cursor-pointer hover:bg-green-700 text-black hover:text-white font-bold py-2 px-4 rounded">
          </div>
        </div>
      </div>
    </div>
  </form>
</div>
