<script>
  import Footer from './components/footer/footer.svelte';
  import FormulaireInitiale from './routes/create-your-pool/+formulaire-initial.svelte';
  import NavBar from './components/nav-bar/nav-bar.svelte';

  let isInitialFormShowed = true;

  const initPoolForm = (event) => {
    const initialPoolObj = event.detail
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