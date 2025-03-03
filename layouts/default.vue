<template>
  <div class="slidev-layout" :class="{ grid: $attrs.center }">
    <CornerCurves class="absolute bottom-0 right-0 transform scale-x--100" />
    <div class="my-auto relative">
      <slot></slot>
    </div>
    <div class="absolute top-4 right-2 flex items-center gap-2">
      <SlideCurrentNo
        class="dark:!text-white !text-[8px] p-1 w-5 h-5 items-center justify-center flex rounded-full bg-white/10 backdrop-blur-sm border border-gray-600 opacity-80"
      />
      <span class="text-[6px] text-gray-400">/</span>
      <SlideTotal
        class="dark:!text-white !text-[8px] p-1 w-5 h-5 items-center justify-center flex rounded-full bg-white/10 backdrop-blur-sm border border-gray-600 opacity-80"
      />
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
