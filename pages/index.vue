<template>
  <v-row class="index" no-gutters style="height: 100%;">
    <component :is="transition" mode="out-in">
        <conversation-area v-if="conversationActive >= 0" />
        <conversations-list v-else />
    </component>
  </v-row>
</template>

<script>
import {
  VSlideXTransition,
  VSlideYTransition
} from 'vuetify/lib/components/transitions'
import ConversationArea from '@/components/ConversationArea.vue'
import conversationsList from '@/components/ConversationsList.vue'

export default {
  components: { ConversationArea, conversationsList },
  computed: {
    conversationActive () {
      return this.$store.state.conversationActive
    },
    transition () {
      if (this.$vuetify.breakpoint.smAndDown) {
        return VSlideYTransition
      } else {
        return VSlideXTransition
      }
    }
  }
}
</script>
