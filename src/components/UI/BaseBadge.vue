<template>
  <component
    :is="isRouter"
    class="card m-2 text-dark text-decoration-none"
    :class="[background, orders, colSize]"
    :title="alt"
    :to="to"
  >
    <div class="card-body p-0 d-flex flex-column align-items-center justify-content-around">
      <h5
        class="card-title d-flex justify-content-around w-100"
        :class="unmuted ? '' : 'text-muted'"
      >
        <span v-if="pub" class="text-dark">
          <fa-icon :icon="['fas', 'lock']" />
        </span>
        <span v-if="date">{{ date }}</span>
      </h5>
      <fa-layers v-if="type" full-width class="mb-2" :class="faSizes">
        <fa-icon :icon="type" />
      </fa-layers>
      <div class="text-center w-100" :class="[headers, unmuted ? '' : 'pointed-text']">
        <slot />
      </div>
    </div>
  </component>
</template>

<script>
export default {
  name: 'BaseBadge',
  props: {
    type: { type: [String, Array], required: true },
    date: { type: String, default: '' },
    color: { type: String, default: 'primary' },
    order: { type: String, default: '' },
    alt: { type: String, default: '' },
    size: { type: String, default: '2' },
    faSize: { type: String, default: '' },
    header: { type: String, default: '' },
    to: { type: String, default: '' },
    pub: { type: Boolean, required: false },
    resp: { type: Boolean, required: false },
    resSize: { type: String, default: '' },
    unmuted: { type: Boolean, required: false },
  },
  computed: {
    background() {
      if (this.pub) return 'bg-warning';
      return 'bg-' + this.color;
    },

    // BUG order csak 12ig lehet
    orders() {
      const order = this.order ? 'order-' + this.order : '';
      return order;
    },

    colSize() {
      if (this.resp) return ['col-md-' + this.size, 'col-' + this.resSize];
      return 'col-' + this.size;
    },

    faSizes() {
      if (this.faSize) {
        const size = this.faSize > 10 ? 10 : this.faSize;
        return 'fa-' + size + 'x';
      } else if (this.size) {
        const size = this.size > 10 ? 10 : this.size;
        return 'fa-' + size * 2 + 'x';
      }
      return 'fa-4x';
    },

    headers() {
      return this.header ? 'h' + this.header : '';
    },

    isRouter() {
      return this.to ? 'router-link' : 'div';
    },
  },
};
</script>

<style lang="scss" scoped>
.card {
  cursor: pointer;
}
</style>
