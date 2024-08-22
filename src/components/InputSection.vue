<template>
  <div class="flex flex-col items-center">
    <input
      v-model="inputValue"
      @input="handleChange"
      class="border-2 border-gray-300 text-center text-2xl rounded p-2"
      inputmode="decimal"
    />
    <DropDownMenu
      :changedCurrencyTitle="changedCurrencyTitle"
      :selectedCurrency="title"
      :currencies="currencies"
    />
  </div>
</template>

<script>
  import { ref, watch } from 'vue';
  import DropDownMenu from './DropDownMenu.vue';

  export default {
    props: {
      changedCurrencyValue: {
        type: Function,
        required: true,
      },
      changedCurrencyTitle: {
        type: Function,
        required: true,
      },
      title: {
        type: String,
        required: true,
      },
      value: {
        type: String,
        required: true,
      },
      currencies: {
        type: Array,
        required: true,
      },
    },
    components: {
      DropDownMenu,
    },
    setup(props) {
      const inputValue = ref(props.value);

      const formatNumber = (v) => {
        return v.replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
      };

      const handleChange = (e) => {
        const rawValue = e.target.value.replace(/\s/g, '');
        if (/^\d*\.?\d{0,2}$/.test(rawValue)) {
          const formattedValue = formatNumber(rawValue);
          inputValue.value = formattedValue;
          props.changedCurrencyValue(formattedValue);
        }
      };

      watch(
        () => props.value,
        (newValue) => {
          inputValue.value = newValue;
        },
      );

      return {
        inputValue,
        handleChange,
      };
    },
  };
</script>
