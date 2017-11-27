<template>
  <a class="i-button" :href="href" :disabled="disabled" :target="target" :rel="newRel" @click="$emit('click', $event)" :style="style" v-if="href">
    <md-ink-ripple :md-disabled="disabled"></md-ink-ripple>
    <i-tooltip v-if="tooltip" :md-direction="direction">{{ tooltip }}</i-tooltip>
    <slot></slot>
  </a>

  <button class="i-button" :type="type" :disabled="disabled" @click="$emit('click', $event)" :style="style" v-else>
    <md-ink-ripple :md-disabled="disabled"></md-ink-ripple>
    <i-tooltip v-if="tooltip" :md-direction="direction">{{ tooltip }}</i-tooltip>
    <slot></slot>
  </button>
</template>

<!--
<style lang="scss" src="../../i-colors/src/i-colors.scss" scoped></style>
-->
<style lang="css" src="../node_modules/i-colors/dist/i-colors.css" scoped></style>

<script>
import mdInkRipple from './mdInkRipple/mdInkRipple.vue';
// import iTooltip from '../../i-tooltip/src/iTooltip.vue';
import iTooltip from 'i-tooltip';

export default {
  name: 'md-button',
  components: {
    'md-ink-ripple': mdInkRipple,
    iTooltip,
  },
  props: {
    href: String,
    target: String,
    rel: String,
    type: {
      type: String,
      default: 'button',
    },
    disabled: Boolean,
    isBackground: String,
    isOutline: String,
    isColor: String,
    tooltip: String,
    tooltipPos: String,
    uppercase: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    newRel() {
      if (this.target === '_blank') {
        return this.rel || 'noopener';
      }
      return this.rel;
    },
    style() {
      return {
        'background-color': this.isBackground,
        'text-transform': this.uppercase ? 'uppercase' : '',
        'border-color': this.isOutline,
        color: this.isColor,
      };
    },
    direction() {
      return this.tooltipPos || 'top';
    },
  },
};
</script>