<template>
  <div>
    <span class="anchor">
      <slot class="anchor"
            name="anchor"></slot>
    </span>
    <div class="tooltip"
         :class="clearstyle?'clearstyle':''">
      <slot></slot>
      <div class="arrow"
           data-popper-arrow></div>
      <div class="close"
           @click="hide(false)"
           v-if="!hideclose"><span>x</span>
      </div>
    </div>
  </div>
</template>

<script>
import { createPopper } from '@popperjs/core'
export default {
  name: 'createPopper',
  data () {
    return {
      popperInstance: {},
      tooltip: {},
      anchor: {},
      basicOpt: [{
        name: 'offset',
        options: {
          offset: [0, 10],
        },
      }]
    }
  },
  props: ['placement', 'trigger', 'hideclose', 'clickpagetoclose', 'clearstyle'],
  created () {
  },
  mounted () {
    this.init()
  },
  unmounted () {
    this.unbindEvents()
  },
  methods: {
    // 如果按钮的slot没有赋值，则延迟初始化
    init (anchor) {
      this.anchor = anchor || this.$el.querySelector('.anchor')
      if (this.anchor.innerHTML === '') {
        return
      }
      this.addClass(this.anchor, 'anchor')
      this.tooltip = this.$el.querySelector('.tooltip')
      if (this.popperInstance.destroy) {
        this.popperInstance.destroy()
      }
      this.popperInstance = createPopper(this.anchor, this.tooltip, {
        placement: this.placement || 'auto',
        modifiers: this.basicOpt,
      })
      this.bindEvents()
      return this
    },
    show (event) {
      if (event.target) {
        this.init(event.target)
      }
      this.hide('NoCallback')
      setTimeout(() => {
        this.display()
      })
      this.bindEvents()
    },
    display () {
      this.tooltip.setAttribute('data-show', '')
      const opt = this.basicOpt.concat([{ name: 'eventListeners', enabled: true }])
      this.popperInstance.setOptions({
        modifiers: opt,
      })
      this.popperInstance.update()
    },
    hide (e) {
      const trigger = this.trigger || 'click'
      if (trigger === 'click' && e && e.target) {
        const contains = (() => {
          return this.findParent(e.target, (p) => {
            return this.hasClass(p, this.anchor.className) || this.hasClass(p, this.tooltip.className)
          })
        })()
        if (contains) {
          return
        }
      }
      this.tooltip.removeAttribute('data-show')
      const opt = this.basicOpt.concat([{ name: 'eventListeners', enabled: false }])
      this.popperInstance.setOptions({
        modifiers: opt,
      })
      document.removeEventListener('click', this.hide)
      if (e !== 'NoCallback') {
        this.$emit('close')
      }
    },
    hideByClick (e) {
      if (this.clickpagetoclose === false) return
      this.hide(e)
    },
    bindEvents () {
      this.unbindEvents()
      const trigger = this.trigger || 'click'
      if (trigger === 'hover') {
        this.anchor.addEventListener('mouseenter', this.show)
        this.anchor.addEventListener('mouseleave', this.hide)
      } else if (trigger === 'focus') {
        this.anchor.addEventListener('focus', this.show)
        this.anchor.addEventListener('blur', this.hide)
      } else if (trigger === 'click') {
        this.anchor.addEventListener('click', this.show)
        document.addEventListener('click', this.hideByClick)
      } else if (trigger === 'manual') {

      }
    },
    unbindEvents () {
      const showEvents = ['mouseenter', 'focus', 'click']
      const hideEvents = ['mouseleave', 'blur']
      showEvents.forEach(event => {
        this.anchor.removeEventListener(event, this.show);
      })
      hideEvents.forEach(event => {
        this.anchor.removeEventListener(event, this.hide);
      })
      document.removeEventListener('click', this.hide)
    },
    findParent: function (el, expr) {
      let p = el
      while (true) {
        if (expr(p)) return p
        if (!p) return null
        if (p.tagName === 'BODY') return null
        p = p.parentNode
      }
    },
    hasClass: function (ele, cls) {
      if (!ele || !ele.className) return false
      return ele.className.match(new RegExp('(\\s|^)' + cls + '(\\s|$)'))
    },
    addClass: function (ele, cls) {
      if (!this.hasClass(ele, cls)) ele.className += ' ' + cls
    }
  }}
</script>

<style lang="scss" scped>
$bcolor: #ccc;
.tooltip {
  background: #fff;
  padding: 30px 8px 12px 8px;
  border-radius: 4px;
  display: none;
  z-index: 999;
  border: 1px solid $bcolor;
  box-shadow: 0 0 5px #999;
}
.close {
  position: absolute;
  right: 25px;
  top: 5px;
  font-family: cursive, monospace, 'Courier New';
  font-size: 25px;
  color: #999;
  cursor: pointer;
}
.tooltip[data-show] {
  display: block;
}

.arrow,
.arrow::before {
  position: absolute;
  width: 10px;
  height: 10px;
  background: inherit;
}

.arrow {
  visibility: hidden;
}

.arrow::before {
  visibility: visible;
  content: '';
  transform: rotate(45deg);
}

.tooltip[data-popper-placement^='top'] > .arrow {
  bottom: -5px;
  border-right: 1px solid #ccc;
}

.tooltip[data-popper-placement^='bottom'] > .arrow {
  top: -5px;
}

.tooltip[data-popper-placement^='left'] > .arrow {
  right: -5px;
}

.tooltip[data-popper-placement^='right'] > .arrow {
  left: -5px;
}
.tooltip[data-popper-placement^='top'] > .arrow::before {
  box-shadow: 2.5px 2.5px 2px #ccc;
}

.tooltip[data-popper-placement^='bottom'] > .arrow::before {
  box-shadow: -2.5px -2.5px 2px #ccc;
}

.tooltip[data-popper-placement^='left'] > .arrow::before {
  border-right: 1px solid $bcolor;
  box-shadow: 2.5px -2.5px 2px #ccc;
}

.tooltip[data-popper-placement^='right'] > .arrow::before {
  box-shadow: -2.5px 2.5px 2px #ccc;
}

.tooltip.clearstyle {
  padding: 0;
  border: 1px solid $bcolor;
}
</style>
