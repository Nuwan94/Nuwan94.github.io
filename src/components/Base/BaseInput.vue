<template>
  <div
    class="form-group"
    :class="[
       {'input-group': hasIcon},
       {'has-danger': error},
       {'focused': focused},
       {'has-label': label || $slots.label},
       {'has-success': valid === true},
       {'has-danger': valid === false}
       ]"
  >
    <slot name="label">
      <label v-if="label" class="form-control-label" :class="labelClasses">
        {{label}}
        <span class="text-danger" v-if="required">*</span>
      </label>
    </slot>

    <div v-if="addonLeftIcon || $slots.addonLeft" class="input-group-prepend">
      <span class="input-group-text">
        <slot name="addonLeft">
          <i :class="addonLeftIcon"></i>
        </slot>
      </span>
    </div>
    <slot v-bind="slotData">
      <input
        :readonly="readonly"
        :value="value"
        v-on="listeners"
        v-bind="$attrs"
        class="form-control no-disabled"
        :class="[
                     {'bg-dark text-white': dark === true},
                     {'is-valid': valid === true},
                     {'is-invalid': valid === false}, inputClasses]"
        aria-describedby="addon-right addon-left"
      />
    </slot>
    <div v-if="addonRightIcon || $slots.addonRight" class="input-group-append">
      <span class="input-group-text">
        <slot name="addonRight">
          <i :class="addonRightIcon"></i>
        </slot>
      </span>
    </div>
    <slot name="infoBlock"></slot>
    <slot name="helpBlock">
      <div
        class="text-danger invalid-feedback"
        style="display: block;"
        :class="{'mt-2': hasIcon}"
        v-if="error"
      >{{ error }}</div>
    </slot>
  </div>
</template>
<script>
export default {
  inheritAttrs: false,
  name: "base-input",
  props: {
    required: {
      type: Boolean,
      description: "Whether input is required (adds an asterix *)"
    },
    readonly: {
      type: Boolean,
      description: "Whether input is readonly",
      default: false
    },
    valid: {
      type: Boolean,
      description: "Whether is valid",
      default: undefined
    },
    label: {
      type: String,
      description: "Input label (text before input)"
    },
    error: {
      type: String,
      description: "Input error (below input)"
    },
    labelClasses: {
      type: String,
      description: "Input label css classes"
    },
    inputClasses: {
      type: String,
      description: "Input css classes"
    },
    value: {
      type: [String, Number],
      description: "Input value"
    },
    addonRightIcon: {
      type: String,
      description: "Addon right icon"
    },
    addonLeftIcon: {
      type: String,
      description: "Addont left icon"
    },
    dark: {
      type: Boolean,
      default: true,
      description: "Dark theme"
    }
  },
  data() {
    return {
      focused: false
    };
  },
  computed: {
    listeners() {
      return {
        ...this.$listeners,
        input: this.updateValue,
        focus: this.onFocus,
        blur: this.onBlur
      };
    },
    slotData() {
      return {
        focused: this.focused,
        ...this.listeners
      };
    },
    hasIcon() {
      const { addonRight, addonLeft } = this.$slots;
      return (
        addonRight !== undefined ||
        addonLeft !== undefined ||
        this.addonRightIcon !== undefined ||
        this.addonLeftIcon !== undefined
      );
    }
  },
  methods: {
    updateValue(evt) {
      let value = evt.target.value;
      this.$emit("input", value);
    },
    onFocus(value) {
      this.focused = true;
      this.$emit("focus", value);
    },
    onBlur(value) {
      this.focused = false;
      this.$emit("blur", value);
    }
  }
};
</script>
<style scoped>
.no-disabled:disabled,
.no-disabled:read-only {
  background: #fff;
  color: #525f7f;
  cursor: default;
}
.has-success::after,
.has-danger::after {
  display: none;
}
.has-danger .form-control,
.has-success .form-control {
  border: 1px solid;
}
.has-danger .form-control {
  border-color: var(--danger);
}
.has-success .form-control {
  border-color: var(--success);
}
.form-control {
  color: #555;
  transition: 0.2s ease-in-out;
}
.form-control:focus {
  color: #222;
}
</style>
