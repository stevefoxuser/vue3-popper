<template>
  <div>
    <span class="anchor">
      <slot class="anchor"
            name="anchor"></slot>
    </span>
    <div class="tooltip">
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
          offset: [0, 8],
        },
      }],
      inited: false
    }
  },
  props: ['placement', 'trigger', 'hideclose'],
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
      this.tooltip = this.$el.querySelector('.tooltip')
      this.popperInstance = createPopper(this.anchor, this.tooltip, {
        placement: this.placement || 'right',
        modifiers: this.basicOpt,
      })
      this.inited = true
      return this
    },
    show () {
      if (!this.inited) return console.log('Popper is not initialized.')
      this.hide()
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
          return this.$tools.FindParent(e.target, (p) => {
            return this.$tools.hasClass(p, this.anchor.className) || this.$tools.hasClass(p, this.tooltip.className)
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
      this.popperInstance.update()
      this.unbindEvents()
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
        document.addEventListener('click', this.hide)
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
    }
  }}
</script>

<style lang="scss" scped>
$bcolor: #ccc;
.tooltip {
  background: #fff;
  padding: 0.2rem 0.08rem 0.12rem 0.08rem;
  border-radius: 0.04rem;
  display: none;
  z-index: 999;
  border: 1px solid $bcolor;
  box-shadow: 0 0rem 0.05rem #999;
}
.close {
  position: absolute;
  right: 0.25rem;
  top: 0.05rem;
  font-family: cursive, monospace, 'Courier New';
  font-size: 0.25rem;
  color: #999;
  cursor: pointer;
}
.tooltip[data-show] {
  display: block;
}

.arrow,
.arrow::before {
  position: absolute;
  width: 0.1rem;
  height: 0.1rem;
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
  bottom: -0.05rem;
  border-right: 1px solid #ccc;
}

.tooltip[data-popper-placement^='bottom'] > .arrow {
  top: -0.05rem;
}

.tooltip[data-popper-placement^='left'] > .arrow {
  right: -0.05rem;
}

.tooltip[data-popper-placement^='right'] > .arrow {
  left: -0.05rem;
}
.tooltip[data-popper-placement^='top'] > .arrow::before {
  box-shadow: 0.025rem 0.025rem 0.02rem #ccc;
}

.tooltip[data-popper-placement^='bottom'] > .arrow::before {
  box-shadow: -0.025rem -0.025rem 0.02rem #ccc;
}

.tooltip[data-popper-placement^='left'] > .arrow::before {
  border-right: 1px solid $bcolor;
  box-shadow: 0.025rem -0.025rem 0.02rem #ccc;
}

.tooltip[data-popper-placement^='right'] > .arrow::before {
  box-shadow: -0.025rem 0.025rem 0.02rem #ccc;
}
</style>
