<template>
  <div class="vue-codemirror">
    <textarea ref="textarea" :name="name" :placeholder="placeholder"></textarea>
  </div>
</template>

<script>
import _CodeMirror from 'codemirror';
const CodeMirror = window.CodeMirror || _CodeMirror;

export default {
  name: 'codemirror',
  data() {
    return {
      content: '',
      getInstance: null
    }
  },
  props: {
    code: String,
    modelValue:  {
      type: String,
      default: ''
    },
    marker: Function,
    unseenLines: Array,
    name: {
      type: String,
      default: 'codemirror'
    },
    placeholder: {
      type: String,
      default: ''
    },
    options: {
      type: Object,
      default: () => ({})
    },
    events: {
      type: Array,
      default: () => ([])
    },
    globalOptions: {
      type: Object,
      default: () => ({})
    },
    globalEvents: {
      type: Array,
      default: () => ([])
    }
  },

  watch: {
    content(newData) {
      this.$emit('update:modelValue', newData);
    },

    modelValue: {
      handler(newData) {
        if (!this.getInstance) {
          return;
        }
        if (newData === this.getValue()) {
          return;
        }
        let info = this.getScrollInfo();
        this.setValue(newData);
        this.scrollTo(info.left, info.top);

      },
      immediate: true
    },
  },

  methods: {
    getValue() {
      if (!this.getInstance) {
        return '';
      }
      return this.getInstance().getValue();
    },

    setValue(value) {
      if (!this.getInstance) {
        return;
      }

      this.getInstance().setValue(value);
    },

    getScrollInfo() {
      if (!this.getInstance) {
        return '';
      }
      return this.getInstance().getScrollInfo();
    },

    scrollTo(left, top) {
      if (!this.getInstance) {
        return '';
      }
      return this.getInstance().scrollTo(left, top);
    },

    onChange(cm) {
      this.content = cm.getValue();
    },

    initialize() {
      const cmOptions = Object.assign({}, this.globalOptions, this.options)
      let codemirror = CodeMirror.fromTextArea(this.$refs.textarea, cmOptions)

      for (const key in cmOptions) {
        codemirror.setOption(key, cmOptions[key]);
      }

      codemirror.setValue(this.modelValue);

      const tmpEvents = {}
      const allEvents = [
        'scroll',
        'changes',
        'beforeChange',
        'cursorActivity',
        'keyHandled',
        'inputRead',
        'electricInput',
        'beforeSelectionChange',
        'viewportChange',
        'swapDoc',
        'gutterClick',
        'gutterContextMenu',
        'focus',
        'blur',
        'refresh',
        'optionChange',
        'scrollCursorIntoView',
        'update'
      ];

      allEvents.concat(this.events)
          .concat(this.globalEvents)
          .filter(e => (!tmpEvents[e] && (tmpEvents[e] = true)))
          .forEach(event => {
            codemirror.on(event, (...args) => {
              this.$emit(event, ...args)
              const lowerCaseEvent = event.replace(/([A-Z])/g, '-$1').toLowerCase()
              if (lowerCaseEvent !== event) {
                this.$emit(lowerCaseEvent, ...args)
              }
            })
          })

      codemirror.on('change', this.onChange);
      this.$emit('ready', codemirror);
      this.$nextTick(() => {
        codemirror.refresh()
      });

      this.getInstance = function () {
        return codemirror;
      };
    },

  },
  mounted() {
    this.initialize();
  }
}
</script>
