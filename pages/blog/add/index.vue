<template>
  <div class="p-10">
    <form @submit.prevent="addItem">
      <div class="bg-white rounded-[20px]">
        <FormHeader name="Blog" page="add" />

        <div class="p-[40px] pb-0">
          <div class="flex gap-[24px]">
            <div class="w-[200px] flex-shrink-0 h-[600px]">
              <div class="mb-5">
                <Input
                  @update:value="data.title = $event"
                  name="title_blog"
                  label="Title"
                  placeholder="Enter title"
                  :value="data.title"
                />
              </div>

              <div class="h-[400px]">
                <label
                  for="image"
                  class="text-[18px] leading-[27px] font-semibold text-[#303972] mb-[16px] block"
                  >Image *</label
                >
                <el-upload
                  @change="addPhoto"
                  @remove="removePhoto"
                  class="upload-demo"
                  drag
                  name="image"
                  id="image"
                  list-type="picture"
                  :limit="1"
                  :auto-upload="false"
                  ref="upload"
                  accept=".jpeg, .jpg, .png"
                >
                  <el-icon class="el-icon--upload"><upload-filled /></el-icon>
                  <div class="el-upload__text">
                    Drop file here or <em>click to upload</em>
                  </div>
                  <template #tip>
                    <div class="el-upload__tip">
                      jpg/png files with a size less than 500kb
                    </div>
                  </template>
                </el-upload>
              </div>
            </div>

            <div class="w-full">
              <editor
                api-key="f0tc1o55esbjszexd9h1lv61etxfc6vpojaeucnvjip9z7np"
                v-model="data.body"
                :init="{
                  menubar: false,
                  plugins: 'lists link image emoticons',
                  toolbar:
                    'anchor autolink charmap codesample emoticons image link lists media searchreplace table visualblocks wordcount checklist mediaembed casechange export formatpainter pageembed linkchecker a11ychecker tinymcespellchecker permanentpen powerpaste advtable advcode editimage tinycomments tableofcontents footnotes mergetags autocorrect typography inlinecss',
                  toolbar:
                    'undo redo | blocks fontsize | emoticons bold italic underline strikethrough  | link image media table mergetags | addcomment showcomments | spellcheckdialog a11ycheck typography | align lineheight checklist numlist bullist  | charmap | removeformat',
                }"
              />
            </div>
          </div>

          <div v-html="data.body"></div>
        </div>

        <div class="flex items-center justify-end p-10 pt-0">
          <SubmitBtn text="Add" />
        </div>
      </div>
    </form>
  </div>
</template>

<script setup>
import axios from "axios";
import { UploadFilled } from "@element-plus/icons-vue";
import { ElNotification } from "element-plus";
import Editor from "@tinymce/tinymce-vue";

const upload = ref();

const data = reactive({
  title: "",
  body: "",
});

const formData = new FormData();

const addPhoto = (e) => {
  formData.append("image", e.raw);
};

const removePhoto = () => {
  formData.delete("image");
};

const addItem = (e) => {
  formData.append("title", data.title);
  formData.append("body", data.body);

  axios
    .post(`https://nest-portfolio-xy2i.onrender.com/api/blog`, formData)
    .then((res) => {
      ElNotification({
        title: "Added",
        type: "success",
      });

      data.title = "";
      data.body = "";
      upload.value.clearFiles();

      formData.delete("title");
      formData.delete("body");
      formData.delete("image");

      useRouter().push({ name: "blog" });
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
};
</script>

<style lang="scss" scoped></style>
