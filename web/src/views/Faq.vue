<!-- html code which renders the page -->
<template>
<div class="row" style="min-height: 75vh;">
  <div class="col-sm-12 my-5">

    <div class="container text-left mb-5">
      <h1>Frequently Asked Questions</h1>
      <!-- automatically pull Q&A from /assets/faq.json -->
      <section id="faq" class="my-3">
        <div v-for="(group, gIdx) in faqGroups" :key="gIdx">
          <h3 data-toggle="collapse" :href="'#group' + gIdx" class="pointer mt-5 py-2 border-left-main">{{group}}</h3>

          <!-- one group -->
          <template v-if="Array.isArray(faq[group])">
            <ol :id="'group' + gIdx" class="collapse">
              <li v-for="(question, qIdx) in faq[group]" :key="qIdx" class="mb-4 pb-4 font-size-xlarge" :class="{'border-bottom': qIdx < faq[group].length - 1}" :id="'g' + gIdx + '-q' + qIdx">
                <b>{{question.q}}</b>
                <div class="font-size-normal" v-html="question.a"></div>
              </li>
            </ol>
          </template>

          <!-- subgroups -->
          <div :id="'group' + gIdx" class="collapse ml-4" v-else>
            <div v-for="(subgroup, sIdx) in Object.keys(faq[group])" :key="sIdx" class="mb-5">
              <h4 class="text-sec">{{subgroup}}</h4>
              <ol :id="'group' + gIdx + '-subgroup' + sIdx">
                <li v-for="(question, qIdx) in faq[group][subgroup]" :key="qIdx" class="mb-4 pb-4 font-size-xlarge" :class="{'border-bottom': qIdx < faq[group][subgroup].length - 1}" :id="'g' + gIdx + 's' + sIdx + '-q' + qIdx">
                  <b>{{question.q}}</b>
                  <div class="font-size-normal" v-html="question.a"></div>
                </li>
              </ol>
            </div>
          </div>
        </div>

      </section>
    </div>
  </div>
</div>
</template>

<!-- Javascript code to import variables, manipulate them, etc. -->
<script>
import Vue from "vue";

import FAQ from "@/assets/faq.json";
export default Vue.extend({
  name: "Faq",
  data() {
    return ({
      faq: null,
      faqGroups: null
    })
  },
  mounted() {
    this.faq = FAQ;
    this.faqGroups = Object.keys(this.faq);
  }
})
</script>

<style lang="scss">
.border-left-main {
    border-left: 10px solid #bfd6ef;
    padding-left: 0.75rem;
}

.font-size-xlarge {
    font-size: larger;
}

.font-size-normal {
    font-size: initial !important;
}
</style>
