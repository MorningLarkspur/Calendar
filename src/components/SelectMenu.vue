<script setup>
import AppButton from '@/components/AppButton.vue'

const props = defineProps({
  type: String,
  show: Boolean,
  content: Array,
  currentSelected: [String, Number]
})
</script>

<template>
  <div
    v-if="props.show"
    class="fixed flex bg-richblack bg-opacity-20 backdrop-blur-sm z-20 inset-0 w-full h-full max-h-dvh items-center justify-center p-4"
  >
    <div>
      <AppButton
        class="relative left-full bg-neutral-800 px-4 py-2 border-[6px] border-neutral-700 rounded-full text-2xl font-bold"
        @click="$emit('close')"
        >X</AppButton
      >
      <div
        v-if="type == 'month'"
        class="grid grid-cols-3 bg-neutral-700 gap-4 p-4 text-2lx rounded-md"
      >
        <template v-for="option in content" :key="option.id">
          <AppButton
            v-if="option === props.currentSelected"
            @click="$emit('close')"
            class="bg-neutral-200 text-black p-4 text-2xl font-bold rounded-md"
          >
            {{ option }}
          </AppButton>
          <AppButton
            v-else
            @click="$emit('select', option)"
            class="bg-neutral-800 p-4 text-2xl font-bold rounded-md"
          >
            {{ option }}
          </AppButton>
        </template>
      </div>
      <div
        v-else-if="type == 'year'"
        class="grid grid-cols-4 bg-neutral-700 gap-4 p-4 text-2lx rounded-md"
      >
        <template v-for="option in content" :key="option.id">
          <AppButton
            v-if="option === props.currentSelected"
            @click="$emit('close')"
            class="bg-neutral-200 text-black p-4 text-2xl font-bold rounded-md"
          >
            {{ option }}
          </AppButton>
          <AppButton
            v-else
            @click="$emit('select', option)"
            class="bg-neutral-800 p-4 text-2xl font-bold rounded-md"
          >
            {{ option }}
          </AppButton>
        </template>
      </div>
    </div>
  </div>
</template>
