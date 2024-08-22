<template>
  <div
    class="relative flex flex-col md:flex-row gap-3 bg-white rounded-xl p-7 items-center shadow-lg justify-center text-lg"
  >
    <InputSection
      :changedCurrencyValue="changedLeftCurrencyValue"
      :changedCurrencyTitle="changedLeftCurrencyTitle"
      :currencies="Object.keys(rates)"
      :title="leftCurrencyTitle"
      :value="leftCurrencyValue"
    />
    <button class="rotate-90 md:rotate-0" @click="swapCurrency">
      <svg width="30px" height="30px" viewBox="0 0 16 16" fill="#000000">
        <path
          fill-rule="evenodd"
          clip-rule="evenodd"
          d="M4.207 15.061L1 11.854v-.707L4.207 7.94l.707.707-2.353 2.354H15v1H2.56l2.354 2.353-.707.707zm7.586-7L15 4.854v-.707L11.793.94l-.707.707L13.439 4H1v1h12.44l-2.354 2.354.707.707z"
        />
      </svg>
    </button>
    <InputSection
      :changedCurrencyValue="changedRightCurrencyValue"
      :changedCurrencyTitle="changedRightCurrencyTitle"
      :currencies="Object.keys(rates)"
      :title="rightCurrencyTitle"
      :value="rightCurrencyValue"
    />
    <button
      @click="removeExchanger"
      class="absolute top-0 right-0 rounded-se-xl rounded-bl-2xl w-9 h-7 p-[1px] shadow-lg cursor-pointer hover:bg-red-600 bg-red-500"
    >
      <svg width="100%" height="100%" viewBox="0 0 1024 1024">
        <path
          fill="white"
          d="M195.2 195.2a64 64 0 0 1 90.496 0L512 421.504 738.304 195.2a64 64 0 0 1 90.496 90.496L602.496 512 828.8 738.304a64 64 0 0 1-90.496 90.496L512 602.496 285.696 828.8a64 64 0 0 1-90.496-90.496L421.504 512 195.2 285.696a64 64 0 0 1 0-90.496z"
        />
      </svg>
    </button>
  </div>
</template>

<script>
  import { ref, watch } from 'vue';
  import InputSection from './InputSection.vue';
  import fx from 'money';

  export default {
    props: {
      defaultCurrency: {
        type: String,
        required: true,
      },
      rates: {
        type: Object,
        required: true,
      },
      removeExchanger: {
        type: Function,
        required: true,
      },
    },
    components: {
      InputSection,
    },
    setup(props) {
      // Левая валюта - название
      const leftCurrencyValue = ref(1);
      // Левая валюта - значение
      const leftCurrencyTitle = ref(props.defaultCurrency !== 'USD' ? 'USD' : 'EUR');
      // Правая валюта - название
      const rightCurrencyValue = ref('-');
      // Правая валюта - значение
      const rightCurrencyTitle = ref(props.defaultCurrency);

      // Если курсы валют загрузились и сохранены, пересчитываем значение правой валюты
      watch(
        () => props.rates,
        () => {
          if (!Object.keys(fx.rates).length) return;
          rightCurrencyValue.value = addSpaces(
            parseFloat(
              fx(leftCurrencyValue.value)
                .from(leftCurrencyTitle.value)
                .to(rightCurrencyTitle.value)
                .toFixed(2),
            ),
          );
        },
      );

      // Добавить пробел между разрядами
      function addSpaces(number) {
        return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
      }

      // Значение левой валюты изменилось => пересчет правой
      const changedLeftCurrencyValue = (value) => {
        leftCurrencyValue.value = addSpaces(value);
        if (value === '') {
          rightCurrencyValue.value = '';
        } else {
          rightCurrencyValue.value = addSpaces(
            parseFloat(
              fx(value).from(leftCurrencyTitle.value).to(rightCurrencyTitle.value).toFixed(2),
            ),
          );
        }
      };

      // Значение правой валюты изменилось => пересчет левой
      const changedRightCurrencyValue = (value) => {
        rightCurrencyValue.value = addSpaces(value);
        if (value === '') {
          leftCurrencyValue.value = value;
        } else {
          leftCurrencyValue.value = addSpaces(
            parseFloat(
              fx(value).from(rightCurrencyTitle.value).to(leftCurrencyTitle.value).toFixed(2),
            ),
          );
        }
      };

      // Обработка смены типа правой валюты
      const changedLeftCurrencyTitle = (curr) => {
        leftCurrencyTitle.value = curr;
        if (leftCurrencyValue.value === '') {
          rightCurrencyValue.value = '';
        } else {
          rightCurrencyValue.value = addSpaces(
            parseFloat(
              fx(leftCurrencyValue.value).from(curr).to(rightCurrencyTitle.value).toFixed(2),
            ),
          );
        }
      };

      // Обработка смены типа левой валюты
      const changedRightCurrencyTitle = (curr) => {
        rightCurrencyTitle.value = curr;
        if (rightCurrencyValue.value === '') {
          leftCurrencyValue.value = '';
        } else {
          rightCurrencyValue.value = addSpaces(
            parseFloat(
              fx(leftCurrencyValue.value).from(leftCurrencyTitle.value).to(curr).toFixed(2),
            ),
          );
        }
      };

      // Смена валюты местами
      const swapCurrency = () => {
        const tempLeftValue = leftCurrencyValue.value;
        const tempLeftTitle = leftCurrencyTitle.value;
        leftCurrencyValue.value = rightCurrencyValue.value;
        leftCurrencyTitle.value = rightCurrencyTitle.value;
        rightCurrencyValue.value = tempLeftValue;
        rightCurrencyTitle.value = tempLeftTitle;
      };

      return {
        leftCurrencyValue,
        leftCurrencyTitle,
        rightCurrencyValue,
        rightCurrencyTitle,
        changedLeftCurrencyValue,
        changedRightCurrencyValue,
        changedLeftCurrencyTitle,
        changedRightCurrencyTitle,
        swapCurrency,
      };
    },
  };
</script>
