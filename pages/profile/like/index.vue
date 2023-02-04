<template>
    <div>
      {{ userfollow }}
    </div>
</template>

<script>
export default {
  name: 'ProFile',
  layout: 'ProfileLayout',
  middleware: 'auth',
  data() {
    return {
      userfollow: []
    }
  },
  async created() {
    await this.getFollow()
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
    }
  }
}
</script>