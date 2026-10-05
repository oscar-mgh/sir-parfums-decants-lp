<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue';

interface Perfume {
  id: number;
  name: string;
  brand: string;
  category: 'Diseñador' | 'Nicho' | 'Árabe';
  notes: string;
  priceFrom: number;
  image: string;
}

const perfumes: Perfume[] = [
  {
    id: 1,
    name: 'Sauvage Elixir',
    brand: 'Dior',
    category: 'Diseñador',
    notes: 'Canela, Nuez Moscada, Lavanda, Ámbar',
    priceFrom: 180,
    image: 'https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?w=400&auto=format&fit=crop&q=60',
  },
  {
    id: 2,
    name: 'Baccarat Rouge 540',
    brand: 'Maison Francis Kurkdjian',
    category: 'Nicho',
    notes: 'Azafrán, Jazmín, Madera de Ámbar, Cedro',
    priceFrom: 320,
    image: 'https://images.unsplash.com/photo-1592945403244-b3fbafd7f539?w=400&auto=format&fit=crop&q=60',
  },
  {
    id: 3,
    name: 'Khamrah',
    brand: 'Lattafa',
    category: 'Árabe',
    notes: 'Canela, Nuez Moscada, Praliné, Vainilla',
    priceFrom: 110,
    image: 'https://images.unsplash.com/photo-1588405748880-12d1d2a59f75?w=400&auto=format&fit=crop&q=60',
  },
  {
    id: 4,
    name: 'Bleu de Chanel Parfum',
    brand: 'Chanel',
    category: 'Diseñador',
    notes: 'Ralladura de Limón, Menta, Sándalo',
    priceFrom: 190,
    image: 'https://images.unsplash.com/photo-1541643600914-78b084683601?w=400&auto=format&fit=crop&q=60',
  },
  {
    id: 5,
    name: 'Aventus',
    brand: 'Creed',
    category: 'Nicho',
    notes: 'Piña, Bergamota, Abedul, Almizcle',
    priceFrom: 350,
    image: 'https://images.unsplash.com/photo-1615397349754-cfa2066a298e?w=400&auto=format&fit=crop&q=60',
  },
  {
    id: 6,
    name: 'Club de Nuit Intense',
    brand: 'Armaf',
    category: 'Árabe',
    notes: 'Limón, Piña, Grosellas Negras',
    priceFrom: 95,
    image: 'https://images.unsplash.com/photo-1594035910387-fea47794261f?w=400&auto=format&fit=crop&q=60',
  },
  {
    id: 7,
    name: 'Angels Share',
    brand: 'Kilian',
    category: 'Nicho',
    notes: 'Habichuela Tonka, Roble, Vainilla',
    priceFrom: 310,
    image: 'https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?w=400&auto=format&fit=crop&q=60',
  },
  {
    id: 8,
    name: 'Y EDP',
    brand: 'Yves Saint Laurent',
    category: 'Diseñador',
    notes: 'Manzana, Jengibre, Salvia, Habatonka',
    priceFrom: 165,
    image: 'https://images.unsplash.com/photo-1592945403244-b3fbafd7f539?w=400&auto=format&fit=crop&q=60',
  },
];

const selectedCategory = ref<'Todos' | 'Diseñador' | 'Nicho' | 'Árabe'>('Todos');
const currentPage = ref(1);
const itemsPerPage = ref(6);
const whatsappNumber = import.meta.env.PUBLIC_WHATSAPP_NUMBER;

function updateItemsPerPage() {
  const nextPageSize = window.innerWidth < 640 ? 2 : window.innerWidth < 1024 ? 4 : 8;
  if (itemsPerPage.value !== nextPageSize) {
    itemsPerPage.value = nextPageSize;
    currentPage.value = Math.min(currentPage.value, Math.max(1, totalPages.value));
  }
}

onMounted(() => {
  updateItemsPerPage();
  window.addEventListener('resize', updateItemsPerPage);
});

onUnmounted(() => window.removeEventListener('resize', updateItemsPerPage));

const filteredPerfumes = computed(() => {
  if (selectedCategory.value === 'Todos') return perfumes;
  return perfumes.filter((p) => p.category === selectedCategory.value);
});

const totalPages = computed(() => Math.ceil(filteredPerfumes.value.length / itemsPerPage.value));

const paginatedPerfumes = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  return filteredPerfumes.value.slice(start, start + itemsPerPage.value);
});

function changeCategory(cat: 'Todos' | 'Diseñador' | 'Nicho' | 'Árabe') {
  selectedCategory.value = cat;
  currentPage.value = 1;
}

function changePage(page: number) {
  if (page >= 1 && page <= totalPages.value) {
    currentPage.value = page;
  }
}

function sendWhatsAppRequest(perfume: Perfume) {
  const params = new URLSearchParams({
    text: `Hola, me interesa pedir el decant de ${perfume.name} de ${perfume.brand}.`,
  });
  window.open(`https://wa.me/${whatsappNumber}?${params.toString()}`, '_blank', 'noopener,noreferrer');
}
</script>

