<template>
  <div ref="dropdownRef" class="w-full">
    <div class="relative mt-2">
      <button
        type="button"
        class="relative w-full rounded-md bg-white py-1.5 pl-3 pr-10 text-left text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-500 sm:text-sm sm:leading-6"
        aria-haspopup="listbox"
        :aria-expanded="isOpen"
        aria-labelledby="listbox-label"
        @click="toggleDropdown"
      >
        <span class="flex items-center">
          <CurrencyFlag class="mb-[-1px]" :currency="selectedCurrency" :width="30" :height="20" />
          <span class="ml-3 text-xl block truncate">{{ selectedCurrency }}</span>
        </span>
        <span class="pointer-events-none absolute inset-y-0 right-0 ml-3 flex items-center pr-2">
          <svg
            class="h-5 w-5 text-gray-400"
            viewBox="0 0 20 20"
            fill="currentColor"
            aria-hidden="true"
          >
            <path
              fill-rule="evenodd"
              d="M10 3a.75.75 0 01.55.24l3.25 3.5a.75.75 0 11-1.1 1.02L10 4.852 7.3 7.76a.75.75 0 01-1.1-1.02l3.25-3.5A.75.75 0 0110 3zm-3.76 9.2a.75.75 0 011.06.04l2.7 2.908 2.7-2.908a.75.75 0 111.1 1.02l-3.25 3.5a.75.75 0 01-1.1 0l-3.25-3.5a.75.75 0 01.04-1.06z"
              clip-rule="evenodd"
            />
          </svg>
        </span>
      </button>

      <ul
        v-if="isOpen"
        class="cursor-pointer absolute z-10 mt-1 max-h-56 w-full overflow-auto rounded-md bg-white py-0 text-base shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none sm:text-sm"
        role="listbox"
        aria-labelledby="listbox-label"
      >
        <li
          v-for="curr in currencies"
          :key="curr"
          :class="{
            'relative select-none py-2 pl-3 pr-9 text-gray-900': true,
            'bg-indigo-600 text-white font-semibold': selectedCurrency === curr,
            'font-normal hover:bg-indigo-50': selectedCurrency !== curr,
          }"
          @click="selectCurrency(curr)"
        >
          <div class="flex items-center">
            <CurrencyFlag :width="30" :height="20" :currency="curr" />
            <span class="ml-3 text-xl block truncate">{{ curr }}</span>
          </div>
          <span
            v-if="selectedCurrency === curr"
            class="absolute inset-y-0 right-0 flex items-center pr-4 text-indigo-600"
          >
            <svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
              <path
                fill-rule="evenodd"
                d="M16.704 4.153a.75.75 0 01.143 1.052l-8 10.5a.75.75 0 01-1.127.075l-4.5-4.5a.75.75 0 011.06-1.06l3.894 3.893 7.48-9.817a.75.75 0 011.05-.143z"
                clip-rule="evenodd"
              />
            </svg>
          </span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  import { ref, onMounted, onBeforeUnmount } from 'vue';
  import CurrencyFlag from './CurrencyFlag/CurrencyFlag.vue';

  export default {
    props: {
      selectedCurrency: {
        type: String,
        required: true,
      },
      changedCurrencyTitle: {
        type: Function,
        required: true,
      },
      currencies: {
        type: Array,
        required: true,
      },
    },
    components: {
      CurrencyFlag,
    },
    setup(props) {
      const isOpen = ref(false);
      const dropdownRef = ref(null);

      const toggleDropdown = () => {
        isOpen.value = !isOpen.value;
      };

      const selectCurrency = (curr) => {
        props.changedCurrencyTitle(curr);
        isOpen.value = false;
      };

      const handleClickOutside = (event) => {
        if (dropdownRef.value && !dropdownRef.value.contains(event.target)) {
          isOpen.value = false;
        }
      };

      onMounted(() => {
        document.addEventListener('mousedown', handleClickOutside);
      });

      onBeforeUnmount(() => {
        document.removeEventListener('mousedown', handleClickOutside);
      });

      return {
        isOpen,
        dropdownRef,
        toggleDropdown,
        selectCurrency,
      };
    },
  };
</script>
