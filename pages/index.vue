<script setup lang="ts">
const nuxtApp = useNuxtApp()
const { data } = await useFetch<any>('https://blog-api.vio.vin/api/v1/tags/all', {
  method: 'GET',
  timeout: 3000,
  headers: {
    Accept: 'application/json',
  },
  transform(input) {
    return {
      ...input,
      fetchedAt: new Date(),
    }
  },
  getCachedData(key) {
    const data = nuxtApp.payload.data[key] || nuxtApp.static.data[key]
    if (!data)
      return null

    const expirationDate = new Date(data.fetchedAt)
    // 缓存10s
    expirationDate.setTime(expirationDate.getTime() + 10 * 1000)
    const isExpired = expirationDate.getTime() < Date.now()
    if (isExpired)
      return null
    return data
  },
})

async function fetchData() {
  const { data } = await $fetch('https://blog-api.vio.vin/api/v1/tags/all')
  console.log(data)
}
</script>

<template>
  <div>
    {{ data }}
    <p>
      <a @click="fetchData">fetch data</a>
      <nuxt-link class="text-2xl text-violet" to="/about">
        to about
      </nuxt-link>
    </p>
  </div>
</template>
