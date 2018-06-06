<template>
  <section class="i-select" :class="{disabled}">
    <input ref="search" v-model="search" @focus="openDropdown" @click="openDropdown" @blur="gobacktomodel()" :placeholder="filter && placeholder"
      :readonly="!filter" :class="{ 'readonly': !filter }">

    <i ref="clear" class="clear" @click="clearSearch" v-show="dropdownOpen" :style="{ height }">
      <slot name="clear">
        X
      </slot>
    </i>

    <i ref="icon" class="icon" @click="openDropdown" v-show="!dropdownOpen" :style="{ height }">
      <slot name="icon">
        <svg version="1.1" xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 768 768">
          <path :style="{ 'fill': isColor }" d="m 438.85712,329.14286 h 329.1429 l -164.5714,164.57143 z"></path>
        </svg>
      </slot>
    </i>

    <!-- <div v-else ref="toggle" role="button" @mousedown.prevent="openDropdown" :class="{inline, disabled}" :style="{style}">
      <input value="1" type="hidden" v-model="model">
      <span ref="selected" :style="{ 'color': `${isColor} !important` }">
        {{ getLabel(mutableValue) }}
      </span>

      <i ref="icon" class="icon">
        <slot name="icon">
          <svg version="1.1" xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 768 768">
            <path :style="{ 'fill': isColor }" d="m 438.85712,329.14286 h 329.1429 l -164.5714,164.57143 z"></path>
          </svg>
        </slot>
      </i>
    </div> -->

    <!-- <slot name="spinner">
      <div class="spinner" v-show="mutableLoading">Loading...</div>
    </slot> -->

    <transition name="fade">
      <section v-show="dropdownOpen" :style="style" ref="dropdown">
        <!-- <input ref="search" v-model="search" @keydown.delete="maybeDeleteValue" @keyup.esc="onEscape" @keydown.up.prevent="typeAheadUp"
          @keydown.down.prevent="typeAheadDown" @keydown.enter.prevent="typeAheadSelect" @blur="onSearchBlur" @focus="onSearchFocus"
          type="search" class="form-control" :disabled="disabled" :placeholder="searchPlaceholder" :tabindex="tabindex" :readonly="!searchable"
          :style="{ width: isValueEmpty ? '100%' : 'auto' }" :id="inputId" aria-label="Search for option"> -->
        <!-- <input v-if="filter" ref="search" v-model="search" :placeholder="placeholder"> -->

        <ul :style="{ 'max-height': maxHeight, 'margin': hideResults || !filter ? '0' : '0 0 0.75rem' }">
          <li v-for="(option, index) in filteredOptions" :key="index">
            <a @mousedown.prevent="select(option)" :style="{ 'color': isColor }" :class="{ selected: isOptionSelected(option) }">
              <slot name="option" v-bind="option">
                {{ getLabel(option) }}
              </slot>
            </a>
          </li>
          <li v-if="!filteredOptions.length" class="no-options">
            <slot name="no-options">{{ noMatching }}</slot>
          </li>
        </ul>
        <aside ref="results" v-if="filter && !hideResults && filteredOptions.length < mutableOptions.length">
          <slot name="total-results">
            <span class="total-results">Showing {{ filteredOptions.length }} from {{ mutableOptions.length }} results</span>
          </slot>
        </aside>
      </section>
    </transition>
  </section>
</template>

<style lang="scss" src="./iSelect.scss" scoped>
</style>

