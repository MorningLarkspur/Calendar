<script setup>
const props = defineProps({
  show: Boolean
})
</script>

<template>
  <Transition name="modal">
    <div
      v-if="props.show"
      class="fixed flex bg-richblack bg-opacity-20 backdrop-blur-sm z-20 inset-0 w-full h-full max-h-dvh items-center justify-center p-4 transition-opacity"
    >
      <div class="w-[400px] m-auto bg-white rounded-md drop-shadow-md transition-opacity scale-110">
        <div class="p-5 bg-teal-700 rounded-t text-xl text-white font-bold">
          <slot name="header"></slot>
        </div>

        <div class="p-5 text-black">
          <slot name="body"></slot>
        </div>

        <div class="py-5">
          <slot name="footer"></slot>
        </div>
      </div>
    </div>
  </Transition>
</template>
