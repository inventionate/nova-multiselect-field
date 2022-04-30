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

    <span v-for="(value, i) of field.value" :key="i">
      <Link
        @click.stop
        :href="$url(`/resources/${resourceUri}/${value}`)"
        class="link-default"
      >
        {{value}}
        {{i}}
        {{field.options[Number(value)]['label']}}

      <!--{{ field.options[i]['label'] }}<span v-if="i+2 != field.value.length">, </span>-->
      </Link>
    </span>

  </span>
  <span v-else v-html="value"></span>
</template>

<script>
import HandlesFieldValue from '../mixins/HandlesFieldValue';

export default {
  mixins: [HandlesFieldValue],

  props: ['resource', 'resourceName', 'field'],

  computed: {
    resourceUri() {
      console.log(this.field);
      // console.log(this.resource);
      var result = this.field.attribute.replace( /([A-Z])/g, " $1" );
      return result.split(' ').join('-').toLowerCase();
    },
    valueNew() {
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
