<template>
  <div>
    <div class="announcements flex-column">
      <div class="body flex-column">
        <div class="title">
          {{ title }}
        </div>
        <div class="content carousel flex-column">
          <announcement-tab
            v-for="ann in announcements"
            :key="ann._id"
            :announcement="ann"
            :scrolledTo="query.id === ann._id"
          />
          <div
            v-if="moreAnnouncements"
            class="expand"
            @click="more"
          >
            {{ $t('announcement.more_announcements') }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import fetchJson from '../../core/api/fetch-json'
import AnnouncementModal from './announcementModal'
import AnnouncementTab from './announcementTab'

import { mapActions, mapGetters } from 'vuex'
export default {
  name: 'AnnouncementView',
  computed: {
    ...mapGetters('announcements', [
      'announcements',
      'unread',
      'moreAnnouncements'
    ]),
    title () {
      if (this.announcements.length === 1 && this.unread) {
        return $.i18n.t('announcement.x_announcement_with_unread', { x: this.announcements.length, y: this.unread })
      } else if (this.unread) {
        return $.i18n.t('announcement.x_announcements_with_unread', { x: this.announcements.length, y: this.unread })
      } else if (this.announcements.length === 1) {
        return $.i18n.t('announcement.x_announcement', { x: this.announcements.length })
      } else {
        return $.i18n.t('announcement.x_announcements', { x: this.announcements.length })
      }
    },
    query () {
      return this.$route.query
    }
  },
  mounted () {
    if (this.query.skip) {
      this.more()
    } else if (!this.announcements.length) {
      this.getAnnouncements()
    }
  },
  data () {
    return {
      lastFetch: false
    }
  },
  methods: {
    ...mapActions('announcements', [
      'openAnnouncementModal',
      'getAnnouncements',
      'readAnnouncement'
    ]),
    readAll () {
      // todo: do we need this?
      this.announcements.forEach(a => {
        if (!a.read) {
          this.readAnnouncement(a._id)
        }
      })
    },
    more () {
      let skip = this.announcements.length
      let options = {
        append: true,
        skip: skip
      }
      this.getAnnouncements(options)
    }
  },
  components: {
    AnnouncementModal,
    AnnouncementTab,
  }
}
</script>

<style scoped lang="scss">
.flex-column {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.announcements {
  background: linear-gradient(262.39deg, #D7EFF2 -1.56%, #FDFFFF 95.05%)
}

.body {
  position: relative;
  width: 1024px;
  width: min(1200px, 80vw);
  margin: 150px;

  &:before {
    content: '';
    border-top: 4px solid #6ae8e3;
    border-left: 4px solid #6ae8e3;
    border-bottom: 4px solid #6ae8e3;
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 20%;
    border-radius: 40px 0 0 40px;
    pointer-events: none;
  }

  &:after {
    content: '';
    border-top: 4px solid #6ae8e3;
    border-right: 4px solid #6ae8e3;
    border-bottom: 4px solid #6ae8e3;

    position: absolute;
    top: 0;
    right: 0;
    height: 100%;
    width: 20%;

    border-radius: 0 40px 40px 0;

    pointer-events: none;
  }
  /* background-image: url(/images/pages/base/modal_background.png); */
  /* background-size: 1024px 100%; */
}

.title {
  color: #0E4C60;
  font-size: 40px;
  font-family: bold;
  transform: translateY(-50%);
}
.content {
  width: 100%;
}
.expand {
  cursor: pointer;
}
</style>
