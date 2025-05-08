---
# You can also start simply with 'default'
theme: default
background: https://wallpaperaccess.com/full/4954296.jpg
# some information about your slides (markdown enabled)
title: Prompt Driven Development
info: |
  An overview of what's going on out there
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
---

---


<style>

/* global styles */
.slidev-layout.quote p {
  font-style: italic;
  font-size: 3rem;
  line-height: 5rem;
  text-align: center;

  &::before, &::after {
    content: '"';
  }
}

.slidev-vclick-current {
  font-weight: 600;
}

.slidev-layout ul {
  padding-top: 2rem;
  list-style: none;
}

li {
  font-size: 1.5rem;
  padding-bottom: 1.5rem
}

</style>

# Prompt Driven Development

A quick glance at how producing software
<span v-mark="{type: 'strike-through', iterations: 4, strokeWidth: 4, color: 'red'}">has changed</span><v-click at=1> has been changing recently and keeps on speeding up.</v-click>

<ul>
<v-clicks>
<li>
1. AI assisted coding
</li>
<li>
2. AI as a peer
</li>
<li>
3. Vibe coding / prompt-driven-development
</li>
</v-clicks>
</ul>

<!--

We don't know wha't coming but we know – like the SpiceGirls sing – things (aka "software development") will never be the same again

-->

---

## bolt.new – an OG of agentic software development

<PromptExample
  prompt="Build a mobile app that I can use with my wife to do the weekly shopping."
  response="Based on your requirements, I'll create a beautifully designed shopping app for you and your wife to manage your weekly shopping together. The app will focus on collaboration, ease of use, and visual appeal with an Apple-inspired aesthetic."
  aiLabel="bolt.new"
  additionalContent="<img src='/bolt_2.png' />"
  additionalContentPosition="right"
  additionalContentWidth="20%"
/>

<!--
[project](https://bolt.new/~/sb1-curscjb9)
-->

---
layout: full
---

### Hip-Shot-Development

<img src="/bolt_1.png" alt="A whole project"/>

---

### Workaholic AI

<PromptExample
fontSize="0.8em"
prompt="I want to build an app for shopping groceries. Before we start with the actual application, I want you to help he shape the requirements in a project design document (PDD)"
thoughts="<li>The user wants to build a grocery shopping app and is asking for help with creating a Project Design Document (PDD) first. They've provided specific guidelines for how I should respond, emphasizing beautiful design, readability, and proper organization.</p
<li>I need to create a comprehensive PDD for a grocery shopping app. I'll focus on creating:</li>
<ol>
<li>An approach statement</li>
<li>Core features (4-8 bullet points)</li>
<li>Design elements (bullet points)</li>
</ol>
<li>For a grocery shopping app, I'll need to consider features like:</li>
<ul>
<li>Product browsing and searching</li>
<li>Shopping cart functionality</li>
<li>User accounts</li>
<li>Order history</li>
<li>Payment processing</li>
<li>Delivery options</li>
<li>Product categories</li>
<li>Favorites/saved items</li>
</ul>
<li>For the design, I'll focus on:</li>
<ul>
<li>Modern, clean interface</li>
<li>Appropriate color scheme</li>
<li>Typography</li>
<li>Responsive design</li>
<li>Navigation patterns</li>
<li>Visual hierarchy</li>
</ul>
<li>Let me craft a response following the required format.</li>"
  response="<li>Based on your requirements, I'll help you create a comprehensive Project Design Document (PDD) for a grocery shopping application. We'll focus on creating a beautifully designed, user-friendly experience that combines functionality with Apple-level aesthetics while ensuring it serves the core needs of grocery shoppers.</li>
<li><strong>Core Features</strong>:</li>
<ul>
<li>Intuitive product browsing with categorization (produce, dairy, meat, etc.)</li>
<li>Smart search functionality with filters and suggestions</li>
<li>Shopping list creation and management</li>
<li>Shopping cart with easy add/remove functionality</li>
<li>Order history and one-click reordering</li>
<li>Personalized recommendations based on purchase history</li>
<li>Delivery scheduling with time slot selection</li>
<li>Seamless checkout process with multiple payment options</li>
</ul>
<li><strong>Design Elements</strong>:</li>
<ul>
<li>Clean, minimalist interface with ample white space and a focus on product imagery</li>
<li>Color system featuring fresh green primary (#34D399), complementary blue (#60A5FA), accent orange (#FB923C), with success, warning, and error states</li>
<li>Subtle animations for cart additions, transitions between screens, and loading states</li>
<li>Typography using a modern sans-serif font family with varying weights for hierarchy</li>
<li>Card-based product displays with high-quality imagery and clear pricing</li>
<li>Bottom navigation on mobile, expanding to side navigation on larger screens</li>
<li>Micro-interactions that provide feedback for user actions (adding items, completing checkout)</li>"
  aiLabel="bolt.new"
/>

<!--
[project](https://bolt.new/~/sb1-21tkywal)
-->

---

### AI business engineer – still workaholic

<MarkdownVisualizer
  file="/grocery-app-pdd-bolt.md"
  height="400px"
  sourceTitle="AI generated PDD"
  renderedTitle="Preview"
  sourceFontSize="0.75em"
  fontSize="0.75em"
  lineHeight="1.2"
/>

<!-- 
Now, we can at least influence what the AI will try to achieve. 
The documentation created will be usable as context for the subsequent requests.
-->

---

### We didn't ask for user flows

<MarkdownVisualizer
  file="/grocery-app-user-flows-bolt.md"
  height="400px"
  sourceTitle="User flows"
  renderedTitle="Preview"
  sourceFontSize="0.75em"
  fontSize="0.75em"
  lineHeight="1.2"
/>

---

### We didn't ask for wireframes

<MarkdownVisualizer
  file="/grocery-app-wireframes-bolt.md"
  height="400px"
  sourceTitle="Wireframes"
  renderedTitle="Preview"
  sourceFontSize="0.75em"
  fontSize="0.75em"
  lineHeight="1.2"
/>

---

### We didn't ask for technical architecture

<MarkdownVisualizer
  file="/grocery-app-ta-bolt.md"
  height="400px"
  sourceTitle="Technical architecture"
  renderedTitle="Preview"
  sourceFontSize="0.75em"
  fontSize="0.75em"
  lineHeight="1.2"
/>

---

<style scoped>
p {font-size: 2rem};
</style>

... but it 💯 makes sense to think about it!

<ul>
<v-clicks>
<li>
It's magic 🪄
</li>
<li>
It's AI 🤖
</li>
<li>
It's agentic 🦾🛠️
</li>
</v-clicks>
</ul>

---
layout: quote
---

The future of software development is about setting the proper boundaries for agentic systems

---

## How can we as software developers set boundaries?

<ul>
<v-clicks>
<li>Be aware of the agents in our development environment &nbsp; 🤖🤖🤖
</li>
<li>User Prompt</li>
<li>System Prompt</li>
<li>Context</li>
</v-clicks>
</ul>

---