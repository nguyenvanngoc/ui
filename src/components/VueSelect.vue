<template>
  <VueDropdown
    ref="dropdown"
    class="vue-select"
    v-bind="$attrs"
    v-on="$listeners"
    :disabled="finalDisabled"
    :icon-left="displayedIcon"
    :icon-right="iconRight"
    :label="displayedLabel"
    :popover-class="['popover', 'select-popover', popoverClass]"
    content-class="vue-select-popover-content"
    force-min-size
  >
    <template slot="trigger">
      <slot name="trigger" :label="displayedLabel"/>
    </template>

    <VueGroup
      v-model="valueModel"
      class="vertical"
    >
      <slot/>
    </VueGroup>
  </VueDropdown>
</template>

<script>
import DisabledChild from '../mixins/DisabledChild'

export default {
  name: 'VueSelect',

  inheritAttrs: false,

  mixins: [
    DisabledChild,
  ],

  provide () {
    return {
      VueSelect: {
        setCurrentChild: this.setCurrentChild,
      },
    }
  },

  props: {
    iconLeft: {
      type: String,
      default: null,
    },

    iconRight: {
      type: String,
      default: 'keyboard_arrow_down',
    },

    label: {
      type: [String, Number],
      default: null,
    },

    placeholder: {
      type: String,
      default: 'Select...',
    },

    popoverClass: {
      type: String,
      default: undefined,
    },

    value: {},
  },

  data () {
    return {
      currentChild: null,
    }
  },

  computed: {
    displayedLabel () {
      if (this.label) {
        return this.label
      } else if (this.currentChild) {
        return this.currentChild.selectLabel ||
          this.currentChild.$attrs.label ||
          this.value
      } else if (this.placeholder) {
        return this.placeholder
      } else {
        return this.value
      }
    },

    displayedIcon () {
      if (this.iconLeft) {
        return this.iconLeft
      } else if (this.currentChild) {
        return this.currentChild.$attrs.icon ||
          this.currentChild.$attrs['icon-left']
      }
    },

    valueModel: {
      get () { return this.value },
      set (value) { this.$emit('input', value) },
    },
  },

  methods: {
    setCurrentChild (vm) {
      this.currentChild = vm
    },
  },
}
</script>

<style lang="stylus">
.vue-select
  .dropdown-trigger
    .vue-button
      > .content > .default-slot
        flex auto 1 1

.vue-select-popover-content
  padding 0 4px
  max-height 220px
  overflow-y auto
</style>
