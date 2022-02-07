<template>
  <button
    class="btn_primary"
    :class="{
      'h-12': height === 'sm',
      'h-20': height === 'md',
      'h-36': height === 'xl',
      'w-12': width === 'sm',
      'w-20': width === 'md',
      'w-36': width === 'xl'
    }"
  >
    <slot />
  </button>
</template>

<script>
export default {
  name: 'CalcButton',
  props: {
    height: {
      type: String,
      default: 'sm'
    },
    width: {
      type: String,
      default: 'sm'
    }
  }
}
</script>

<style scoped>
.btn_primary {
  @apply rounded-xl flex items-center justify-center text-2xl duration-100 text-blue-800;
  box-shadow: 0.25rem 0.25rem .5rem rgb(0 0 0 / 10%), -0.35rem -0.35rem 0.5rem rgb(255 255 255 / 45%);
}

.btn_primary:active,
.btn_primary.btn_active {
  @apply bg-black bg-opacity-5 text-blue-900;
  box-shadow: 0.25rem 0.25rem .3rem rgb(0 0 0 / 10%) inset, -0.15rem -0.15rem 0.5rem rgb(255 255 255 / 25%) inset;
}

.btn_primary > span {
  @apply transform duration-100;
}

.btn_primary:active > span,
.btn_primary.btn_active > span {
  @apply scale-90;
}
</style>
