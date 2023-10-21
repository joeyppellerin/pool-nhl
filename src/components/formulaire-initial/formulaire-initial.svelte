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

<div class=" container flex items-center justify-center">
  <form on:submit|preventDefault={submit}>
    <div class="p-10">
      <!--Form Card-->
      <div class="max-w-lg rounded-lg overflow-hidden shadow-lg">
        <div class="px-6 py-4 bg-yellow-500">
          <div class="mb-2">
            <h2 class="font-bold text-xl">Création de l'affichage du pool</h2>
            <p class="text-md mt-2">Pour créer un nouveau pool, vous devez définir le cap salariale Maximal, le nom des participants du pool ainsi que le format du pool (par example, le nombre d'attaquants à sélectionner).</p>
          </div>
        </div>
        <div class="px-6 pt-4 pb-2">
          <!-- Champs d'entré du cap salarial -->
          <div class="mb-6">
            <div class="mb-2">
              <label class="block text-gray-700 text-md font-bold mb-2" for="cap-hit">
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
            <p class="text-sm">1 M$ = 1 000 000$ - Example : 81.5 M$ = 81 500 000$</p>
          </div>
          <!-- Champs d'entré des participants -->
          <div>
            <label class="block text-gray-700 text-md font-bold mb-2" for="participant-name">
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
          <div>
            <div class="flex flex-wrap">
              {#each participants as participant, i}
              <div>
                <div class="pt-3">
                  <div class="pr-1">
                    <div class="flex flex-nowrap">
                      <div>
                        <div class="rounded-full bg-yellow-500 px-3 py-1">
                          <span class="font-bold">{participant}</span>
                          <button on:click|preventDefault={() => deleteParticipant(i)} type="button" class="ml-2 inline w-7 h-7 bg-red-600 text-white rounded-full hover:bg-red-900 focus:outline-none focus:shadow-outlinep-2 shadow font-bold">
                            X
                          </button>
                        </div>
                      </div>
                      </div>
                    </div>
                </div>
              </div>
              {/each}
            </div>
          </div>
          {/if}
          <!-- Champs d'entré des catégories -->
          <fieldset class="mt-4" aria-labelledby="category-fields" aria-describedby="no-category-error">
            <legend>
              <p id="category-fields" class="font-bold text-lg">
                <span>Catégorie</span>{#if categories?.length > 1}<span>s</span>{/if}
              </p>
            </legend>
            {#if noCategoryError}
              <p id="no-category-error" class="text-red-500">Un minimum d'une catégorie est requise.</p>
            {/if}
            <div>
              <div class="mt-4 mb-2">
                <label class="block text-gray-700 text-md font-bold mb-2" for="category-name">
                  Nom de la catégorie
                </label>
                {#if fieldCategoryNameError}
                  <p id="required-field-name-error" class="text-red-500">Ce champs est requis.</p>
                {/if}
                <input aria-describedby="required-field-name-error" autocomplete="off" bind:value={categoryName} class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="category-name" type="text">
              </div>
              <p class="text-sm">Example : Attanquant, Défenseurs, Gardien de but, Équipe, etc.</p>
            </div>
            <div class="mt-4">
              <label class="block text-gray-700 text-md font-bold mb-2" for="category-number">
                Nombre de choix par catégorie
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
            <div class="flex flex-wrap">
              {#each categories as category, i}
              <div>
                <div class="pt-3">
                  <div class="pr-1">
                    <div class="flex flex-nowrap">
                      <div>
                        <div class="rounded-full bg-yellow-500 px-3 py-1">
                          <span class="font-bold">{category.name}</span>&nbsp;|&nbsp;Nombre de choix&nbsp;:&nbsp;<span class="font-bold">{category.playersLimit}</span>
                          <button on:click|preventDefault={() => deleteCategory(i)} type="button" class="ml-2 inline w-7 h-7 bg-red-600 text-white rounded-full hover:bg-red-900 focus:outline-none focus:shadow-outlinep-2 shadow font-bold">
                            X
                          </button>
                        </div>
                      </div>
                      </div>
                    </div>
                </div>
              </div>
              {/each}
            </div>
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
