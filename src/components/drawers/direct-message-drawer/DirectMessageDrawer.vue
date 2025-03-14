<template>
  <div class="dm-drawer">
    <div class="tabs">
      <div
        class="tab"
        @click="changeTab(0)"
        :class="{
          selected: selectedTab === 0,
          notification: friendRequestExists
        }"
      >
        <div class="material-icons">group</div>
        {{ $t("dm-tab.friends") }}
      </div>
      <div
        class="tab"
        @click="changeTab(1)"
        :class="{
          selected: selectedTab === 1,
          notification: dmNotificationExists
        }"
      >
        <div class="material-icons">access_time</div>
        {{ $t("dm-tab.recents") }}
      </div>
    </div>
    <FriendList v-if="selectedTab === 0" />
    <RecentList v-if="selectedTab === 1" />
    <div class="buttons">
      <div
        class="button"
        @click="openSavedNotes"
        :class="{ selected: savedNotesSelected }"
      >
        <div class="icon material-icons">book</div>
        <div class="title">{{ $t("dm-tab.saved-notes") }}</div>
      </div>
      <div class="button" @click="addFriendButton">
        <div class="icon material-icons">person_add</div>
        <div class="title">{{ $t("dm-tab.add-friend") }}</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
const FriendList = () =>
  import(
    /* webpackChunkName: "FriendList" */ "@/components/drawers/direct-message-drawer/FriendList.vue"
  );
const RecentList = () =>
  import(
    /* webpackChunkName: "RecentList" */ "@/components/drawers/direct-message-drawer/RecentList.vue"
  );

import { ChannelsModule } from "@/store/modules/channels";
import { FriendsModule, FriendStatus } from "@/store/modules/friends";
import { MeModule } from "@/store/modules/me";
import { NotificationsModule } from "@/store/modules/notifications";
import { PopoutsModule } from "@/store/modules/popouts";
import { Component, Vue } from "vue-property-decorator";

@Component({ components: { FriendList, RecentList } })
export default class MainApp extends Vue {
  selectedTab = parseInt(localStorage.getItem("selectedDmTab") || "0");

  openSavedNotes() {
    if (!MeModule.user.id) return;
    ChannelsModule.LoadDmChannel(MeModule.user.id);
  }
  addFriendButton() {
    PopoutsModule.ShowPopout({
      component: "add-friend-popout",
      id: "add-friend"
    });
  }
  changeTab(index: number) {
    this.selectedTab = index;
    localStorage.setItem("selectedDmTab", index.toString());
  }
  get friendRequestExists() {
    return this.friends.find(f => f.status === FriendStatus.PENDING);
  }
  get friends() {
    return FriendsModule.friendsWithUser;
  }
  get dmNotificationExists() {
    return NotificationsModule.allDMNotifications.length > 0;
  }
  get savedNotesSelected() {
    const channelID = this.$route.params.channel_id;
    if (!channelID) return false;
    const DMChannel = ChannelsModule.getDMChannel(channelID);
    const recipient = DMChannel?.recipients?.[0];
    return recipient?.id === MeModule.user.id;
  }
}
</script>

<style lang="scss" scoped>
.dm-drawer {
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.buttons {
  display: flex;
  height: 45px;
  background: rgba(255, 255, 255, 0.05);
}
.buttons .button {
  display: flex;
  flex-direction: column;
  flex: 1;
  padding: 3px;
  opacity: 0.7;
  font-size: 14px;
  align-content: center;
  cursor: pointer;
  user-select: none;
  align-items: center;
  transition: 0.2s;
  &:hover {
    background: rgba(255, 255, 255, 0.1);
    opacity: 1;
  }
  &.selected {
    background: var(--primary-color);
    opacity: 1;
  }
}

.tabs {
  display: flex;
  width: 100%;
}
.tab {
  display: flex;
  align-items: center;
  align-content: center;
  justify-content: center;
  height: 30px;
  flex: 1;
  user-select: none;
  cursor: pointer;
  transition: 0.2s;
  opacity: 0.7;
  border-radius: 4px;
  margin: 2px;
  &:hover {
    background: rgba(255, 255, 255, 0.1);
    opacity: 1;
  }
  &.selected {
    background: var(--primary-color);
    opacity: 1;
  }
  &.notification {
    background: var(--alert-color);
  }
  .material-icons {
    margin-right: 5px;
  }
}
@media (max-width: 950px) {
  .dm-drawer {
    padding-bottom: 56px;
  }
}
</style>
