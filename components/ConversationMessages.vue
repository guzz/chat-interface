<template>
    <v-row class="no-gutters">
        <v-col
            cols="auto"
            style="max-width: 75%;" 
            :class="message.self ? 'ml-auto' : 'mr-auto'"
            class="conversation-area__card"
        >
            <v-card
                class="conversation-area__baloon pa-4 pb-2"
                :class="[
                    { 'conversation-area__baloon--self': message.self === true },
                    { 'conversation-area__baloon--other': message.self === false },
                    { 'conversation-area__baloon--first':  messagePosition === 'first' },
                    { 'conversation-area__baloon--middle': messagePosition === 'middle' },
                    { 'conversation-area__baloon--last': messagePosition === 'last' },
                    { 'conversation-area__baloon--single': messagePosition === 'single' },
                ]"
            >
                {{message.content}}
                <v-card-actions class="pa-0">
                <span class="conversation-area__time-label" :class="message.self ? 'text-right' : ''">
                    {{message.time}}
                </span>
                </v-card-actions>
            </v-card>
            <template v-if="message.actions && index === 0">
                <v-btn
                    v-for="(action, a) in message.actions.filter((a, i) => i < 4)"
                    :key="a"
                    block
                    class="conversation-area__user-action my-1"
                    :class="[
                        { 'conversation-area__baloon--self': message.self === true },
                        { 'conversation-area__baloon--other': message.self === false }
                    ]"
                    @click="sendMessage(action)"
                >
                    {{action}}
                </v-btn>
            </template>
        </v-col>
    </v-row>
</template>

<script>
export default {
    props: {
        messages: {
            type: Array,
            default: () => []
        },
        message: {
            type: Object,
            default: () => {}
        },
        index: {
            type: Number,
            default: -1
        }
    },
    computed: {
        messagePosition () {
            const currentMessage = this.message
            const previousMessage = this.messages[this.index - 1]
            const nextMessage = this.messages[this.index + 1]

            if (!previousMessage && !nextMessage) return 'single'

            if (!previousMessage) {
                const sameAsNext = currentMessage.self === nextMessage.self
                if (sameAsNext) {
                    return 'last'
                } else {
                    return 'single'
                }
            } 

            if (!nextMessage) {
                const sameAsPrevious = currentMessage.self === previousMessage.self
                if (sameAsPrevious) {
                    return 'first'
                } else {
                    return 'single'
                }
            }

            const sameAsPrevious = currentMessage.self === previousMessage.self
            const sameAsNext = currentMessage.self === nextMessage.self
            
            if (sameAsPrevious && sameAsNext) {
                return 'middle' 
            } else if (sameAsPrevious && !sameAsNext) {
                return 'first'
            } else if (!sameAsPrevious && sameAsNext) {
                return 'last'
            } else {
                return 'single'
            }
        }
    },
    methods: {
        sendMessage (actionText) {
            class Message {
                constructor(content) {
                this.content = content
                this.self = true
                const today = new Date();
                const hour = today.getHours() + ":" + (today.getMinutes() < 10 ? '0' : '') + today.getMinutes();
                this.time = hour 
                }
            }

            const message = new Message(actionText)
            this.$store.dispatch('sendMessage', message)
        }
    }
}
</script>

<style lang="sass">
.conversation-area__baloon
    border-radius: 20px!important  
.conversation-area__baloon--self
    background-color: var(--v-primary-base)!important
    color: #FFFFFF!important
    &.conversation-area__baloon--single
        border-bottom-right-radius: 0!important
        margin: 8px 0
    &.conversation-area__baloon--last
        border-top-right-radius: 0!important
        border-bottom-right-radius: 0!important
        margin: 1px 0 8px
    &.conversation-area__baloon--first
        border-bottom-right-radius: 0!important
        margin: 8px 0 1px
    &.conversation-area__baloon--middle
        border-bottom-right-radius: 0!important
        border-top-right-radius: 0!important
        margin: 1px 0
.conversation-area__baloon--other
    background-color: var(--v-secondary-lighten2)!important
    color: #FFFFFF!important
    &.conversation-area__baloon--single
        border-bottom-left-radius: 0!important
        margin: 8px 0
    &.conversation-area__baloon--last
        border-top-left-radius: 0!important
        border-bottom-left-radius: 0!important
        margin: 1px 0 8px
    &.conversation-area__baloon--first
            border-bottom-left-radius: 0!important
            margin: 8px 0 1px
    &.conversation-area__baloon--middle
            border-bottom-left-radius: 0!important
            border-top-left-radius: 0!important
            margin: 1px 0
.conversation-area__time-label
    width: 100%
    font-weight: 300
    font-style: italic
    font-size: 0.75rem
.conversation-area__user-action
    padding: 12px
    font-size: 0.9rem
    text-transform: uppercase
    font-weight: 500
    background-color: #FFFFFF!important
    &.conversation-area__baloon--other
        color: var(--v-secondary-lighten2)!important
        border: solid 1px var(--v-secondary-lighten2)
    &.conversation-area__baloon--self
        color: var(--v-primary-base)!important
        border: solid 1px var(--v-primary-base)

</style>