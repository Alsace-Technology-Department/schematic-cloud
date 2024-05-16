<template>
  <form @submit.prevent="submit">
    <input
      v-model="key"
      class="form-control mt-2"
      type="text"
      :placeholder="placeholder"
    />
    <button
      type="submit"
      class="btn btn-secondary d-block w-100 mt-2"
      :disabled="disabled"
    >
      {{ button }}
    </button>
  </form>
  <p class="links mt-4">
    点击这里
    <nuxt-link class="text-decoration-none" to="/">返回首页 </nuxt-link>
  </p>
</template>

<script setup lang="ts">
const key = ref('')

const props = defineProps({
  placeholder: {
    type: String,
    required: true,
  },
  button: {
    type: String,
    required: true,
  },
  path: {
    type: String,
    required: true,
  },
})

const disabled = computed(() => key.value === '')

const submit = () => {
  if (key.value !== '') {
    useRouter().push(`${props.path}/${key.value}`)
  }
}
</script>
