<script setup>
import { ref, computed } from 'vue';
import MarkdownIt from 'markdown-it';

// Create markdown-it instance
const md = new MarkdownIt({
  html: true,
  linkify: true,
  typographer: true,
  breaks: true
});

// Get base path from Vite
const base = import.meta.env.BASE_URL;

// Props to customize the component
const props = defineProps({
  prompt: {
    type: String,
    required: true
  },
  response: {
    type: String,
    required: true
  },
  userIcon: {
    type: String,
    default: '👤'
  },
  aiIcon: {
    type: String,
    default: '🤖'
  },
  userLabel: {
    type: String,
    default: 'User Prompt'
  },
  aiLabel: {
    type: String,
    default: 'AI Response'
  },
  theme: {
    type: String,
    default: 'light' // 'light', 'dark', 'terminal'
  },
  responseMarkdown: {
    type: Boolean,
    default: false
  },
  promptMarkdown: {
    type: Boolean,
    default: true
  },
  additionalContent: {
    type: String,
    default: ''
  },
  additionalContentPosition: {
    type: String,
    default: 'after' // 'before', 'after', 'replace', 'left', 'right'
  },
  additionalContentWidth: {
    type: String,
    default: '50%' // Width for left/right positioning
  },
  maxHeight: {
    type: String,
    default: '' // Optional max height for content containers
  },
  thoughts: {
    type: String,
    default: '' // Optional AI thoughts to display in expandable section
  },
  thoughtsExpanded: {
    type: Boolean,
    default: false // Whether thoughts section is expanded by default
  },
  fontSize: {
    type: String,
    default: '0.9em' // Base font size for the component
  },
  responseHeight: {
    type: String,
    default: '300px' // Default height for response content when scrolling
  }
});

// Copy to clipboard functionality
const copyToClipboard = (text) => {
  navigator.clipboard.writeText(text)
    .then(() => {
      // Optional: Show a temporary success message or change button state
      console.log('Content copied to clipboard');
    })
    .catch(err => {
      console.error('Failed to copy: ', err);
    });
};

// Process the response to handle <br> tags properly
const processedResponse = computed(() => {
  if (props.responseMarkdown) {
    // Replace <br> tags with actual line breaks for markdown processing
    return props.response.replace(/<br>/g, '\n');
  }
  return props.response;
});

// Process the prompt to handle <br> tags properly
const processedPrompt = computed(() => {
  if (props.promptMarkdown) {
    // Replace <br> tags with actual line breaks for markdown processing
    return props.prompt.replace(/<br>/g, '\n');
  }
  // Support line breaks in non-markdown prompts by replacing \n with <br> for HTML display
  return props.prompt.replace(/\n/g, '<br>');
});

// Process additional content to fix image paths
const processedAdditionalContent = computed(() => {
  if (!props.additionalContent) return '';
  
  // Replace image src paths to include the base path
  return props.additionalContent.replace(
    /src=["']\/([^"']+)["']/g, 
    (match, path) => `src="${base}${path}"`
  );
});

// Function to render markdown
const renderMarkdown = (content) => {
  return md.render(content);
};

// Computed styles based on theme
const containerStyle = ref({
  light: {
    prompt: {
      bg: '#f9f9f9',
      border: '#e0e0e0',
      header: '#e0e0e0'
    },
    response: {
      bg: '#f0f8ff',
      border: '#d0e8ff',
      header: '#d0e8ff'
    },
    additional: {
      bg: '#f5f5f5',
      border: '#e5e5e5'
    },
    thoughts: {
      bg: '#fff8e6',
      border: '#ffe0b2',
      text: '#996b00'
    }
  },
  dark: {
    prompt: {
      bg: '#333',
      border: '#444',
      header: '#444'
    },
    response: {
      bg: '#1a2a3a',
      border: '#2a4a6a',
      header: '#2a4a6a'
    },
    additional: {
      bg: '#2a2a2a',
      border: '#3a3a3a'
    },
    thoughts: {
      bg: '#332b00',
      border: '#665500',
      text: '#ffd54f'
    }
  },
  terminal: {
    prompt: {
      bg: '#282c34',
      border: '#444',
      header: '#444',
      text: '#abb2bf'
    },
    response: {
      bg: '#282c34',
      border: '#444',
      header: '#444',
      text: '#98c379'
    },
    additional: {
      bg: '#21252b',
      border: '#444',
      text: '#d19a66'
    },
    thoughts: {
      bg: '#2c313a',
      border: '#555',
      text: '#e5c07b'
    }
  }
}[props.theme]);

