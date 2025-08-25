<script setup lang="ts">
import { ref, onMounted, getCurrentInstance } from 'vue'

const props = defineProps<{ modelValue?: string; value?: string }>()
const emit = defineEmits<{ (e: 'update:modelValue', v: string): void }>()
const getVal = () => (props.modelValue ?? props.value ?? '#ffffff')

const Comp = ref<any>(null)
onMounted(() => {
  const inst = getCurrentInstance()
  const comps = inst?.appContext?.components || {}
  Comp.value = comps['AColorPicker'] || comps['ColorPicker'] || null
})

const onColorUpdate = (v: string) => {

  const hex = v?.startsWith('#') ? v.toLowerCase() : ('#' + v).toLowerCase()
  emit('update:modelValue', hex)
}

const onNativeInput = (e: Event) => {
  let v = (e.target as HTMLInputElement).value || ''
  if (v && !v.startsWith('#')) v = '#' + v
  emit('update:modelValue', v.toLowerCase())
}
</script>

<template>
  <component
      v-if="Comp"
      :is="Comp"
      :value="getVal()"
      format="hex"
      :disabledAlpha="true"
      @update:value="onColorUpdate"
  />
  <input
      v-else
      type="color"
      :value="getVal()"
      @input="onNativeInput"
      class="color-input"
  />
</template>

<style scoped>
.color-input{
  width: 40px;
  height: 24px;
  padding: 0;
  border: 1px solid #e5e7eb;
  border-radius: 6px;
  background: transparent;
  cursor: pointer;
}
</style>
