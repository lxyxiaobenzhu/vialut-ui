<template>
  <div class="mykit-preview">
    <section>
      <slot></slot>
    </section>

    <div v-show="codeVisible" class="source-code">
      <pre
        class="language-html"
      ><code class="language-html">{{ previewSourceCode }}</code></pre>
    </div>

    <div class="preview-bottom">
      <span name="Code" @click="showSourceCode">{{
        !codeVisible ? "查看实例" : "隐藏"
      }}</span>
    </div>
  </div>
</template>

<script>
import Prism from "prismjs";
import "../assets/prism.css";

const isDev = import.meta.env.MODE === "development";

export default {
  props: {
    /** 组件名称 */
    compName: {
      type: String,
      default: "",
      require: true,
    },
    /** 要显示代码的组件 */
    demoName: {
      type: String,
      default: "",
      require: true,
    },
  },
  data() {
    return {
      sourceCode: "",
      codeVisible: false,
    };
  },
  computed: {
    previewSourceCode() {
      return this.sourceCode.replace(/'\.\.\/\.\.\/index'/g, `'@tencent/viperUi'`);
    },
  },
  async mounted() {
    if (this.compName && this.demoName) {
      if (isDev) {
        this.sourceCode = (
          await import(
            /* @vite-ignore */ `../../packages/${this.compName}/docs/${this.demoName}.vue?raw`
          )
        ).default;
      } else {
        this.sourceCode = await fetch(
          `${isDev ? "" : "/viperUi"}/packages/${this.compName}/docs/${this.demoName}.vue`
        ).then((res) => res.text());
      }
    }
    await this.$nextTick();
    Prism.highlightAll();
  },
  methods: {
    async copyCode() {
      // this.$copyText(this.sourceCode);
    },
    showSourceCode() {
      this.codeVisible = !this.codeVisible;
    },
  },
};
</script>

<style lang="less">
pre {
  line-height: 0;
}
.mykit-preview {
  border: 4px;
  border: 1px dashed #e7e7e7;
  padding: 10px;
  border-bottom: 1px dashed #e7e7e7;
  section {
    margin: 15px;
  }
}

.source-code {
  max-height: 500px;
}
.language-html {
  margin: 0;
  padding: 0 15px;
}
.preview-bottom {
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-top: 1px dashed #e7e7e7;
  cursor: pointer;
  transition: 0.5s;
  font-size: 14px;
  width: 100%;
  height: 100%;
  text-align: center;
}
.preview-bottom :hover {
  color: #fff;
  background: #333;
  transition: 0.5s;
}
.preview-bottom span {
  width: 100%;
}
</style>
