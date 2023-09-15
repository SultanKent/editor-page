<template>
  <div class="editor-container">
    <div class="toolbar">
      <img src="../assets/Editor1.png" @click="undo"/>
      <img src="../assets/Editor2.png" @click="redo" />
      <img src="../assets/Editor3.png" @click="toggleBlock('h1')"/>
      <img src="../assets/Editor4.png" @click="toggleBlock('p')"/>
      <div class="input-icon">
      <input type="file" @change="uploadImage" class="file-input">
      </div>
      <button @click="getHtmlCode" class="getHtml">Скопировать HTML</button>
    </div>
    <div class="editor" contenteditable="true" @input="updateContent" ref="editor" @focus="onFocus('editor')">
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nullam vehicula, mi eget tincidunt iaculis, arcu dui pharetra mi, at dictum metus elit vel odio.
    </div>
    <img :src="image" class="input-img" alt="Изображение" v-if="image" />

    <div class="editor" contenteditable="true" @input="updateContent2" ref="editor2" @focus="onFocus('editor2')">
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nullam vehicula, mi eget tincidunt iaculis, arcu dui pharetra mi, at dictum metus elit vel odio.
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      contentHistory: [],
      currentHistoryIndex: -1,
      image: '',
      content2History: [],
      current2HistoryIndex: -1,
      focusedEditor: 'editor',
    };
  },
  methods: {
    updateContent(event) {
      const content = event.target.innerHTML;
      this.saveHistory(content);
    },
    saveHistory(content) {
      this.contentHistory.splice(this.currentHistoryIndex + 1);
      this.contentHistory.push(content);
      this.currentHistoryIndex++;
    },
    updateContent2(event) {
      const content = event.target.innerHTML;
      this.saveHistory2(content);
    },
    saveHistory2(content) {
      this.content2History.splice(this.current2HistoryIndex + 1);
      this.content2History.push(content);
      this.current2HistoryIndex++;
    },
    onFocus(editor) {
      this.focusedEditor = editor;
    },
    undo() {
    if (this.focusedEditor === 'editor') {
      if (this.currentHistoryIndex > 0) {
        this.currentHistoryIndex--;
        this.updateEditorContent(this.$refs.editor, this.contentHistory[this.currentHistoryIndex]);
      }
    } else if (this.focusedEditor === 'editor2') {
      if (this.current2HistoryIndex > 0) {
        this.current2HistoryIndex--;
        this.updateEditorContent(this.$refs.editor2, this.content2History[this.current2HistoryIndex]);
      }
    }
  },
  redo() {
    if (this.focusedEditor === 'editor') {
      if (this.currentHistoryIndex < this.contentHistory.length - 1) {
        this.currentHistoryIndex++;
        this.updateEditorContent(this.$refs.editor, this.contentHistory[this.currentHistoryIndex]);
      }
    } else if (this.focusedEditor === 'editor2') {
      if (this.current2HistoryIndex < this.content2History.length - 1) {
        this.current2HistoryIndex++;
        this.updateEditorContent(this.$refs.editor2, this.content2History[this.current2HistoryIndex]);
      }
    }
  },
    toggleBlock(tagName) {
      const editor = this.focusedEditor === 'editor' ? this.$refs.editor : this.$refs.editor2;
      const selection = window.getSelection();
      const range = selection.getRangeAt(0);

      if (!range.collapsed) {
        const wrapper = document.createElement(tagName);
        range.surroundContents(wrapper);
      }
    },
    uploadImage(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = () => {
          this.image = reader.result;
        };
        reader.readAsDataURL(file);
      }
    },
    getHtmlCode() {
      const tempContainer = document.createElement('div');
      tempContainer.appendChild(document.documentElement.cloneNode(true));
      const base64Images = tempContainer.querySelectorAll('img[src^="data:image"]');
      base64Images.forEach(image => image.remove());
      const fullPageHtml = tempContainer.innerHTML;
      this.copyToClipboard(fullPageHtml);
    },
    
    copyToClipboard(text) {
      const textarea = document.createElement('textarea');
      textarea.value = text;
      document.body.appendChild(textarea);
      textarea.select();
      document.execCommand('copy');
      document.body.removeChild(textarea);
      alert('HTML-код скопирован в буфер обмена.');
    },
    updateEditorContent(editor, content) {
      if (editor) {
        editor.innerHTML = content;
      }
    },
    execCommandOnEditor(command, showUI, value) {
      if (this.focusedEditor === 'editor') {
        document.execCommand(command, showUI, value);
      } else if (this.focusedEditor === 'editor2') {
        document.execCommand(command, showUI, value);
      }
    },
  },
};
</script>

<style scoped>
.editor-container {
  display: flex;
  flex-direction: column;
  width: 639px;
  margin: 0 auto;
  gap: 31px;
}

.toolbar {
  display: flex;
  gap: 15px;
  align-items: center;
}

.editor {
  border: none;
  outline: none;
}

.input-icon {
  background: url(../assets/Editor5.png) no-repeat;
}

.file-input {
  opacity: 0;
  width: 42px;
  height: 38px;
  cursor: pointer;
}

.file-input::-webkit-file-upload-button {
  visibility: hidden;
}
.file-input::-moz-file-upload-button {
  visibility: hidden;
}

.input-img {
  height: fit-content;
  border-radius: 4px;
}

.getHtml {
  border: none;
  background: transparent;
  color: #639EFF;
}
</style>