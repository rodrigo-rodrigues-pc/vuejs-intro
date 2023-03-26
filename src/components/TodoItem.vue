<script>
  export default {
    props: {
      title: String,
      completed: Boolean,
    }
  }
</script>

<template>
  <li :class="{ checked: completed }" @click="$emit('toggleCompleted')">
    {{ title }}<span class="close" @click.stop="$emit('remove')">&times;</span>
  </li>
</template>

<style scoped>

</style>
