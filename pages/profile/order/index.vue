<template>
  <div>order</div>
</template>

<script>
export default {
  name: 'OrderIndex',
  layout: 'ProfileLayout',
  middleware: 'auth',
  data() {
    return {
      userfollow: [],
    }
  },

  methods: {
    async getFollow() {
      try {
        const respo = await this.$axios.get(`/follow/product`)
        this.userfollow = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>