<script lang="ts">
  import { onMount } from 'svelte';
  import { fade, fly } from 'svelte/transition';
  import NetworkBackground from './components/NetworkBackground.svelte';

  let visible = false;
  let showTooltip = false;

  onMount(() => {
    visible = true;
  });
</script>

<div class="relative min-h-screen bg-black text-white selection:bg-white/25 selection:text-white flex flex-col justify-between font-sans overflow-x-hidden">
  <!-- Dynamic Svelte Network Background -->
  <NetworkBackground />

  <!-- Main Content Container -->
  <div class="relative z-10 w-full max-w-4xl mx-auto px-6 sm:px-12 md:px-16 pt-16 sm:pt-24 md:pt-32 pb-12 flex-grow flex flex-col justify-between">
    
    {#if visible}
      <main class="flex flex-col space-y-12 sm:space-y-16">

        <!-- Logo Brand Icon -->
        <div in:fly={{ y: 15, duration: 700, delay: 100 }} class="w-fit">
          <a
            href="/"
            aria-label="imput home"
            class="block cursor-pointer rounded-lg p-1 transition-transform duration-200 hover:scale-105 active:scale-98 focus:outline-none focus-visible:ring-2 focus-visible:ring-white/50"
          >
            <!-- Logo -->
            <svg
              viewBox="0 0 512 512"
              xmlns="http://www.w3.org/2000/svg"
              class="w-14 h-auto md:w-16 text-white fill-current transition-opacity duration-200 hover:opacity-90"
            >
              <!-- Left branch -->
              <path d="M140 150 L220 210 L220 110 L270 110 L270 260 L140 180 Z" />

              <!-- Right branch -->
              <path d="M372 150 L292 210 L292 110 L242 110 L242 260 L372 180 Z" />

              <!-- Bottom branch -->
              <path d="M170 320 L256 270 L342 320 L320 360 L280 340 L280 430 L232 430 L232 340 L192 360 Z" />
            </svg>
          </a>
        </div>

        <!-- Copy Sections -->
        <div class="flex flex-col space-y-8 max-w-2xl sm:max-w-3xl">

          <!-- Paragraph 1 -->
          <p
            in:fly={{ y: 15, duration: 700, delay: 220 }}
            class="text-xl sm:text-[23px] md:text-[25px] font-normal leading-[1.45] tracking-tight text-[#ECECEC]"
          >
            We develop fast, private, and free software.
          </p>

          <!-- Paragraph 2 -->
          <p
            in:fly={{ y: 15, duration: 700, delay: 460 }}
            class="text-xl sm:text-[23px] md:text-[25px] font-normal leading-[1.45] tracking-tight text-[#ECECEC]"
          >
            We are not hiring, yet

            <span
              class="relative inline-block"
              on:mouseenter={() => (showTooltip = true)}
              on:mouseleave={() => (showTooltip = false)}
            >
              <span class="cursor-help select-none text-[#8E8E8E] underline decoration-dotted decoration-zinc-600 underline-offset-4 transition-colors duration-200 hover:text-white">
                (...yet?)
              </span>

              <!-- Tooltip -->
              {#if showTooltip}
                <span
                  transition:fade={{ duration: 150 }}
                  class="absolute left-1/2 bottom-full z-30 mb-3 -translate-x-1/2 whitespace-nowrap rounded border border-neutral-800 bg-neutral-900 px-2.5 py-1.5 font-mono text-xs text-neutral-300 shadow-xl"
                >
                  Maybe in the future!
                </span>
              {/if}
            </span>.
          </p>
        </div>
      </main>
    {/if}

    <!-- Footer -->
    {#if visible}
      <footer
        in:fade={{ duration: 1000, delay: 700 }}
        class="mt-20 sm:mt-24 md:mt-32 flex flex-wrap gap-x-6 gap-y-3 border-t border-neutral-900/40 pt-6 text-sm font-normal tracking-tight text-zinc-500"
      >
        <a
          href="https://github.com/imputernet"
          target="_blank"
          rel="noopener noreferrer"
          class="transition-colors duration-200 hover:text-white focus:outline-none focus-visible:text-white"
        >
          GitHub
        </a>

        <a
          href="https://x.com/imputernet"
          target="_blank"
          rel="noopener noreferrer"
          class="transition-colors duration-200 hover:text-white focus:outline-none focus-visible:text-white"
        >
          X/Twitter
        </a>
      </footer>
    {/if}

  </div>
</div>