// Determine if we should show the additional content
const showAdditionalContent = computed(() => {
  return props.additionalContent && props.additionalContent.trim() !== '';
});

// Determine if we should use horizontal layout
const useHorizontalLayout = computed(() => {
  return ['left', 'right'].includes(props.additionalContentPosition);
});

// Compute the flex direction for horizontal layout
const flexDirection = computed(() => {
  return props.additionalContentPosition === 'right' ? 'row' : 'row-reverse';
});

// Compute the width for response and additional content in horizontal layout
const contentWidth = computed(() => {
  return {
    response: `calc(100% - ${props.additionalContentWidth} - 1rem)`,
    additional: props.additionalContentWidth
  };
});

// Compute max height style if provided
const maxHeightStyle = computed(() => {
  return props.maxHeight ? { maxHeight: props.maxHeight } : {};
});

// State for thoughts expansion toggle - handle both boolean and string values
const isThoughtsExpanded = ref(
  typeof props.thoughtsExpanded === 'string' 
    ? props.thoughtsExpanded.toLowerCase() === 'true'
    : !!props.thoughtsExpanded
);

// Toggle thoughts visibility
const toggleThoughts = () => {
  isThoughtsExpanded.value = !isThoughtsExpanded.value;
};

// Check if thoughts should be displayed
const showThoughts = computed(() => {
  return props.thoughts && props.thoughts.trim() !== '';
});

// State for prompt and response collapsible sections
const isPromptCollapsed = ref(false);
const isResponseCollapsed = ref(false);

// Toggle prompt visibility
const togglePrompt = () => {
  isPromptCollapsed.value = !isPromptCollapsed.value;
};

// Toggle response visibility
const toggleResponse = () => {
  isResponseCollapsed.value = !isResponseCollapsed.value;
};

// Computed style for font size
const fontSizeStyle = computed(() => {
  return { fontSize: props.fontSize };
});
</script>

