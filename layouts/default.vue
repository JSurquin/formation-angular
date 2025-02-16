<template>
  <div class="slidev-layout" :class="{ grid: $attrs.center }">
    <CornerCurves class="absolute bottom-0 right-0 transform scale-x--100" />
    <div class="my-auto relative">
      <slot></slot>
    </div>
    <SlideCurrentNo
      class="dark:!text-white absolute bottom-4 left-6 -translate-x-1/2 !text-[10px] p-1 w-6 h-6 items-center justify-center flex rounded-full bg-white/10 backdrop-blur-sm border border-gray-600 opacity-80"
    />
    <div
      class="absolute bottom-0 p-4 w-full justify-end -ml-16 mt-auto flex items-center w-full z-50"
    >
      <Link class="dark:!text-purple-300" to="sommaire">
        Revenir au sommaire
      </Link>
    </div>
  </div>
</template>
