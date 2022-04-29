<template>
  <div :class="`text-${field.textAlign}`" v-if="field.belongsToResourceName && field.viewable && field.value">
    <span>
      <span v-if="field.viewable && field.value">
        <Link
          @click.stop
            :href="$url(`/resources/${field.belongsToResourceName}/${Array.isArray(field.value) ? field.value[i]
: field.value.replace(/[\[\]']+/g,'')}`)"
            class="link-default"
        >
          {{ field.value }}
        </Link>
      </span>

      <span v-else>&mdash;</span>
    </span>
  </div>
  <span v-else-if="!field.asHtml">

  <ul class="" v-if="field.value">
    <li class="" v-for="(value, i) of values" :key="i">
      <Link
        :href="$url(`/resources/${field.attribute}/${Array.isArray(field.value) ? field.value[i]: field.value.replace(/[\[\]']+/g,'')}`)"
        class="link-default"
      >
        {{ value }}
      </Link>
    </li>
  </ul>

  </span>
  <span v-else v-html="value"></span>
</template>

<script>
import HandlesFieldValue from '../mixins/HandlesFieldValue';

export default {
  mixins: [HandlesFieldValue],

  props: ['resourceName', 'field'],

  computed: {
    valueNew() {

      console.log(this.field);

      if (this.isMultiselect) {
        const valuesArray = this.getInitialFieldValuesArray();
        if (!valuesArray || !valuesArray.length) return '—';

        const values = valuesArray
          .map(this.getValueFromOptions)
          .filter(Boolean)
          .map(val => `${this.isOptionGroups ? `[${val.group}] ` : ''}${val.label}`);

        const joinedValues = values.join(this.delimiter);

        if (this.valueDisplayLimit >= values.length && this.charDisplayLimit >= joinedValues.length) {
          return joinedValues;
        }

        return this.__('novaMultiselect.nItemsSelected', { count: String(values.length || '') });
      } else {
        const value = this.field.options.find(opt => String(opt.value) === String(this.field.value));
        return (value && value.label) || '—';
      }
    },

    delimiter() {
      return this.field.indexDelimiter || ', ';
    },

    valueDisplayLimit() {
      return this.field.indexValueDisplayLimit || 9999;
    },

    charDisplayLimit() {
      // Set char limit to 9999 if we have value limit, but not char limit
      if (!!this.field.indexValueDisplayLimit && !this.field.indexCharDisplayLimit) return 9999;
      return this.field.indexCharDisplayLimit || 40;
    },
  },
};
</script>
