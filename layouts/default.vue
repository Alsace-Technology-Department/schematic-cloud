<template>
  <Html>

  <Head>
    <Title>schematic.cloud</Title>
    <Meta charset="UTF-8">
    </Meta>
    <Meta name="viewport" content="width=device-width, initial-scale=1">
    </Meta>
    <Meta name="description" hid="description"
      content="Easily store your NBT Minecraft schematics in the cloud for later use">
    </Meta>
    <Link rel="icon" type="image/x-icon" href="https://ns.alsace.team/service/images/logo/favicon.ico">
    </Link>
  </Head>

  <Body>
  <header class="app-header">
      <nav>
        <ul>
          <li><a href="/">首页</a></li>
          <li><a href="/uploadimg">图片转 schem</a></li>
          <li><a href="/uploadlitematic">Litematic 转 schem</a></li>
        </ul>
      </nav>
    </header>
    <Nuxt />
    <div class="container">
      <div class="row pt-5">
        <div class="col-8 mx-auto">
          <div class="card bg-dark bg-opacity-90 text-light">
            <div class="card-body">
              <nuxt-link to="/">
                <img class="d-block my-0 mx-auto" src="~/assets/img/logo.svg" alt="schematic.cloud" />
              </nuxt-link>
              <nuxt-page></nuxt-page>
            </div>
            <div class="card-footer">
              <p class="m-0 text-center">
                &copy; 2020 - 2023 长沙阿尔萨斯文化传播有限公司 All Rights Reserved.
                <br>Powered by Usine d' Alsace · 阿尔萨斯工业™
                <br><a href="https://beian.miit.gov.cn/" target="_blank">湘ICP备2023023798号</a>
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </Body>

  </Html>
</template>

<script setup lang="ts">
import { Config } from '~/types'

const version = ref<string>('loading')

onMounted(async () => {
  version.value = (
    await $fetch((await $fetch<Config>('/config.json')).api_url)
  ).version
})
</script>


<style>
body {
  background-image: url('https://ns.alsace.team/service/images/img/arks_max.JPG');
  background-position: center;
  background-size: cover;
  min-height: 100vh;
}
.app-header {
  background: rgba(0, 0, 0, 0.5);
  color: #fff;
  padding-top: 15px;
  padding-bottom: 5px;
  text-align: center;
}

.app-header ul {
  list-style-type: none;
  padding: 0;
}

.app-header ul li {
  display: inline;
  margin: 0 2rem;
}

.app-header ul li a {
  color: #fff;
  text-decoration: none;
  font-size: 18px;
}

.app-header ul li a:hover {
  text-decoration: underline;
}
</style>
