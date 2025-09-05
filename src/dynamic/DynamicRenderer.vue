
<script lang="ts">
import { defineComponent, h, resolveComponent } from 'vue'
export default defineComponent({
  name: 'DynamicRenderer',
  props: {
    name: {
      type: String,
      required: true, // 必须传入组件名，如 'ComponentA'
    },
  },
  setup(props) {
    // 根据 props.name 解析出组件对象
    const targetComponent = resolveComponent(props.name)

    if (!targetComponent) {
      console.warn(`组件 "${props.name}" 未找到！`)
      return () => h('div', `错误：组件 "${props.name}" 未注册或不存在`)
    }

    // 使用 h() 渲染该组件
    return () => h(targetComponent)
  },
})
</script>