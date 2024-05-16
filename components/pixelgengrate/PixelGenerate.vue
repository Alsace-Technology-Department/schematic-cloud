<template>
  <div v-if="uploading">
    <UploadProgressBar :progress="progress" />
  </div>
  <div v-else class="file-selector">
    <p class="welcome mb-4">
      欢迎来到schematic.cloud！ 请选择您想要上传的图片文件:
    </p>
    <div class="mb-3">
      <input
        tabindex="-1"
        class="form-control form-control-file"
        type="file"
        @change="onChange"
      />
    </div>
    <div v-if="previewUrl" class="mb-3">
      <p class="mt-2">图片预览:</p>
      <img :src="previewUrl" class="img-thumbnail" alt="图片预览" />
    </div>
    <p class="links mt-4">
      点击这里
      <nuxt-link class="text-decoration-none" to="/">返回首页 </nuxt-link>
    </p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const emits = defineEmits(['success', 'failed'])

const uploading = ref<boolean>(false)
const progress = ref<number>(0.0)
const previewUrl = ref<string | null>(null)

const onChange = async (e: Event) => {
  const files = (e.target as HTMLInputElement).files

  if (!files || files.length === 0) {
    return
  }

  const file = files[0]

  if (
    file.name.split('.').pop()?.toLowerCase() !== 'png' &&
    file.name.split('.').pop()?.toLowerCase() !== 'jpg' &&
    file.name.split('.').pop()?.toLowerCase() !== 'jpeg'
  ) {
    emits(
      'failed',
      '您上传的文件不是有效的图片文件，因此已被拒绝。 请上传有效的图片文件。',
    )
    return
  }

  // 设置图片预览
  const reader = new FileReader()
  reader.onload = (event) => {
    previewUrl.value = event.target?.result as string
  }
  reader.readAsDataURL(file)

  uploading.value = true

  const formData = new FormData()
  formData.append('image', file)

  try {
    const resp = await axios.post(
      `${(await $fetch('/config.json')).api_url}/uploadimg`,
      formData,
      {
        headers: {
          'Content-Type': 'multipart/form-data'
        },
        onUploadProgress: (event) => {
          progress.value = Math.round((event.loaded * 100) / event.total)
        },
      },
    )

    emits('success', {
      download_key: resp.data.download_key,
      delete_key: resp.data.delete_key,
    })
  } catch ({ response }) {
    let status
    if (response) {
      status = response.status
    }

    let error
    switch (status) {
      case 400:
        error =
          '您上传的文件不是有效的图片文件，因此已被拒绝。 请上传有效的图片文件。'
        break
      case 500:
        error =
          '该文件无法在服务器中保存，因此您的上传被拒绝。 请重试，或将此问题报告给开发人员。'
        break
      case 413:
        error = '文件过大，请尝试分块并重新上传。'
        break
      case undefined:
      default:
        error = `尝试上传文件时发生未知错误。 请重试，或将此问题报告给开发人员。详细数据${response.message}`
        break
    }

    emits('failed', error)
  }

  uploading.value = false
}
</script>

<style scoped>
.img-thumbnail {
  max-width: 100%;
  height: auto;
}
</style>
