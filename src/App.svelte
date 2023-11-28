<script lang="ts">
  import { HfInference } from "@huggingface/inference"; // importujemy potrzebne narzędzie z biblioteki HuggingFace
  let userInput: string;
  let imagePromise: Promise<string>;

  const hf = new HfInference(import.meta.env.VITE_HUGGING_FACE_ACCESS_TOKEN); // konfigurujemy narzędzie z naszym kluczem dostępu

  async function ask() {
    const blob = await hf.textToImage({
      // interesujący nas sposób interakcji z danym modelem - w tym wypadku wpisany tekst zamieniany na wygenerowany obraz
      model: "stabilityai/stable-diffusion-xl-base-1.0", // konkretny model ze strony HuggingFace
      inputs: userInput, // dane wejściowe użytkownika
    });
    return URL.createObjectURL(blob);
  }

  function generateImage() {
    imagePromise = ask();
  }
</script>

<main>
  <div class="centered">
    <div>
      <!-- Tutaj wpisz własny kod układu strony -->
      <input bind:value={userInput} />
      <button on:click={generateImage}>Generate</button>
    </div>
    <div class="generated-image">
    {#if imagePromise}
      {#await imagePromise}
        Loading...
      {:then result}
        <!-- coś się nie udało... -->
        <!-- svelte-ignore a11y-img-redundant-alt -->
        <img width=320 height=320 src={result} alt="Ai generated image"/>
      {:catch}
        Oops! Failes to generate the image
      {/await}
    {:else}
      No image generated
    {/if}
    </div>
  </div>
</main>

<style>
    /* Tu znajdzie się kod odpowiadający za wygląd strony */
main {
  background-color:#5555;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 100%;

}
input{
  padding: 4px;
  border: solid;
  border-radius: 1000px;
  text-align: center;
}
button{
  border: solid;
  border-radius: 1000px;
  text-align: center;
}
button:hover{
  box-shadow: 0 0 10px black;
}

.centered{
  background-color: #5555;
  padding: 20xp;
  border: solid;
  border-width: 8px;
  border-radius: 30px;
  box-shadow: 0 0 20xp black;
}
.generated-image{
  color:white;
  text-align: center;
}
</style>