<!-- global-top.vue -->

<template>
  <footer
    v-if="$slidev.themeConfigs.eventLogo || $slidev.themeConfigs.twitter"
    class="absolute bottom-0 p-4 mt-auto flex items-center w-full text-primary dark:text-white z-10"
  >
    <a
      v-if="$slidev.themeConfigs.twitter"
      :href="$slidev.themeConfigs.twitterUrl"
      target="_blank"
    >
      <logos-linkedin />{{ $slidev.themeConfigs.twitter }}
    </a>
  </footer>
</template>
