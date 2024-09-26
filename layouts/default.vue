<template>
  <div class="slidev-layout" :class="{ grid: $attrs.center }">
    <CornerCurves class="absolute bottom-0 right-0 transform scale-x--100" />
    <div class="my-auto z-10 relative">
      <slot></slot>
    </div>
    <div
      class="absolute bottom-0 p-4 w-full justify-end -ml-16 mt-auto flex items-center w-full z-50"
    >
      <Link class="dark:!text-purple-300" to="sommaire">
        Revenir au sommaire
      </Link>
    </div>
  </div>
</template>