<template>
  <div>
    <!-- Contenedor Principal de Filtros (Ancho Completo y Centrado) -->
    <div class="mb-8 flex w-full flex-col items-center justify-center gap-4 text-center sm:flex-row sm:justify-between">
      <!-- Botones de Categorías: Forzados al centro con w-full, justify-center y mx-auto -->
      <div class="flex w-full flex-wrap items-center justify-center gap-2 mx-auto sm:w-auto">
        <button
          v-for="cat in ['Todos', 'Diseñador', 'Nicho', 'Árabe'] as const"
          :key="cat"
          @click="changeCategory(cat)"
          :class="[
            'cursor-pointer rounded border px-4 py-2 text-xs font-bold transition-colors duration-200',
            selectedCategory === cat
              ? 'border-orange-500 bg-orange-500 text-neutral-950 shadow-md shadow-orange-500/20'
              : 'border-neutral-800 bg-neutral-900/80 text-neutral-400 hover:border-neutral-700 hover:text-white',
          ]">
          {{ cat }}
        </button>
      </div>

      <!-- Botón Volver al inicio: Centrado -->
      <a
        href="/"
        class="inline-flex items-center justify-center cursor-pointer rounded border border-neutral-800 bg-neutral-900/80 px-4 py-2 text-xs font-bold text-neutral-400 transition-colors duration-200 hover:border-orange-500 hover:text-orange-400">
        Volver al inicio
      </a>
    </div>

    <!-- Resto del componente (Grid de Tarjetas) -->
    <div class="grid grid-cols-2 gap-3 sm:grid-cols-4 sm:gap-4">
      <div
        v-for="perfume in paginatedPerfumes"
        :key="perfume.id"
        class="group flex min-h-[21rem] min-w-0 flex-col overflow-hidden rounded-lg border border-neutral-800/80 bg-neutral-900/40 p-3 transition-colors duration-200 hover:border-orange-500/40 hover:bg-neutral-900/80">
        <div class="flex min-h-0 flex-1 flex-col">
          <div class="relative min-h-0 flex-1 overflow-hidden rounded bg-neutral-950 flex items-center justify-center">
            <img
              :src="perfume.image"
              :alt="perfume.name"
              loading="lazy"
              class="h-full w-full object-contain p-2 opacity-95 transition-transform duration-300 group-hover:scale-105" />

            <span
              class="absolute top-2 left-1/2 -translate-x-1/2 sm:left-2 sm:translate-x-0 rounded-md bg-neutral-950/80 px-2 py-0.5 text-[9px] font-extrabold uppercase tracking-widest text-orange-400 backdrop-blur-md">
              {{ perfume.category }}
            </span>
          </div>

          <div class="mt-2 min-h-0 text-center sm:text-left">
            <span class="block truncate text-[10px] font-semibold tracking-wider text-neutral-400 uppercase">
              {{ perfume.brand }}
            </span>

            <h3
              class="truncate text-xs font-extrabold text-white sm:text-sm"
              :title="perfume.name">
              {{ perfume.name }}
            </h3>

            <p class="mt-1 line-clamp-1 text-[10px] leading-tight text-neutral-400">
              {{ perfume.notes }}
            </p>
          </div>
        </div>

        <div
          class="mt-2 flex shrink-0 flex-col items-center justify-between gap-2 border-t border-neutral-800/60 pt-2 text-center sm:flex-row sm:text-left">
          <div class="w-full sm:w-auto">
            <span class="block text-[8px] uppercase tracking-wider text-neutral-500"> Decant desde </span>
            <span class="text-xs font-black text-white sm:text-sm"> ${{ perfume.priceFrom }} MXN </span>
          </div>

          <button
            @click="sendWhatsAppRequest(perfume)"
            class="w-full sm:w-auto cursor-pointer rounded bg-orange-500 px-3 py-1.5 text-[11px] font-bold text-neutral-950 transition-colors duration-200 hover:bg-orange-400 focus:outline-none focus:ring-2 focus:ring-orange-500 focus:ring-offset-2 focus:ring-offset-neutral-950 active:scale-95">
            Pedir
          </button>
        </div>
      </div>
    </div>

    <!-- Paginación -->
    <div
      v-if="totalPages > 1"
      class="mt-10 flex items-center justify-center gap-2 text-xs font-bold">
      <button
        @click="changePage(currentPage - 1)"
        :disabled="currentPage === 1"
        aria-label="Página anterior"
        class="flex h-9 w-9 cursor-pointer items-center justify-center rounded border border-neutral-800 text-base text-neutral-300 transition-colors hover:border-orange-500 hover:text-orange-400 disabled:cursor-not-allowed disabled:opacity-30">
        <span aria-hidden="true">←</span>
      </button>

      <button
        v-for="page in totalPages"
        :key="page"
        @click="changePage(page)"
        :class="[
          'flex h-9 w-9 cursor-pointer items-center justify-center rounded border text-xs font-bold transition-colors duration-200',
          currentPage === page
            ? 'border-orange-500 bg-orange-500 font-extrabold text-neutral-950 shadow-md shadow-orange-500/20'
            : 'border-neutral-800 bg-neutral-900/80 text-neutral-400 hover:border-neutral-700 hover:text-white',
        ]">
        {{ page }}
      </button>

      <button
        @click="changePage(currentPage + 1)"
        :disabled="currentPage === totalPages"
        aria-label="Página siguiente"
        class="flex h-9 w-9 cursor-pointer items-center justify-center rounded border border-neutral-800 text-base text-neutral-300 transition-colors hover:border-orange-500 hover:text-orange-400 disabled:cursor-not-allowed disabled:opacity-30">
        <span aria-hidden="true">→</span>
      </button>
    </div>
  </div>
</template>
