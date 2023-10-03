<template>
  <div>
    <LoadingSchematicData v-if="loading || state === 'unset'" />
    <IconScreen
      v-else-if="state === 'not_found'"
      icon="/icons/help-circle.svg"
      text="没有找到Schematic"
    />
    <IconScreen
      v-else-if="state === 'gone'"
      icon="/icons/trash-2.svg'"
      text="此Schematic已经被删除了"
    />
    <IconScreen
      v-else-if="state === 'unknown'"
      icon="/icons/alert-triangle.svg"
      text="位置错误"
    />
    <IconScreen v-else icon="/icons/check-circle.svg" :text="finalText" />
    <nuxt-link to="/" class="btn btn-secondary d-block w-100 mt-5"
      >回到首页</nuxt-link
    >
  </div>
</template>

<script>
import LoadingSchematicData from '~/components/LoadingSchematicData'
import IconScreen from '~/components/IconScreen'

export default {
  name: 'SchematicWorkflow',
  components: { LoadingSchematicData, IconScreen },
  props: {
    loading: {
      type: Boolean,
      default: true,
    },
    state: {
      type: String,
      default: 'unset',
    },
    finalText: {
      type: String,
      required: true,
    },
  },
}
</script>
