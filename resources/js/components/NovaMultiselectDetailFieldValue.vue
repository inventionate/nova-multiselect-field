<template>
  <div
    class="multiselect-detail-field-value"
    v-if="values"
  >
    <div class="overflow-hidden rounded-lg">
      <div
        class="multiselect-detail-row"
        v-for="(value, i) of values"
        :key="i"
      >
        <Link
          @click.stop
          v-if="hasValue && resource.authorizedToView"
          :href="$url(`/resources/${resourceName}/${resource.id.value}`)"
          class="link-default"
        >
          {{ value }}
        </Link>
      </div>
    </div>
  </div>

  <div v-else>—</div>
</template>

<script>
export default {
  props: ['field', 'values', 'resourceName', 'resource'],
};
</script>
