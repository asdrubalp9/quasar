<template>
  <div>
    <p>{{ title }}</p>
    <ul>
      <li v-for="todo in todos" :key="todo.id" @click="increment">
        {{ todo.id }} - {{ todo.content }}
      </li>
    </ul>
    <p>Count: {{ todoCount }} / {{ meta.totalCount }}</p>
    <p>Active: {{ active ? 'yes' : 'no' }}</p>
    <p>Clicks on todos: {{ clickCount }}</p>
  </div>
</template>

<script lang="ts">
<% if (typescriptConfig === 'composition') { %>
import {
  defineComponent, PropType, computed, ref, toRef, Ref,
} from '@vue/composition-api';
import { Todo, Meta } from './models';

function useClickCount() {
  const clickCount = ref(0);
  function increment() {
    clickCount.value += 1
    return clickCount.value;
  }

  return { clickCount, increment };
}

function useDisplayTodo(todos: Ref<Todo[]>) {
  const todoCount = computed(() => todos.value.length);
  return { todoCount };
}

export default defineComponent({
  name: 'CompositionComponent',
  props: {
    title: {
      type: String,
      required: true
    },
    todos: {
      type: (Array as unknown) as PropType<Todo[]>,
      default: () => []
    },
    meta: {
      type: (Object as unknown) as PropType<Meta>,
      required: true
    },
    active: {
      type: Boolean
    }
  },
  setup(props) {
    return { ...useClickCount(), ...useDisplayTodo(toRef(props, 'todos')) };
  },
});
<% } else if (typescriptConfig === 'class') { %>
import { Vue, Component, Prop } from 'vue-property-decorator';
import { Todo, Meta } from './models';

@Component
export default class ClassComponent extends Vue {
  @Prop({ type: String, required: true }) readonly title!: string;
  @Prop({ type: Array, default: () => [] }) readonly todos!: Todo[];
  @Prop({ type: Object, required: true }) readonly meta!: Meta;
  @Prop(Boolean) readonly active!: boolean;

  clickCount = 0;

  increment() {
    this.clickCount += 1;
  }

  get todoCount() {
    return this.todos.length;
  }
}
<% } else if (typescriptConfig === 'options') { %>
import Vue, { PropType } from 'vue';
import { Todo, Meta } from './models';

export default Vue.extend({
  name: 'OptionsComponent',
  props: {
    title: {
      type: String,
      required: true
    },
    todos: {
      type: (Array as unknown) as PropType<Todo[]>,
      default: () => [] as Todo[]
    },
    meta: {
      type: (Object as unknown) as PropType<Meta>,
      required: true
    },
    active: {
      type: Boolean
    }
  },
  data(): { clickCount: number } {
    return {
      clickCount: 0
    };
  },
  methods: {
    increment(): void {
      this.clickCount += 1;
    }
  },
  computed: {
    todoCount(): number {
      return this.todos.length;
    }
  }
});
<% } %>
</script>
