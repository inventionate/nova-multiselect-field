<template>
  <ul class="" v-if="values">
    <li class="" v-for="(value, i) of values" :key="i">
      <Link
        :href="$url(`/resources/${resourceName}/${Array.isArray(field.value) ? field.value[i]: field.value.replace(/[\[\]']+/g,'')}`)"
        class="link-default"
      >
        {{ value }}
      </Link>
    </li>
  </ul>

  <div v-else>—</div>
</template>

<script>
export default {
  props: ['field', 'values', 'resourceName', 'resource'],
};
</script>
