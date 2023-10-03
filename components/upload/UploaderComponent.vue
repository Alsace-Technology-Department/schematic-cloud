<template>
  <div v-if="uploading">
    <UploadProgressBar :progress="progress" />
  </div>
  <div v-else class="file-selector">
    <p class="welcome mb-4">
      欢迎来到schematic.cloud！ 请选择您想要上传的schematic文件:
    </p>
    <div class="mb-3">
      <input
        tabindex="-1"
        class="form-control form-control-file"
        type="file"
        @change="onChange"
      />
    </div>
    <p class="links mt-4">
      点击这里
      <nuxt-link class="text-decoration-none" to="/download"
        >下载</nuxt-link
      >
      你的schematic, 或者点击
      <nuxt-link class="text-decoration-none" to="/delete">删除</nuxt-link
      >
      你的文件。
    </p>
  </div>
</template>

<script setup lang="ts">
import axios from 'axios'

const emits = defineEmits(['success', 'failed'])

const uploading = ref<boolean>(false)
const progress = ref<number>(0.0)

const onChange = async (e: InputEvent) => {
  const files: FileList = (e.target as HTMLFormElement).files

  if (!files || files.length === 0) {
    return
  }
  const file = files[0]

  uploading.value = true

  const formData = new FormData()
  formData.append('schematic', file)

  try {
    const resp = await axios.post(
      `${(await $fetch('/config.json')).api_url}/upload`,
      formData,
      {
        'Content-Type': 'multipart/form-data',
        onUploadProgress: (event) => {
          progress.value = Math.round((event.loaded * 100) / event.total)
        },
      }
    )

    emits('success', {
      download_key: resp.data.download_key,
      delete_key: resp.data.delete_key,
    })
  } catch (err) {
    let status
    if (err.response) {
      status = err.response.status
    }

    let error
    switch (status) {
      case 400:
        error =
          '您上传的文件不是有效的 NBT，因此已被拒绝。 请上传有效的 NBT 文件。'
        break
      case 500:
        error =
          '该文件无法在服务器级别保存，因此您的上传被拒绝。 请重试，或将此问题报告给开发人员。'
        break
      case undefined:
      default:
        error =
          '尝试上传文件时发生未知错误。 请重试，或将此问题报告给开发人员。'
        break
    }

    emits('failed', error)
  }

  uploading.value = false
}
</script>
