<template>
  <f7-button v-bind="config" @click.stop="clicked" @taphold.native="onTaphold($event)" @contextmenu.native="onContextMenu($event)">
    <template v-if="context.component.slots && context.component.slots.default">
      <generic-widget-component :context="childContext(slotComponent)" v-for="(slotComponent, idx) in context.component.slots.default" :key="'default-' + idx" />
    </template>
  </f7-button>
</template>

<script>
import mixin from '../widget-mixin'
import variableMixin from '../variable-mixin'
import { OhButtonDefinition } from '@/assets/definitions/widgets/system'
import { actionsMixin } from '../widget-actions'

export default {
  mixins: [mixin, actionsMixin, variableMixin],
  widget: OhButtonDefinition,
  methods: {
    clicked () {
      if (this.hasAction) {
        this.performAction()
      }
      if (this.config.clearVariable && !this.config.clearVariableKey) {
        if (Array.isArray(this.config.clearVariable)) {
          this.config.clearVariable.forEach((v) => {
            const clearVariableScope = this.getVariableScope(this.context.ctxVars, this.context.varScope, v)
            const clearVariableLocation = (clearVariableScope) ? this.context.ctxVars[clearVariableScope] : this.context.vars
            this.$set(clearVariableLocation, v, undefined)
          })
        } else if (typeof this.config.clearVariable === 'string') {
          const clearVariableScope = this.getVariableScope(this.context.ctxVars, this.context.varScope, this.config.clearVariable)
          const clearVariableLocation = (clearVariableScope) ? this.context.ctxVars[clearVariableScope] : this.context.vars
          this.$set(clearVariableLocation, this.config.clearVariable, undefined)
        }
      }
      if (this.config.clearVariable && this.config.clearVariableKey) {
        let value = this.context.vars[this.config.clearVariable]
        if (Array.isArray(this.config.clearVariableKey)) {
          this.config.clearVariableKey.forEach((key) => {
            const clearVariableScope = this.getVariableScope(this.context.ctxVars, this.context.varScope, this.config.clearVariable)
            const clearVariableLocation = (clearVariableScope) ? this.context.ctxVars[clearVariableScope] : this.context.vars
            value = this.setVariableKeyValues(clearVariableLocation, key, undefined)
          })
        } else if (typeof this.config.clearVariableKey === 'string') {
          const clearVariableScope = this.getVariableScope(this.context.ctxVars, this.context.varScope, this.config.clearVariable)
          const clearVariableLocation = (clearVariableScope) ? this.context.ctxVars[clearVariableScope] : this.context.vars
          value = this.setVariableKeyValues(clearVariableLocation, this.config.clearVariableKey, undefined)
        }
        this.$set(this.context.vars, this.config.clearVariable, value)
      }
    }
  }
}
</script>