<script>
  export default {
    name: 'i-select',
    data: () => ({
      search: '',
      dropdownOpen: false,
      mutableOptions: [],
      multipleReturn: [],
      height: false
    }),

    /**
     * Clone props into mutable values,
     * attach any event listeners.
     */
    created() {
      this.mutableOptions = this.options.slice(0);
      const initialValueWithReturn =
        this.return && this.mutableOptions.find(o => o[this.return] === this.value);
      this.mutableValue = initialValueWithReturn || this.initial;
      !this.hideLabel && (this.search = this.getLabel(this.mutableValue));
    },

    /**
     * Dropdown List have same top to
     * <input> in i-select heigth
     */
    mounted() {
      const input = window.getComputedStyle(this.$refs.search);
      this.height = input.getPropertyValue('height');
    },

    watch: {
      /**
       * When the value prop changes, update
       * the internal mutableValue.
       * @param  {mixed} val
       * @return {void}
       */
      value(val) {
        this.mutableValue = val;
        if (this.return) {
          const initialValueWithReturn =
            this.return && this.mutableOptions.find(o => o[this.return] === val);
          this.mutableValue = initialValueWithReturn || val;
        }
        // Set Label
        val && this.setLabel(this.mutableValue);
      },

      /**
       * Update options list
       * when the father change
       * the value passed
       * into this property
       * @param  {mixed} val
       * @return {void}
       */
      options(val) {
        this.mutableOptions = val;

        if (this.return) {
          const initialValueWithReturn =
            this.return && val.find(o => o[this.return] === this.value);
          this.mutableValue =
            initialValueWithReturn || this.getLabel(this.value) || this.model;
          !this.hideLabel && (this.search = this.getLabel(this.mutableValue));
        } else {
          if (this.value)
            return console.warn(
              `[i-select warn]: It's impossible identify the value "${
              this.value
            }" without declare a return property.\nSee more: https://www.npmjs.com/package/i-select#return`,
            );
        }
      },

      /**
       * Make initial reactive
       * @param  {mixed} val
       * @return {void}
       */
      initial(val) {
        this.mutableValue = val;
      },
    },
    props: {
      /**
       * Contains the currently selected value. Very similar to a
       * `value` attribute on an <input>.
       * @type {Object||String||null}
       */
      value: {
        default: null,
      },

      /**
       * Initial value showing on `i-select`
       * @type {String}
       */
      initial: {
        type: String,
        default: '',
      },

      /**
       * Equivalent to the `placeholder` attribute on an `<input>`.
       * Used in filter
       * @type {String}
       */
      placeholder: {
        type: String,
        default: '',
      },

      /**
       * Alternative message to show when has no matching on filter
       * @type {String}
       */
      noMatching: {
        type: String,
        default: 'Sorry, no matching options.',
      },

      /**
       * An array of strings or objects to be used as dropdown choices.
       * If you are using an array of objects, vue-select will look for
       * a `label` key (ex. [{label: 'This is Foo', value: 'foo'}]). A
       * custom label key can be set with the `label` prop.
       * @type {Array}
       */
      options: {
        type: Array,
        default () {
          return [];
        },
      },

      /**
       * This select will return multiple results?
       * @type {Boolean}
       */
      multiple: {
        type: Boolean,
        default: false,
      },

      /**
       * Tells what key to use when generating option
       * labels when each `option` is an object.
       * @type {String}
       */
      label: {
        type: String,
        default: 'label',
      },

      /**
       * Return to v-model only a string instead an object.
       * Enter with key name and return this value
       * @type {String}
       */
      return: {
        type: String,
      },

      /**
       * Limit how much results will be avaliable in Dropdown
       * @type {String}
       */
      limit: {
        type: String,
        default: '5',
      },

      /**
       * When True a input search will be avaliable.
       * @type {Boolean}
       */
      filter: {
        type: Boolean,
        default: false,
      },

      /**
       * Disable the entire component.
       * @type {Boolean}
       */
      disabled: {
        type: Boolean,
        default: false,
      },

      /**
       * Convert All Exibed Text to UPPERCASE.
       * @type {Boolean}
       */
      uppercase: {
        type: Boolean,
        default: false,
      },

      /**
       * Hide results from Dropdown
       * when filteredOptions is called
       * @type {Boolean}
       */
      hideResults: {
        type: Boolean,
        default: false,
      },

      /**
       * Hide Label from <input>
       * @type {Boolean}
       */
      hideLabel: {
        type: Boolean,
        default: false,
      },

      /**
       * Sets the max-height property on the dropdown list.
       * @deprecated
       * @type {String}
       */
      maxHeight: {
        type: String,
        default: '200px',
      },

      /**
       * Integration with iComponents, personalize colors and tooltips.
       * Also personalization is avaliable with `i-select` class in CSS
       */
      isBackground: String,
      isOutline: String,
      isColor: String,
      isTooltip: String,
      isPosition: String,
    },
    computed: {
      /**
       * Return Selected value to v-model.
       * @return {Object|String}
       */
      model: {
        get() {
          return this.mutableValue;
        },
        set(v) {
          if (this.return) {
            if (!this.multiple) {
              return this.$emit('input', v[this.return]);
            } else {
              return this.$emit('input', v.map(each => each[this.return]));
            }
          } else {
            this.$emit('input', v);
          }
        },
      },

      /**
       * The currently displayed options, filtered
       * by the search elements value. If tagging
       * true, the search text will be prepended
       * if it doesn't already exist.
       *
       * @return {array}
       */
      filteredOptions() {
        if (!this.filter) return this.mutableOptions.slice(0, +this.limit);

        const search = (this.search || '').toString().toLowerCase();
        const options = this.mutableOptions.filter(option => {
          if (typeof option === 'object') {
            if (option.hasOwnProperty(this.label)) {
              return (
                option[this.label]
                .toString()
                .toLowerCase()
                .indexOf(search) > -1
              );
            } else {
              return console.warn(
                `[i-select warn]: Label key "${
                this.label
              }" does not exist in options object.\nSee more: https://www.npmjs.com/package/i-select#label`,
              );
            }
          }
          return (
            option
            .toString()
            .toLowerCase()
            .indexOf(search) > -1
          );
        });
        return options.slice(0, +this.limit);
      },

      style() {
        return {
          'background-color': this.isBackground,
          'text-transform': this.uppercase ? 'uppercase' : '',
          'border-color': this.isOutline,
          color: this.isColor,
          top: this.height
        };
      },
    },
    methods: {
      /**
       * Toggle the visibility of the dropdown menu.
       * Close dropdown when clicked outside.
       * @param  {Event} e
       * @return {void}
       */
      toggleDropdown(e) {
        let isClickOutside =
          e.target != this.$refs.dropdown &&
          e.target != this.$refs.selected &&
          e.target != this.$refs.results &&
          e.target != this.$refs.toggle &&
          e.target != this.$refs.search &&
          e.target != this.$refs.clear &&
          e.target != this.$refs.icon &&
          e.target != this.$el;

        isClickOutside && this.closeDropdown();
      },

      /**
       * Open Dropdown
       * @param  {Event} e
       * @return {void}
       */
      openDropdown(e) {
        this.filter && (this.search = '');

        this.$refs.search && this.$refs.search.focus();

        this.dropdownOpen = true;
        this.$nextTick(() => {
          this.container = document.querySelectorAll('body')[0];
          if (this.container) {
            // this.$el.addEventListener('mouseleave', this.closeDropdown);
            this.container.addEventListener('click', this.toggleDropdown);
          }
        });
      },

      /**
       * Close Dropdown
       * @param  {Event} e
       * @return {void}
       */
      closeDropdown() {
        this.dropdownOpen = false;
        this.container.removeEventListener('click', this.toggleDropdown, false);
      },

      /**
       * Select a given option.
       * @param  {Object|String} option
       * @return {void}
       */
      select(option, close = true) {
        if (!this.multiple) {
          this.model = option;
        } else {
          const index = this.multipleReturn.findIndex(o => o === option);
          if (index < 0) this.multipleReturn.push(option);
          else this.multipleReturn.splice(index, 1);
          this.model = this.multipleReturn;
        }
        // Set Label
        this.setLabel(option);

        close && this.closeDropdown();
      },

      /**
       * Reutilize function to Set Label
       * @param  {Object || String} val
       */
      setLabel(val) {
        if (this.multiple) {
          !this.hideLabel && (this.search = this.getLabel(this.multipleReturn).join(', '));
        } else {
          !this.hideLabel && (this.search = this.getLabel(val));
        }
      },

      /**
       * Callback to generate the label text. If {option}
       * is an object, returns option[this.label] by default.
       * @type {String}
       * @param  {Object || String} option
       * @return {String}
       */
      getLabel(option) {
        if (!option) return '';

        if (typeof option === 'object' && this.label && !!option[this.label]) {
          return option[this.label];
        } else {
          if (this.multiple) {
            if (option.length)
              return option.map(each => each[this.label])
            else
              return []
          } else {
            const label = this.mutableOptions.find(i => i[this.return] == option);
            console.warn(
              `[i-select warn]: Label key "${
                this.label
              }" does not exist in option selected "${option}" is ${label}\nSee more: https://www.npmjs.com/package/i-select#label`,
            );

            if (label) {
              return label[this.label];
            } else {
              return option;
            }
          }
        }
      },

      isOptionSelected(option) {
        if (!this.multiple) {
          return this.mutableValue === option;
        } else {
          return this.multipleReturn.some(each => each === option);
        }
      },

      gobacktomodel() {
        if (this.multiple) {
          !this.hideLabel && (this.search = this.getLabel(this.multipleReturn).join(', '));
        } else {
          !this.hideLabel && (this.search = this.getLabel(this.mutableValue));
        }
      },

      clearSearch() {
        !this.hideLabel && (this.search = this.initial);
        this.closeDropdown();
        this.$emit('input', undefined);
        this.multiple && (this.multipleReturn = []);
      },
    },
  };
</script>