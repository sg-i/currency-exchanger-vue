<template>
  <div class="min-h-screen flex items-center justify-center">
    <div class="z-10 mx-auto p-4 bg-opacity-0">
      <main class="container">
        <div class="flex flex-col gap-3">
          <Exchanger
            v-for="exchanger in exchangers"
            :key="exchanger.id"
            :rates="rates"
            :defaultCurrency="defaultCurrency"
            :removeExchanger="() => removeExchanger(exchanger.id)"
          />
          <button
            class="bg-white mx-auto font-bold py-2 px-2 mb-4 rounded-full hover:bg-slate-100"
            @click="addExchanger"
          >
            <svg
              fill="#000000"
              width="20px"
              height="20px"
              viewBox="0 0 1920 1920"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M866.332 213v653.332H213v186.666h653.332v653.332h186.666v-653.332h653.332V866.332h-653.332V213z"
                fill-rule="evenodd"
              />
            </svg>
          </button>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
  import { ref, onMounted } from 'vue';
  import Exchanger from './components/Exchanger.vue';
  import fx from 'money';

  export default {
    components: {
      Exchanger,
    },
    setup() {
      // Определение базовой валюты пользователя из локали браузера
      function getCurrencyByLocale() {
        const userLocale = navigator.language || navigator.userLanguage;

        // Создаем объект Intl.NumberFormat с использованием локали пользователя
        const currencyFormatter = new Intl.NumberFormat(userLocale, {
          style: 'currency',
          currency: 'RUB',
        });

        // Получаем информацию о валюте
        const currency = currencyFormatter.resolvedOptions().currency;

        return currency;
      }
      const defaultCurrency = ref(getCurrencyByLocale());

      // Стейт конвертеров валют
      const exchangers = ref([{ id: Date.now() }]);
      // Добавить конвертер
      const addExchanger = () => {
        exchangers.value.push({ id: Date.now() });
      };
      // Удалить конвертер
      const removeExchanger = (id) => {
        console.log('id');
        exchangers.value = exchangers.value.filter((exchanger) => exchanger.id !== id);
      };

      // Котировки
      const rates = ref({});

      onMounted(async () => {
        // Получаем курсы валют с ЦБ РФ
        const fetchRates = async () => {
          try {
            const response = await fetch('https://www.cbr-xml-daily.ru/daily_json.js');
            const data = await response.json();
            const ratesData = { RUB: 1 };

            // Проходим по ключам в объекте Valute и добавляем курсы валют в ratesData
            Object.keys(data.Valute).forEach((code) => {
              const value = data.Valute[code].Value;
              const nominal = data.Valute[code].Nominal;
              ratesData[code] = (1 / value) * nominal;
            });

            fx.base = 'RUB';
            fx.rates = ratesData;
            rates.value = ratesData;
          } catch (error) {
            console.error('Error fetching the rates:', error);
          }
        };

        fetchRates();
      });

      return {
        exchangers,
        addExchanger,
        removeExchanger,
        rates,
        defaultCurrency,
      };
    },
  };
</script>
