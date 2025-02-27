<template>
  <div :class="emptyClassName">
    <woot-loading-state
      v-if="uiFlags.isFetching || loadingChatList"
      :message="loadingIndicatorMessage"
    />
    <!-- No inboxes attached -->
    <div
      v-if="!inboxesList.length && !uiFlags.isFetching && !loadingChatList"
      class="clearfix"
    >
      <onboarding-view v-if="isAdmin" />
      <div v-else class="current-chat">
        <div>
          <img src="~dashboard/assets/images/inboxes.svg" alt="No Inboxes" />
          <span>
            {{ $t('CONVERSATION.NO_INBOX_AGENT') }}
          </span>
        </div>
      </div>
    </div>
    <!-- Show empty state images if not loading -->
    <div
      v-else-if="!uiFlags.isFetching && !loadingChatList"
      class="current-chat"
    >
      <!-- No conversations available -->
      <div v-if="!allConversations.length">
        <img src="~dashboard/assets/images/chat.svg" alt="No Chat" />
        <span>
          {{ $t('CONVERSATION.NO_MESSAGE_1') }}
          <br />
        </span>
      </div>
      <!-- No conversation selected -->
      <div v-else-if="allConversations.length && !currentChat.id">
        <img src="~dashboard/assets/images/chat.svg" alt="No Chat" />
        <span>{{ $t('CONVERSATION.404') }}</span>
      </div>
    </div>
  </div>
</template>
<script>
import { mapGetters } from 'vuex';
import adminMixin from '../../../mixins/isAdmin';
import accountMixin from '../../../mixins/account';
import OnboardingView from './OnboardingView';

export default {
  components: {
    OnboardingView,
  },
  mixins: [accountMixin, adminMixin],
  computed: {
    ...mapGetters({
      currentChat: 'getSelectedChat',
      allConversations: 'getAllConversations',
      inboxesList: 'inboxes/getInboxes',
      uiFlags: 'inboxes/getUIFlags',
      loadingChatList: 'getChatListLoadingStatus',
    }),
    loadingIndicatorMessage() {
      if (this.uiFlags.isFetching) {
        return this.$t('CONVERSATION.LOADING_INBOXES');
      }
      return this.$t('CONVERSATION.LOADING_CONVERSATIONS');
    },
    newInboxURL() {
      return this.addAccountScoping('settings/inboxes/new');
    },
    emptyClassName() {
      if (
        !this.inboxesList.length &&
        !this.uiFlags.isFetching &&
        !this.loadingChatList &&
        this.isAdmin
      ) {
        return 'inbox-empty-state';
      }
      return 'columns full-height conv-empty-state';
    },
  },
};
</script>
<style scoped>
.inbox-empty-state {
  height: 100%;
  overflow: auto;
}
</style>
