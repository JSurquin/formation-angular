<template>
  <div class="bg-section slidev-layout new-section">
    <CornerCurves class="absolute left-0 top-0 transform rotate-90" />
    <div
      class="flex flex-col justify-center text-center h-full underline dark:decoration-purple-400 underline-offset-16"
    >
      <slot></slot>
    </div>
    <SlideCurrentNo
      class="dark:!text-white absolute top-4 right-2 -translate-x-1/2 !text-[8px] p-1 w-6 h-6 items-center justify-center flex rounded-full bg-white/10 backdrop-blur-sm border border-gray-600 opacity-80"
    />
    <div
      class="absolute bottom-0 p-4 justify-end -ml-16 mt-auto flex items-center w-full z-50"
    >
      <Link class="dark:!text-purple-300" to="sommaire">
        Revenir au sommaire
      </Link>
    </div>
    <AsGraphic type="zigzag" class="absolute top-20 -right-30" />
  </div>
</template>

<style>
.slidev-layout.new-section {
  h1 {
    @apply text-primary text-6xl z-50 leading-20 mb-0 font-title font-extrabold;
  }

  h1 + p {
    @apply mt-4 opacity-100;
  }

  p > img {
    @apply w-2/3 mx-auto;
  }
}
</style>
