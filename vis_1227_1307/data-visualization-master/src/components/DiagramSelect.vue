<script>
export default {
  props:{
    value: { type: String, required: true },
    select: { type: Array, required: true }
  }
}
</script>

<template>
  <label :class="$style.selectLabel">
    <slot />
    <select
      v-bind="{ value }"
      @change="$emit('input', $event.target.value)"
      :class="$style.select"
    >
      <option
        v-for="(option, i) in select"
        :key="i"
        :value="option.value"
      >
        {{ option.label }}
      </option>
    </select>
  </label>
</template>

<style module>
.selectLabel {
  display: block;
  margin: 16px 0;

}
/* added a style here */
.select {
  position: relative;
  display: inline-block;
  padding: 6px 64px 6px 22px;
  font-size: 22px;
  font-weight: 300;
  border-radius: 4px;
  background: #5c9cd8;
  color: #fff;
  cursor: pointer;
}
</style>
