<script setup>
import { ref, computed, onMounted } from 'vue';
import MarkdownIt from 'markdown-it';

// Create markdown-it instance
const md = new MarkdownIt({
  html: true,
  linkify: true,
  typographer: true,
  breaks: true
});

const props = defineProps({
  // Source markdown content
  markdown: {
    type: String,
    default: ''
  },
  // File path to load markdown from
  file: {
    type: String,
    default: ''
  },
  // Height of the containers
  height: {
    type: String,
    default: '400px'
  },
  // Theme for the component
  theme: {
    type: String,
    default: 'light' // 'light', 'dark'
  },
  // Title for the source section
  sourceTitle: {
    type: String,
    default: 'Markdown Source'
  },
  // Title for the rendered section
  renderedTitle: {
    type: String,
    default: 'Rendered Output'
  },
  // Font size for the rendered markdown
  fontSize: {
    type: String,
    default: '0.9em'
  },
  // Line height for the rendered markdown
  lineHeight: {
    type: String,
    default: '1.4'
  },
  // Source code font size
  sourceFontSize: {
    type: String,
    default: '0.9em'
  }
});

// State for file content
const fileContent = ref('');
const isLoading = ref(false);
const loadError = ref('');

const markdownContent = computed(() => {
  // If markdown prop is provided, use it
  if (props.markdown && props.markdown.trim() !== '') {
    return props.markdown;
  }
  // Otherwise use file content if available
  return fileContent.value;
});

// Rendered markdown HTML
const renderedMarkdown = computed(() => {
  return md.render(markdownContent.value);
});

// Load file content if file prop is provided
onMounted(async () => {
  if (props.file) {
    isLoading.value = true;
    loadError.value = '';
    
    try {
      // Fetch the file from the public directory
      const response = await fetch(props.file);
      
      if (!response.ok) {
        throw new Error(`Failed to load file: ${response.status} ${response.statusText}`);
      }
      
      const text = await response.text();
      fileContent.value = text;
    } catch (error) {
      console.error('Error loading markdown file:', error);
      loadError.value = `Error loading file: ${error.message}`;
      fileContent.value = loadError.value;
    } finally {
      isLoading.value = false;
    }
  }
});

// Computed styles based on theme
const containerStyle = computed(() => {
  if (props.theme === 'dark') {
    return {
      bg: '#282c34',
      border: '#444',
      header: '#444',
      text: '#abb2bf',
      renderedBg: '#21252b',
      renderedText: '#efefef'
    };
  }
  return {
    bg: '#f9f9f9',
    border: '#e0e0e0',
    header: '#e0e0e0',
    text: '#333',
    renderedBg: '#ffffff',
    renderedText: '#333'
  };
});

// Computed style for font size and line height
const renderedStyle = computed(() => {
  return {
    fontSize: props.fontSize,
    lineHeight: props.lineHeight,
    color: containerStyle.value.renderedText,
    height: props.height
  };
});

// Computed style for source code
const sourceStyle = computed(() => {
  return {
    color: containerStyle.value.text,
    height: props.height,
    fontSize: props.sourceFontSize
  };
});
</script>

<template>
  <div class="markdown-visualizer" :class="theme">
    <div class="container-wrapper">
      <!-- Source markdown container -->
      <div class="container source-container" :style="{
        backgroundColor: containerStyle.bg,
        borderColor: containerStyle.border
      }">
        <div class="header" :style="{ backgroundColor: containerStyle.header }">
          <div class="title">{{ sourceTitle }}</div>
        </div>
        <div class="content source" :style="sourceStyle">
          <div v-if="isLoading" class="loading">Loading...</div>
          <pre v-else><code>{{ markdownContent }}</code></pre>
        </div>
      </div>

      <!-- Rendered markdown container -->
      <div class="container rendered-container" :style="{
        backgroundColor: containerStyle.renderedBg,
        borderColor: containerStyle.border
      }">
        <div class="header" :style="{ backgroundColor: containerStyle.header }">
          <div class="title">{{ renderedTitle }}</div>
        </div>
        <div class="content rendered" :style="renderedStyle">
          <div v-if="isLoading" class="loading">Loading...</div>
          <div v-else v-html="renderedMarkdown"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.markdown-visualizer {
  width: 100%;
  margin: 1rem 0;
}

.container-wrapper {
  display: flex;
  gap: 1rem;
  width: 100%;
}

.container {
  flex: 1;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  border-width: 1px;
  border-style: solid;
}

.header {
  display: flex;
  align-items: center;
  padding: 0.5rem 1rem;
  font-weight: bold;
}

.content {
  padding: 1rem;
  overflow-y: auto;
  scrollbar-width: thin;
}

.content::-webkit-scrollbar {
  width: 6px;
}

.content::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 0, 0.2);
  border-radius: 3px;
}

.source {
  font-family: 'Fira Code', monospace;
  white-space: pre-wrap;
}

.source pre {
  margin: 0;
  padding: 0;
}

.source code {
  display: block;
  width: 100%;
}

.loading {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  font-style: italic;
  opacity: 0.7;
}

/* Ensure the rendered markdown looks good */
.rendered {
  overflow-y: auto;
}

/* Style for rendered markdown elements */
:deep(h1), :deep(h2), :deep(h3), :deep(h4), :deep(h5), :deep(h6) {
  margin-top: 0.7em;
  margin-bottom: 0.3em;
  font-weight: bold;
}

:deep(h1) { font-size: 1.6em; }
:deep(h2) { font-size: 1.4em; }
:deep(h3) { font-size: 1.2em; }
:deep(h4) { font-size: 1.1em; }
:deep(h5), :deep(h6) { font-size: 1em; }

:deep(p) {
  margin-bottom: 0.7em;
  margin-top: 0;
}

:deep(ul), :deep(ol) {
  margin-bottom: 0.7em;
  margin-top: 0.3em;
  padding-left: 1.5em;
}

:deep(li) {
  margin-bottom: 0.2em;
}

:deep(code) {
  font-family: 'Fira Code', monospace;
  background-color: rgba(0, 0, 0, 0.05);
  padding: 0.1em 0.3em;
  border-radius: 3px;
  font-size: 0.9em;
}

:deep(pre) {
  background-color: rgba(0, 0, 0, 0.05);
  padding: 0.7em;
  border-radius: 4px;
  overflow-x: auto;
  margin: 0.7em 0;
}

:deep(pre code) {
  background-color: transparent;
  padding: 0;
}

:deep(blockquote) {
  border-left: 4px solid #ddd;
  padding-left: 0.7em;
  margin-left: 0;
  margin-right: 0;
  margin-top: 0.5em;
  margin-bottom: 0.5em;
  color: #666;
}

:deep(table) {
  border-collapse: collapse;
  width: 100%;
  margin: 0.7em 0;
}

:deep(th), :deep(td) {
  border: 1px solid #ddd;
  padding: 0.3em 0.5em;
  text-align: left;
}

:deep(th) {
  background-color: rgba(0, 0, 0, 0.05);
}

/* Responsive design for smaller screens */
@media (max-width: 768px) {
  .container-wrapper {
    flex-direction: column;
  }
}

/* Dark theme adjustments */
.dark :deep(code) {
  background-color: rgba(255, 255, 255, 0.1);
}

.dark :deep(pre) {
  background-color: rgba(0, 0, 0, 0.3);
}

.dark :deep(blockquote) {
  border-left-color: #555;
  color: #aaa;
}

.dark :deep(th) {
  background-color: rgba(255, 255, 255, 0.1);
}

.dark :deep(th), .dark :deep(td) {
  border-color: #555;
}
</style>