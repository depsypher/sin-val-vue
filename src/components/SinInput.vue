<script setup lang="ts">
import {ref, onMounted, onUnmounted, watch} from 'vue';
import SinDigit from "./SinDigit.vue";
import {luhnCheck} from "./LuhnCheck.ts";

const props = defineProps<{ id: string }>();
const storageKey = `SinInput-${props.id}`

const digits = ref( Array(9).fill("") as Array<string>);
const valid = ref(false);

const storageEventCallback = (e: StorageEvent) => {
  if (e.key === storageKey) {
    digits.value = JSON.parse(e.newValue!) as string[];
  }
}

onMounted(() => {
  window.addEventListener('storage', storageEventCallback);

  const existing = localStorage.getItem(storageKey);
  if (existing) {
    const data = JSON.parse(existing) as string[];
    if (data) {
      digits.value = data;
    }
  }
});
onUnmounted(() => {
  window.removeEventListener('storage', storageEventCallback)
});

watch(digits, (newValue) => {
  localStorage.setItem(storageKey, JSON.stringify(newValue));
  const good = newValue.filter(v => v.length === 1 && !Number.isNaN(parseInt(v)));
  valid.value = good.length === 9 && luhnCheck(digits.value);
});

function onChange(index: number, value: string) {
  const newValue = [...digits.value]
  newValue[index] = value.slice(0, 1);
  digits.value = newValue;
}
</script>

<template>
  <div class="sin-input">
    <SinDigit
        v-for="(value, index) in digits"
        :index="index"
        :value="value"
        :on-change="onChange"
    />
    <div class="hidden md:block verification wide">
      {{valid ? "Valid SIN ✅" : "Invalid SIN ❌"}}
    </div>
    <div class="md:hidden verification">
      {{valid ? "✅" : "❌"}}
    </div>
  </div>
</template>

<style scoped>
.sin-input {
  display: flex;
  justify-content: center;
}
.verification {
  text-wrap: nowrap;
  align-self: center;
}
.wide {
  width: 7em;
}
</style>