<template>
  <div class="prompt-example-container" :class="theme" :style="fontSizeStyle">
    <!-- Prompt section -->
    <div class="prompt-container" :style="{
      backgroundColor: containerStyle.prompt.bg,
      borderColor: containerStyle.prompt.border
    }">
      <div class="header collapsible" :style="{ backgroundColor: containerStyle.prompt.header }">
        <div class="avatar user">{{ userIcon }}</div>
        <div class="title" @click="togglePrompt">{{ userLabel }}</div>
        <div class="copy-button" @click.stop="copyToClipboard(prompt)" title="Copy to clipboard">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
          </svg>
        </div>
      </div>
      <div v-show="!isPromptCollapsed" class="content prompt" :style="{ color: containerStyle.prompt.text, ...maxHeightStyle }">
        <div v-if="promptMarkdown" v-html="renderMarkdown(processedPrompt)"></div>
        <div v-else v-html="processedPrompt"></div>
      </div>
    </div>
    
    <!-- Additional content before response -->
    <div v-if="showAdditionalContent && additionalContentPosition === 'before'" 
         class="additional-content-container" 
         :style="{
           backgroundColor: containerStyle.additional.bg,
           borderColor: containerStyle.additional.border,
           color: containerStyle.additional.text
         }">
      <div class="content additional" :style="maxHeightStyle" v-html="processedAdditionalContent"></div>
    </div>
    
    <!-- Horizontal layout for left/right positioning -->
    <div v-if="useHorizontalLayout && showAdditionalContent" 
         class="horizontal-layout"
         :style="{ flexDirection }">
      
      <!-- Response section in horizontal layout -->
      <div v-if="response && response.trim() !== ''"
           class="response-container" 
           :style="{
             backgroundColor: containerStyle.response.bg,
             borderColor: containerStyle.response.border,
             width: contentWidth.response
           }">
        <div class="header collapsible" :style="{ backgroundColor: containerStyle.response.header }">
          <div class="avatar ai">{{ aiIcon }}</div>
          <div class="title" @click="toggleResponse">{{ aiLabel }}</div>
          <div class="copy-button" @click.stop="copyToClipboard(response)" title="Copy to clipboard">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
            </svg>
          </div>
        </div>
        <div v-show="!isResponseCollapsed" class="content response" :style="{ 
             color: containerStyle.response.text, 
             maxHeight: props.responseHeight,
             overflowY: 'auto'
           }">
          <!-- Thoughts section at the top of response -->
          <div v-if="showThoughts" class="thoughts-container-inline">
            <div class="thoughts-header" @click="toggleThoughts">
              <div class="thoughts-toggle">{{ isThoughtsExpanded ? '▼' : '►' }}</div>
              <div class="thoughts-title">Thoughts &gt;</div>
            </div>
            <div v-if="isThoughtsExpanded" 
                 class="thoughts-content" 
                 :style="{
                   backgroundColor: containerStyle.thoughts.bg,
                   borderColor: containerStyle.thoughts.border,
                   color: containerStyle.thoughts.text
                 }">
              <div v-if="responseMarkdown" v-html="renderMarkdown(props.thoughts)"></div>
              <div v-else v-html="props.thoughts"></div>
            </div>
          </div>
          
          <div v-if="responseMarkdown" v-html="renderMarkdown(processedResponse)"></div>
          <div v-else v-html="response"></div>
        </div>
      </div>
      
      <!-- Additional content in horizontal layout -->
      <div class="additional-content-container" 
           :style="{
             backgroundColor: containerStyle.additional.bg,
             borderColor: containerStyle.additional.border,
             color: containerStyle.additional.text,
             width: contentWidth.additional
           }">
        <div class="content additional" :style="maxHeightStyle" v-html="processedAdditionalContent"></div>
      </div>
    </div>
    
    <!-- Standard vertical layout for before/after/replace -->
    <template v-if="!useHorizontalLayout">
      <!-- Response section (skipped if additionalContentPosition is 'replace' or response is empty) -->
      <div v-if="additionalContentPosition !== 'replace' && response && response.trim() !== ''" 
           class="response-container" 
           :style="{
             backgroundColor: containerStyle.response.bg,
             borderColor: containerStyle.response.border
           }">
        <div class="header collapsible" :style="{ backgroundColor: containerStyle.response.header }">
          <div class="avatar ai">{{ aiIcon }}</div>
          <div class="title" @click="toggleResponse">{{ aiLabel }}</div>
          <div class="copy-button" @click.stop="copyToClipboard(response)" title="Copy to clipboard">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
            </svg>
          </div>
        </div>
        <div v-show="!isResponseCollapsed" class="content response" :style="{ 
             color: containerStyle.response.text, 
             maxHeight: props.responseHeight,
             overflowY: 'auto'
           }">
          <!-- Thoughts section at the top of response -->
          <div v-if="showThoughts" class="thoughts-container-inline">
            <div class="thoughts-header" @click="toggleThoughts">
              <div class="thoughts-toggle">{{ isThoughtsExpanded ? '▼' : '►' }}</div>
              <div class="thoughts-title">Thoughts</div>
            </div>
            <div v-if="isThoughtsExpanded" 
                 class="thoughts-content" 
                 :style="{
                   backgroundColor: containerStyle.thoughts.bg,
                   borderColor: containerStyle.thoughts.border,
                   color: containerStyle.thoughts.text
                 }">
              <div v-if="responseMarkdown" v-html="renderMarkdown(props.thoughts)"></div>
              <div v-else v-html="props.thoughts"></div>
            </div>
          </div>
          
          <div v-if="responseMarkdown" v-html="renderMarkdown(processedResponse)"></div>
          <div v-else v-html="response"></div>
        </div>
      </div>
      
      <!-- Additional content after response or as replacement -->
      <div v-if="showAdditionalContent && (additionalContentPosition === 'after' || additionalContentPosition === 'replace')" 
           class="additional-content-container" 
           :style="{
             backgroundColor: containerStyle.additional.bg,
             borderColor: containerStyle.additional.border,
             color: containerStyle.additional.text
           }">
        <div class="content additional" :style="maxHeightStyle" v-html="processedAdditionalContent"></div>
      </div>
            
    </template>
  </div>
