<script setup>


import {onMounted, ref} from 'vue';


const preview = ref('initial');
const previewElement = ref(null);
// const contentEditableElement = ref(null);
let contentEditableElement = null;
let spanElement = null;
let scopedAttributeName;

let styles = {
  bold: {
    isActive: false,
    tagName: 'b',
  },
  italic: {
    isActive: false,
    tagName: 'em'
  },
  underline: {
    isActive: false,
    tagName: 'u',
  },
  delete: {
    isActive: false,
    tagName: 'del',
  },
  code: {
    isActive: false,
    tagName: 'code',
  },
};


onMounted(() => {
  scopedAttributeName = document.querySelector('.content-editable').getAttributeNames()
      .find(attributeName => attributeName.includes('data-v'));

  // let pElement = document.createElement('p');
  // pElement.classList.add('partial-text');
  contentEditableElement = document.getElementById('content-editable');
  createStyledSpanElement(contentEditableElement);
  // contentEditableElement.appendChild(pElement);
});

function createStyledSpanElement(contentEditableElement) {
  let spanElement = document.createElement('span');
  spanElement.setAttribute(scopedAttributeName, '');
  spanElement.classList.add('partial-text');
  // contentEditableElement = document.getElementById('content-editable');
  contentEditableElement.appendChild(spanElement);
}

function handleMouseDown(event) {
  event.preventDefault();

}

function handleClick(event) {
  const styleName = event.currentTarget.getAttribute('id');
  createStyledSpanElement(contentEditableElement);
  styles[styleName].isActive = !styles[styleName].isActive;
  toggleStyleButtonBackground(styles[styleName].isActive, event);

  const stylesActiveNames = Object.keys(styles).filter(
      styleName => styles[styleName].isActive
  );

  let lastNestedElement = null;
  for (const styleActiveName of stylesActiveNames) {
    const nestedElements = document.querySelectorAll('.partial-text');
    const lastNestedElement = nestedElements[nestedElements.length - 1];

    const element = document.createElement(styles[styleActiveName].tagName);
    element.setAttribute(scopedAttributeName, '');
    element.classList.add('partial-text');

    lastNestedElement.appendChild(element);
  }
  moveCaretToLast(contentEditableElement);
}

function toggleStyleButtonBackground(state, event) {
  if (state)
    event.currentTarget.classList.add('active');
  else
    event.currentTarget.classList.remove('active');
}

function moveCaretToLast(contentEditableElement) {
  const nestedElements = document.querySelectorAll('.partial-text');
  const lastNestedElementPartial = nestedElements[nestedElements.length - 1];

  let range = document.createRange();
  range.selectNodeContents(lastNestedElementPartial);
  range.collapse(false);
  let selection = window.getSelection();
  selection.removeAllRanges();
  selection.addRange(range);
}

function handleFocus(event) {
  event.preventDefault();
  moveCaretToLast(contentEditableElement);
}

</script>

<template>

  <div class="editor">
    <div class="font-styles">
      <!--      enum bold-->
      <button class="style-button" id="bold"
              @click="handleClick"
              @mousedown="handleMouseDown">
        B
      </button>
      <button
          class="style-button" id="italic"
          @click="handleClick"
          @mousedown="handleMouseDown">
        <em>I</em>
      </button>
      <button
          class="style-button" id="delete"
          @click="handleClick"
          @mousedown="handleMouseDown">
        <del>S</del>
      </button>
      <!--      <button-->
      <!--          class="style-button" id="code"-->
      <!--          @click="handleClick"-->
      <!--          @mousedown="handleMouseDown">-->
      <!--        {}-->
      <!--      </button>-->
      <button
          class="style-button" id="underline"
          @click="handleClick"
          @mousedown="handleMouseDown">
        <u>U</u>
      </button>
    </div>


    <div id="content-editable" class="content-editable" contenteditable="true" @focus="handleFocus">
    </div>

  </div>

  <!--  <div class="preview" ref="previewElement">-->
  <!--    &lt;p&gt; {{ preview }} &lt;/p&gt-->
  <!--  </div>-->

</template>

<style scoped>



.style-button {
  width: 10em;
  height: 5ex;
  background-color: #e9e9ed;
  font-weight: bold;
  color: black;
  cursor: pointer;
}

.active {
  background-color: rgba(233, 233, 237, 0.85);
}


.editor {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  border: solid 1px;
  padding: 1rem;
}



.font-styles {
  display: flex;
  max-width: 160px;
}

.font-style {
  flex: 1;
}

.preview {
  border: solid 1px #34495e;
  padding: 1rem;
  color: #41b883;
}

.content-editable {
  min-height: 3rem;
  background-color: white;
  color: black;
  padding: 0.2rem;
  white-space: pre-wrap;
}


.content-editable:focus {
  outline: none;
}

.partial-text {
  display: inline-flex;
}

</style>
