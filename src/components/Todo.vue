<template>
  <li class="item" :class="{ dark: darkMode }">
    <span>{{ todo.value }}</span>
    <div class="item-buttons">
      <button class="remove-button" @click="removeTodo">Remove</button>
      <button class="done-button" @click="completeTodo">Done</button>
    </div>
  </li>
</template>
<script lang="ts">
import { PropType, Ref, defineComponent, inject } from "vue";

interface TODO {
  id: string;
  value: string;
}

export default defineComponent({
  props: {
    todo: {
      required: true,
      type: Object as PropType<TODO>,
    },
    removeTodo: {
      required: true,
      type: Function as PropType<() => void>,
    },
    completeTodo: {
      required: true,
      type: Function as PropType<() => void>,
    },
  },
  setup() {
    const darkMode = inject<Ref<boolean>>("dark-mode");

    return {
      darkMode,
    };
  },
});
</script>
