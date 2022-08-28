<template>
  <div class="w-8/12 flex items-center flex-col">
    <p class="text-center text-red-700">
      {{ message }}
    </p>
  </div>
</template>

<script>
export default {
  props: ["message"],
};
</script>
