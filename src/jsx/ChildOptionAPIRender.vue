<script lang="ts">
import {defineComponent } from 'vue'

export default defineComponent({
  name: 'HelloRender',
  props: {
    renderContent: {
      type: Object,
      required: true
    }
  },
  render() {
    return this.renderContent({ message: '【这是child的内容】（option API + render 方式）' })
  }
})
</script>