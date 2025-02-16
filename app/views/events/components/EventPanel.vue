<template>
  <side-panel
    :is-visible="isVisible"
    :block-background="false"
    id="event-panel"
    @close-panel="close"
  >
    <template #header>
      <ul class="event-panel-tabs nav nav-tabs">
        <li class="tab"
            :class="{active: panelType === t}"
            v-for="t in possibleTabs"
            :key="t"
            @click="changeTab(t)"
        >
          <a href="#">{{ t }}</a>
        </li>
      </ul>
    </template>

    <template #body>
      <div class="event-panel-body">
        <edit-event v-if="['new', 'edit'].includes(panelType)" :editType="panelType" @save="onEventSave" />
        <edit-members v-if="panelType === 'members'" @save="onEventSave" />
        <edit-instance v-if="panelType === 'instance'" @save="onEventSave" />
      </div>
    </template>
  </side-panel>
</template>

<script>
import { mapGetters, mapMutations, mapActions } from 'vuex'
import SidePanel from '../../../components/common/SidePanel'
import EditEvent from './EditEventComponent'
import EditMembers from './EditMembersComponent'
import EditInstance from './EditInstanceComponent'

export default {
  name: 'EventPanel',
  components: {
    SidePanel,
    EditEvent,
    EditMembers,
    EditInstance
  },
  data () {
    return {
    }
  },
  computed: {
    ...mapGetters({
      isVisible: 'events/eventPanelVisible',
      panelType: 'events/eventPanelType'
    }),
    tabOptions () {
      return [
        'new',
        'edit',
        'members',
        'instance'
      ]
    },
    possibleTabs () {
      if (me.isAdmin()) {
        if (this.panelType === 'new') {
          return ['new']
        } else {
          return ['instance', 'edit', 'members']
        }
      }
    },
    title () {
      return {
        info: 'Event Info', // maybe we don't need it
        new: 'Create Event',
        edit: 'Edit Event'
      }[this.panelType]
    }
  },
  beforeMount () {
    window.addEventListener('keyup', this.onEscapeKeyUp)
  },
  beforeDestroy () {
    window.removeEventListener('keyup', this.onEscapeKeyUp)
  },
  methods: {
    ...mapMutations({
      close: 'events/closeEventPanel',
      changeEventTab: 'events/changeEventPanelTab'
    }),
    ...mapActions({
      refreshEvent: 'events/fetchEvent'
    }),
    changeTab (t) {
      this.changeEventTab(t)
    },
    onEventSave (id) {
      this.refreshEvent(id).then(() => {
        this.close()
      })
    },
    onEscapeKeyUp (event) {
      if (event.which === 27) {
        this.close()
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.event-panel-body {
  padding: 10px;
}
.event-panel-tabs {
  display: inline-block;
}
</style>
