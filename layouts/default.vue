<template>
  <div>
    <Nuxt />
  </div>
</template>

<style lang="postcss">
html {
  font-family:
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

.btn {
  @apply font-bold py-2 px-4 rounded;
}
.btn-blue {
  @apply bg-blue-500 text-white;
}
.btn-indigo {
  @apply bg-indigo-600 text-white;
}
.btn-blue:hover {
  @apply bg-blue-600;
}
.input {
  @apply py-2 px-4 rounded border;
}
.desktop {
  justify-content: space-between;
  @apply flex flex-wrap;
}
@media (max-width: 640px) {
  .desktop {
    /* display: block !important; */
  }
}

</style>