</template>

<style scoped>
.prompt-example-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  width: 100%;
  margin: 1rem 0;
  max-width: 100%;
  overflow-x: hidden;
}

.horizontal-layout {
  display: flex;
  gap: 1rem;
  width: 100%;
}

.prompt-container, .response-container, .additional-content-container {
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  border-width: 1px;
  border-style: solid;
  width: 100%;
}

.header {
  display: flex;
  align-items: center;
  padding: 0.5rem 1rem;
  font-weight: bold;
  gap: 0.5rem;
  position: relative;
}

.collapsible {
  cursor: pointer;
  user-select: none;
  position: relative;
  transition: background-color 0.2s;
}

.collapsible:hover {
  filter: brightness(0.95);
}

.toggle-icon {
  margin-left: auto;
  font-size: 0.8em;
  opacity: 0.7;
}

.avatar {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  border-radius: 50%;
}

.copy-button {
  margin-left: auto;
  opacity: 0;
  cursor: pointer;
  transition: opacity 0.2s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 4px;
  border-radius: 4px;
}

.header:hover .copy-button {
  opacity: 0.7;
}

.copy-button:hover {
  opacity: 1 !important;
  background-color: rgba(0, 0, 0, 0.05);
}

.content {
  padding: 0.5rem;
  overflow-x: auto;
  transition: max-height 0.3s ease-out;
}

.response {
  overflow-y: auto;
  scrollbar-width: thin;
}

.response::-webkit-scrollbar {
  width: 6px;
}

.response::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 0, 0.2);
  border-radius: 3px;
}

.additional {
  width: 100%;
  box-sizing: border-box;
}

.terminal .content {
  font-family: 'Fira Code', monospace;
}

/* For code blocks inside the response */
:deep(pre) {
  margin: 0.5rem 0;
  padding: 0.75rem;
  border-radius: 4px;
  background-color: rgba(0, 0, 0, 0.05);
  overflow-x: auto;
}

:deep(code) {
  font-family: 'Fira Code', monospace;
  font-size: 0.9em;
}

.terminal :deep(pre) {
  background-color: #21252b;
}

.terminal :deep(code) {
  color: #98c379;
}

/* Image styling */
:deep(img) {
  max-width: 100%;
  height: auto;
  border-radius: 4px;
  margin: 0.5rem 0;
  object-fit: contain;
}

/* Center images if desired */
.additional-content-container :deep(img) {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

/* Responsive adjustments */
@media (max-width: 640px) {
  .horizontal-layout {
    flex-direction: column !important;
  }
  
  .horizontal-layout .response-container,
  .horizontal-layout .additional-content-container {
    width: 100% !important;
  }
}

/* Ensure iframes and other embedded content don't overflow */
:deep(iframe), :deep(video), :deep(embed), :deep(object) {
  max-width: 100%;
  height: auto;
}

/* Handle tables properly */
:deep(table) {
  width: 100%;
  border-collapse: collapse;
  overflow-x: auto;
  display: block;
}

:deep(th), :deep(td) {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

/* Thoughts section styling */
.thoughts-container-inline {
  margin-bottom: 1rem;
  border-bottom: 1px dashed rgba(0, 0, 0, 0.1);
  padding-bottom: 0.5rem;
}

.thoughts-header {
  display: flex;
  align-items: center;
  padding: 0.5rem;
  cursor: pointer;
  font-weight: 500;
  border-radius: 4px;
  background-color: rgba(0, 0, 0, 0.05);
  transition: background-color 0.2s;
}

.thoughts-header:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

.thoughts-toggle {
  margin-right: 0.5rem;
  font-size: 0.8em;
  transition: transform 0.2s;
}

.thoughts-title {
  font-family: monospace;
}

.thoughts-content {
  margin-top: 0.25rem;
  padding: 1rem;
  border-radius: 6px;
  border-width: 1px;
  border-style: solid;
  overflow-x: auto;
  font-family: monospace;
  line-height: 1.5;
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-10px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Animation for collapsible sections */
.content {
  animation: slideDown 0.3s ease-out;
}

@keyframes slideDown {
  from { opacity: 0.8; transform: translateY(-5px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>