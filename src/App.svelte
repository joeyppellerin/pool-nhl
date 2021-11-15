<script>
  import { onMount } from 'svelte';
  import Footer from './components/footer/footer.svelte';
  import FormulaireInitiale from './components/formulaire-initial/formulaire-initial.svelte';
  import ListePooler from './components/liste-pooler/liste-pooler.svelte';
  import NavBar from './components/nav-bar/nav-bar.svelte';

  let isInitialFormShowed = true;
  let initialPoolObj = null;

  const CLE_STORAGE = 'initialForm';

  onMount(() => {
    isInitialFormShowed = true;
    initialPoolObj = JSON.parse(sessionStorage.getItem(CLE_STORAGE)) ?? null;
    if (initialPoolObj) {
      isInitialFormShowed = false;
    }
  });

  const reinitialiserPool = () => {
    sessionStorage.removeItem(CLE_STORAGE);
    isInitialFormShowed = true;
  }

  const initPoolForm = (event) => {
    initialPoolObj = event.detail
    sessionStorage.setItem(CLE_STORAGE, JSON.stringify(initialPoolObj));
    isInitialFormShowed = false;
  }
</script>

<div class="flex flex-col h-screen justify-between">
  <header class="h-18 bg-black shadow-lg text-center">
    <NavBar />
  </header>
  <main class="mt-8 mb-auto">
    {#if isInitialFormShowed}
    <FormulaireInitiale on:createPool={initPoolForm} />
    {:else}
      <div class="mb-8 px-4 text-right">
        <button on:click|preventDefault={reinitialiserPool} class="bg-yellow-400 hover:bg-gray-900 text-black hover:text-yellow-400 font-bold py-2 px-4 rounded">
          Réinitialiser le pool
        </button>
      </div>
      <ListePooler initialPoolObj={initialPoolObj} />
    {/if}
  </main>
  <footer class="h-8">
    <Footer />
  </footer>
</div>

<style lang="postcss" global>
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
</style>