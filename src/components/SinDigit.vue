<script setup lang="ts">

const props = defineProps<{
  index: number,
  value: string,
  onChange: (index: number, value: string) => void;
}>();

function onKeyDown(e: KeyboardEvent) {
  const key = e.key;
  if (key === "Backspace") {
    const element = (e?.currentTarget as HTMLInputElement);
    const val = element.value;
    if (val.length === 0) {
      const prevInput = element.previousElementSibling as HTMLElement;
      if (prevInput) {
        props.onChange(props.index - 1, "");
        prevInput.focus();
      }
    } else {
      props.onChange(props.index, val.slice(0, val.length - 1));
    }
  }
}

function onInput(e: Event) {
  const element = (e?.currentTarget as HTMLInputElement);
  const val = element.value;
  if (val.match(/[0-9]/)) {
    props.onChange(props.index, val);
    const nextInput = element.nextElementSibling as HTMLElement;
    if (nextInput) {
      nextInput.focus();
    }
  }
}

function onSelect(e: Event) {
  const element = (e?.currentTarget as HTMLInputElement);
  const nextInput = element.nextElementSibling as HTMLElement;
  if (nextInput.nodeName == "INPUT") {
    props.onChange(props.index, "")
  } else {
    props.onChange(props.index, element.value);
  }
}
</script>

<template>
  <input type="text"
         inputmode="numeric"
         class="input"
         min="0"
         max="9"
         :value="value"
         @keydown="onKeyDown($event)"
         @input="onInput($event)"
         @select="onSelect($event)"
  />
</template>

<style scoped>
.input {
  width: 1em;
  height: 1.2em;
  padding: 0.4em;
  margin: 0.2em;
  text-align: center;
}

.input:nth-child(3n) {
  margin-right: 1em;
}
</style>
