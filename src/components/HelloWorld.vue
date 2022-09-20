<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { KeepLiveWS } from 'bilibili-live-ws/browser'
import type { DanmuMsg, RoomMsgHandler } from '../app'
import parser from '../parser'

defineProps<{ msg: string }>()

const count = ref(0)
const danmuList = ref<DanmuMsg[]>([])
const attention = ref(0)

const openRoom = (roomId: number, handler: RoomMsgHandler) => {
  const live = new KeepLiveWS(roomId)

  live.on('open', () => console.log('Connection is established'))

  live.on('DANMU_MSG', (data: any) => {
    console.log('DANMU_MSG')
    // handler.onIncomeDanmuRaw?.(data)
    // handler.onIncomeDanmu?.(parser.DANMU_MSG(data))
  })
}

onMounted(async () => {
  try {
    const handler: RoomMsgHandler = {
      onHeartbeat: (newAttention) => {
        attention.value = newAttention
      },
      onIncomeDanmu: (msg) => {
        danmuList.value.push(msg)
      },
    }
    openRoom(92613, handler)
  } catch (error) {
    console.error(error)
  }
})

</script>

<template>
  <h1>{{ msg }}</h1>


  <div class="card">
    <button type="button" @click="count++">count is {{ count }}</button>
    <p> {{ danmuList }}</p>
    <p>
      Edit
      <code>components/HelloWorld.vue</code> to test HMR
    </p>
  </div>

  <p>
    Check out
    <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank">create-vue</a>, the official Vue + Vite
    starter
  </p>
  <p>
    Install
    <a href="https://github.com/johnsoncodehk/volar" target="_blank">Volar</a>
    in your IDE for a better DX
  </p>
  <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
