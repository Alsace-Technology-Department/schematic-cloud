<template>
  <div>
    <div v-if="result.error" class="text">
      <b class="d-block py-4">{{ result.error }}</b>
    </div>
    <div v-else>
      <div
        class="modal fade bg-opacity-50 bg-dark show"
        :class="{
          'd-block': modal,
        }"
        tabindex="-1"
      >
        <div class="modal-dialog">
          <div class="modal-content bg-dark">
            <div class="modal-header">
              <h1 class="modal-title fs-5">确定要删除吗？</h1>
              <button
                type="button"
                class="btn-close btn-close-white"
                data-bs-dismiss="modal"
                aria-label="Close"
                @click="toggleDeleteModal"
              ></button>
            </div>
            <div class="modal-body">
              您确定要删除原理图吗？ 您的文件将立即从我们的服务器中删除。 此操作无法撤销。
            </div>
            <div class="modal-footer">
              <button
                type="button"
                class="btn btn-info"
                data-bs-dismiss="modal"
                @click="toggleDeleteModal"
              >
                取消
              </button>
              <button
                type="button"
                class="btn btn-danger"
                @click="handleDeleteConfirm"
              >
                确定
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="text">
        <p>
          您只能看到一次这个页面，请务必记住一下内容（如果您需要的话）。 最重要的是，保留您的删除密钥，否则您将无法删除该schematic。
        </p>
      </div>
      <UploadCopyableText name="下载密钥" :value="result.download_key" />
      <UploadCopyableText name="删除密钥" :value="result.delete_key" />
      <UploadCopyableText
        name="上传 URL"
        :value="downloadUrl(result.download_key!)"
        :is-url="true"
        url-button-txt="Download"
        url-button-variant="success"
      />
      <div class="row">
        <div class="col-6">
          <button
            class="btn btn-danger d-block w-100"
            @click="toggleDeleteModal"
          >
            删除此schematic
          </button>
        </div>

        <div class="col-6">
          <button
            class="btn btn-success d-block w-100"
            @click="handleDownloadClick"
          >
            下载此schematic
          </button>
        </div>
      </div>
    </div>
    <button
      class="btn btn-secondary d-block w-100 mt-3"
      @click="emits('reset')"
    >
      上传其他文件
    </button>
  </div>
</template>

<script setup lang="ts">
import { PropType } from 'vue'
// import { Config } from '~/types'

const modal = ref(false)
const emits = defineEmits(['reset'])

const props = defineProps({
  result: {
    type: Object as PropType<{
      download_key: string | undefined
      delete_key: string | undefined
      error: string | undefined
    }>,
    required: true,
  },
})

const downloadUrl = async (key: string) => {
  // return `${(await $fetch<Config>('/config.json')).public_url}/download/${key}`
  return `/schem load url:${key}`
}

const handleDownloadClick = async () => {
  await useRouter().push(`/download/${props.result!.download_key}`)
}

const toggleDeleteModal = () => {
  modal.value = !modal.value
}

const handleDeleteConfirm = async () => {
  await useRouter().push(`/delete/${props.result!.delete_key}`)
}
</script>
