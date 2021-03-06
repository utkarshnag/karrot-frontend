<template>
  <q-card class="no-shadow grey-border">
    <q-card-main class="generic-padding">
      <div class="row no-wrap">
        <div class="content full-width">
          <div class="row no-wrap items-baseline">
            <strong
              class="q-mr-sm"
              style="white-space: nowrap"
            >
              {{ $d(pickupDate, 'long') }}
            </strong>
            <router-link
              v-if="storeName && storeId"
              :to="{ name: 'store', params: { storeId }}"
              class="ellipsis text-secondary"
            >
              {{ storeName }}
            </router-link>
            <router-link
              v-if="feedback.isEditable"
              class="edit-button q-ml-sm"
              :to="{ name: 'pickupFeedback', params: { feedbackId: feedback.id }}"
            >
              <q-icon
                name="fas fa-pencil-alt"
                :title="$t('BUTTON.EDIT')"
              />
            </router-link>
          </div>
          <small class="text-weight-light">
            <router-link :to="{ name: 'user', params: { userId } }">
              {{ userName }}
            </router-link>
            <span
              class="message-date"
              place="date"
            >
              <DateAsWords :date="createdAt" />
            </span>
          </small>
          <div>
            <AmountBox
              v-if="hasWeight"
              class="on-right float-right amount-box"
              :size="80"
              :amount="weight"
            />
            <div
              v-if="comment"
              class="comment"
            >
              <Markdown :source="comment" />
            </div>
            <div class="q-mt-sm">
              <ProfilePicture
                :user="feedback.givenBy"
                :size="22"
              />
              <span v-if="membersWithoutGiver.length > 0">
                <ProfilePicture
                  v-for="member in membersWithoutGiver"
                  :key="member.id"
                  user="member"
                  :size="15"
                />
              </span>
            </div>
          </div>
        </div>
      </div>
    </q-card-main>
  </q-card>
</template>

<script>
import { QCard, QCardMain, QCardTitle, QTooltip, QIcon } from 'quasar'
import AmountBox from './AmountBox'
import ProfilePicture from '@/components/ProfilePictures/ProfilePicture'
import DateAsWords from '@/components/General/DateAsWords'
import Markdown from '@/components/Markdown'

export default {
  components: {
    QCard, QCardMain, QCardTitle, QTooltip, QIcon, AmountBox, ProfilePicture, DateAsWords, Markdown,
  },
  props: {
    feedback: { required: true, type: Object },
  },
  computed: {
    membersWithoutGiver () {
      const { pickup: { collectors = [] } = {} } = this.feedback
      return collectors.filter((el) => {
        return el.id !== this.feedback.givenBy.id
      })
    },
    weight () {
      return this.feedback && this.feedback.weight
    },
    hasWeight () {
      return Number.isFinite(this.weight)
    },
    comment () {
      return this.feedback && this.feedback.comment
    },
    createdAt () {
      return this.feedback && this.feedback.createdAt
    },
    storeName () {
      const { about: { store: { name } = {} } = {} } = this.feedback
      return name
    },
    storeId () {
      const { about: { store: { id } = {} } = {} } = this.feedback
      return id
    },
    userName () {
      return (this.feedback && this.feedback.givenBy && this.feedback.givenBy.displayName) || '?'
    },
    userId () {
      return this.feedback && this.feedback.givenBy && this.feedback.givenBy.id
    },
    pickupDate () {
      return this.feedback && this.feedback.about && this.feedback.about.date
    },
  },
}
</script>

<style scoped lang="stylus">
@import '~variables'
.comment
  padding-top 8px
  word-wrap break-word
  >>> p:last-child
    margin-bottom 5px
.message-date
  display inline-block
.edit-button
  opacity .7
.q-card:hover .edit-button
  opacity 1
</style>
