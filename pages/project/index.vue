<template>
  <div class="min-h-screen">
    <div class="p-5">
      <AddBtn name="project" />
    </div>

    <div class="grid grid-cols-4 gap-5 p-5">
      <div v-for="(el, ind) in data.list" :key="ind">
        <ProjectCard :el="el" />
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";

const data = reactive({
  list: [],
});

onMounted(() => {
  axios
    .get(`https://nest-portfolio-xy2i.onrender.com/api/project`)
    .then((res) => {
      data.list = res.data;
    })
    .catch((error) => {
      const message = error?.response?.data?.message;
      if (typeof message == "object") {
        for (let i in message) {
          setTimeout(() => {
            ElNotification({
              title: "Error",
              message: message[i],
              type: "warning",
            });
          }, i * 200);
        }
      } else {
        ElNotification({
          title: "Error",
          message: message,
          type: "warning",
        });
      }
    });
});
</script>

<style lang="scss" scoped></style>
