<script>
  // Importing modules
  import { goto } from "@sapper/app";
  import { onMount } from "svelte";

  // Importing components
  import Spinner from "../../components/Loader/Spinner.svelte";
  import Background from "../../components/Layout/Background.svelte";

  // Redirecting user to needed page
  onMount(() => {
    goto(`https://lococovu.me/authorize?return=${encodeURIComponent('https://gaming.lococovu.me/app')}`);
  });
</script>

<!-- Page's Layout -->
<Background />

<main class="w-full h-screen flex justify-center items-center">
  <!-- Loader -->
  <Spinner size="20" color="#fff" />
</main>