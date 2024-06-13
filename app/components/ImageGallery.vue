<script setup lang="ts">
const { data: images, refresh } = await useFetch("/api/images");

async function uploadImage({ target }: Event) {
  // https://hub.nuxt.com/docs/storage/blob#useupload
  const upload = useUpload("/api/images/upload", {
    multiple: false,
  });
  await upload([...target.files])
    .then(async () => {
      await refresh();
    })
    .catch((err) => {
      alert("Failed to upload image:\n" + { err: err.message });
    });
}

async function deleteImage(pathname: string) {
  await $fetch(`/api/images/${pathname}`, { method: "DELETE" });
  await refresh();
}
</script>

<template>
  <div>
    <h3>Images</h3>
    <input
      accept="jpeg, png"
      type="file"
      name="file"
      multiple="false"
      @change="uploadImage"
    />
    <p>
      <img
        v-for="image of images"
        :key="image.pathname"
        width="200"
        :src="`/images/${image.pathname}`"
        :alt="image.pathname"
        @dblclick="deleteImage(image.pathname)"
      />
    </p>
    <p v-if="images?.length">
      <i>Tip: delete an image by double-clicking on it.</i>
    </p>
  </div>
</template>
