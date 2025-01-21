<template>
  <div v-if="uploading">
    <UploadProgressBar :progress="progress" />
  </div>
  <div v-else class="file-selector">
    <p class="welcome mb-4">
      欢迎来到 schematic.cloud！ 请选择您想要上传的 Litematic 文件：
    </p>
    <div class="mb-3">
      <input
        tabindex="-1"
        class="form-control form-control-file"
        type="file"
        accept=".litematic"
        @change="onChange"
      />
    </div>
    <p class="links mt-4">
      点击这里
      <nuxt-link class="text-decoration-none" to="/">返回首页</nuxt-link>
    </p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const emits = defineEmits(['success', 'failed'])

const uploading = ref<boolean>(false)
const progress = ref<number>(0.0)

const onChange = async (e: Event) => {
  const files = (e.target as HTMLInputElement).files

  if (!files || files.length === 0) {
    return
  }

  const file = files[0]

  if (file.name.split('.').pop()?.toLowerCase() !== 'litematic') {
    emits('failed', '请上传有效的 Litematic 文件。')
    return
  }

  uploading.value = true

  const formData = new FormData()
  formData.append('file', file)

  try {
    const resp = await axios.post(
      `${(await $fetch('/config.json')).lite_api_url}/api/convert`,
      formData,
      {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
        onUploadProgress: (event) => {
          progress.value = Math.round((event.loaded * 100) / event.total)
        },
      }
    )

    emits('success', {
      download_key: resp.data.download_key,
      delete_key: resp.data.delete_key,
    })
  } catch (error) {
    let errorMessage = '上传失败，请重试。'
    if (error.response && error.response.data && error.response.data.error) {
      errorMessage = error.response.data.error // 从服务器响应中提取错误消息
    }
    emits('failed', errorMessage)
  }

  uploading.value = false
}
</script>

<style scoped>
.file-selector {
  text-align: center;
  max-width: 600px;
  margin: auto;
}
</style>
