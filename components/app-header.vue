<template>
    <header class="flex justify-between w-full">
      
 
  

<UHorizontalNavigation :links="links" class="  border-b border-gray-200 dark:border-gray-800" />
<ClientOnly>
 
   
  <UButton
      :icon="isDark ? 'i-heroicons-moon-20-solid' : 'i-heroicons-sun-20-solid'"
      color="gray"
      variant="ghost"
      aria-label="Theme"
      @click="isDark = !isDark"
    />
    <template #fallback>
      <div class="w-8 h-8" />
    </template>
  </ClientOnly>
    </header>

</template>

<script setup>

const colorMode = useColorMode()
const route = useRoute()
const links = [
  [{
    label: 'Principal',
    icon: 'i-heroicons-home',
    to: '/'
  }, {
    label: 'Dashboard',
    icon: 'i-heroicons-chart-bar',
    to: `${route.path.startsWith('/dev') ? '/dev' : ''}/components/horizontal-navigation`
  },]
]
const isDark = computed({
  get () {
    return colorMode.value === 'dark'
  },
  set () {
    colorMode.preference = colorMode.value === 'dark' ? 'light' : 'dark'
  }
})


</script>

<style  >
body{
  background-image: url('');

  opacity: 0.7; /* Defina o nível de transparência (0.0 a 1.0) */
}

</style>