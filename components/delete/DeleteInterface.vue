<template>
  <SchematicWorkflow
    :loading="loading"
    :state="state"
    final-text="schematic文件已被删除"
  />
</template>

<script setup lang="ts">
import { useWorkflow } from '~/mixin/workflow'

const { checkHeaders, deleteUrl, hasError, state, loading } = useWorkflow()

const props = defineProps({
  accessKey: {
    type: String,
    required: true,
  },
})

onMounted(async () => {
  await checkHeaders(await deleteUrl(props.accessKey!), false)
  if (hasError()) {
    loading.value = false
    return
  }
  await deleteSchematic()
})

const deleteSchematic = async () => {
  try {
    await $fetch(await deleteUrl(props.accessKey!), {
      method: 'DELETE',
    })
  } catch (err) {
    state.value = 'unknown'
    // eslint-disable-next-line no-console
    console.error('Failed to delete schematic', err)
  }

  loading.value = false
}
</script>
