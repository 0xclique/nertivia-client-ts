<template>
  <div class="settings-drawer">
    <div class="items">
      <div
        class="item"
        v-for="(page, index) in pages"
        :key="index"
        :class="{ selected: page.id === currentSettingTab }"
        @click="changeTab(page.id)"
      >
        <div class="material-icons">{{ page.icon }}</div>
        <div class="name">{{ $t(`server-settings.tab-names.${page.id}`) }}</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { MeModule } from "@/store/modules/me";
import { ServerMembersModule } from "@/store/modules/serverMembers";
import { ServersModule } from "@/store/modules/servers";
import { Component, Vue } from "vue-property-decorator";

import settingsPages from "@/utils/serverSettingsPages.json";
import { DrawersModule } from "@/store/modules/drawers";
import { permissions } from "@/constants/rolePermissions";
@Component
export default class MainApp extends Vue {
  changeTab(path: string) {
    DrawersModule.SetLeftDrawer(false);
    this.$router.push({ params: { tab: path } });
  }
  get currentSettingTab() {
    return this.$route.params.tab;
  }
  get serverID() {
    return this.$route.params.server_id;
  }
  get server() {
    return ServersModule.servers[this.serverID];
  }
  get isAdmin() {
    return ServerMembersModule.isAdmin(
      MeModule.user.id || undefined,
      this.serverID
    );
  }
  get isCreator() {
    return this.server?.creator?.id === MeModule.user.id;
  }
  get pages() {
    return Object.values(settingsPages).filter((p: any) => {
      const isAdminOrCreator = this.isCreator || this.isAdmin;
      if (p.owner && !this.isCreator) return false;
      if (p.permission && !isAdminOrCreator) {
        return ServerMembersModule.memberHasPermission(
          MeModule.user.id || "",
          this.serverID,
          permissions[p.permission].value
        );
      }
      if (p.admin && !isAdminOrCreator) return false;
      return true;
    });
  }
}
</script>
<style lang="scss" scoped>
.item {
  display: flex;
  align-items: center;
  align-content: center;
  height: 30px;
  margin: 4px;
  padding-left: 4px;
  cursor: pointer;
  position: relative;
  overflow: auto;
  user-select: none;
  color: white;
  opacity: 0.8;
  border-radius: 4px;
  transition: 0.2s;
  .name {
    margin-left: 4px;
  }
  &:hover {
    background-color: rgba(255, 255, 255, 0.1);
    opacity: 1;
  }
  &.selected {
    background: rgba(255, 255, 255, 0.1);
    opacity: 1;
    &:before {
      content: "";
      position: absolute;
      background: var(--primary-color);
      left: 0;
      top: 0;
      width: 3px;
      bottom: 0;
    }
  }
}
@media (max-width: 950px) {
  .settings-drawer {
    padding-bottom: 56px;
  }
}
</style>
