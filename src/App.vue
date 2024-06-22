<script setup>
import IqTranslator from "./components/IqTranslator.vue";
import FooterComp from "./components/FooterComp.vue";
</script>

<template>
  <div class="bg-[#30344c] md:h-[100vh] flex flex-col justify-around">
    <IqTranslator class="pt-10" />
    <FooterComp class="md:w-[400px] bg-[#30344c] h-[40px] md:mx-auto" />
  </div>
</template>

<style>
html {
  box-sizing: border-box;
}
</style>
