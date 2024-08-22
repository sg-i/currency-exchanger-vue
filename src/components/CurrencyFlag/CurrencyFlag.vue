<template>
  <span
    :style="{
      height: dimension.height + 'px',
      width: dimension.width + 'px',
      backgroundImage: `url(${currencyImage})`,
      backgroundSize: 'cover',
      display: 'inline-block',
    }"
  ></span>
</template>

<script>
  import { computed } from 'vue';
  import flags from './flags';
  import { CurrencyFlagSizes } from './types';

  const RATIO = 1.6;
  const DEFAULT_DIMENSION = {
    height: 16,
    width: 24,
  };

  export default {
    name: 'CurrencyFlag',
    props: {
      currency: {
        type: String,
        required: true,
      },
      size: {
        type: String,
        default: null,
        validator: (value) =>
          [
            CurrencyFlagSizes.SMALL,
            CurrencyFlagSizes.MEDIUM,
            CurrencyFlagSizes.LARGE,
            CurrencyFlagSizes.XLARGE,
          ].includes(value),
      },
      width: {
        type: Number,
        default: null,
      },
      height: {
        type: Number,
        default: null,
      },
    },
    setup(props) {
      const currencyImage = computed(() => {
        return flags[props.currency.toLowerCase()] || flags['$$$'];
      });

      const calculateDimension = (width, height) => {
        if (!width && !height) {
          return DEFAULT_DIMENSION;
        }
        let w = width;
        let h = height;
        if (!width && height) {
          w = height * RATIO;
        } else if (!height && width) {
          h = width / RATIO;
        }
        return {
          height: h,
          width: w,
        };
      };

      const currencyFlagDims = {
        [CurrencyFlagSizes.SMALL]: {
          height: 10,
          width: 16,
        },
        [CurrencyFlagSizes.MEDIUM]: DEFAULT_DIMENSION,
        [CurrencyFlagSizes.LARGE]: {
          height: 24,
          width: 36,
        },
        [CurrencyFlagSizes.XLARGE]: {
          height: 32,
          width: 48,
        },
      };

      const dimension = computed(() => {
        return props.size
          ? currencyFlagDims[props.size]
          : calculateDimension(props.width, props.height);
      });

      return {
        currencyImage,
        dimension,
      };
    },
  };
</script>
