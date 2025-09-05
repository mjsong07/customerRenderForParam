<script>
import { defineComponent} from 'vue'

export default defineComponent({ 
   props: {
    renderContent: {
      type: Object,
      required: true
    }
  },
  setup(props) { 
    return () =>
     props.renderContent({ message: '【这是child的内容】（composition API + return fn 方式）' })
  }
})
</script>