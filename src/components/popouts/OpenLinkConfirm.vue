<template>
  <div class="popout-background" @click="backgroundClick">
    <div class="generic-popout">
      <div class="content animate-in">
        <div class="header">
          <div class="text">Visit?</div>
        </div>
        <div class="inner-content">
          <div class="description">
            <div>Do you want to visit</div>
            <div class="url">{{ url }}</div>
          </div>
          <div class="buttons">
            <CustomButton
              :name="$t('back')"
              class="no-button"
              @click="close"
              icon="close"
              :alert="true"
            />
            <CustomButton
              name="Visit"
              @click="visitLink"
              icon="launch"
              :filled="true"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import AvatarImage from "@/components/AvatarImage.vue";
import CustomButton from "@/components/CustomButton.vue";
import { PopoutsModule } from "@/store/modules/popouts";
@Component({
  components: { AvatarImage, CustomButton }
})
export default class ProfilePopout extends Vue {
  @Prop() private identity!: string;
  @Prop() private data!: {
    url: string;
  };
  backgroundClick(event: any) {
    if (event.target.classList.contains("popout-background")) {
      this.close();
    }
  }
  visitLink() {
    const win = window.open(this.url, "_blank");
    win?.focus?.();
    this.close();
  }
  close() {
    PopoutsModule.ClosePopout(this.identity);
  }
  get url() {
    if (!this.data.url.startsWith("http")) {
      return "https://" + this.data.url;
    }
    return this.data.url;
  }
}
</script>
<style lang="scss" scoped>
.generic-popout {
  display: flex;
  background: var(--popout-color);
  border-radius: 4px;
  max-width: 300px;
  min-width: 300px;
  max-height: 500px;
  min-height: 200px;
  overflow: hidden;
}
.animate-in {
  opacity: 0;
  animation: showUp 0.2s;
  animation-fill-mode: forwards;
}
@keyframes showUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.popout-background {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.658);
  z-index: 99999999999999999999999999999;
  display: flex;
  align-content: center;
  align-items: center;
  justify-content: center;
  pointer-events: all;
}
.header {
  display: flex;
  align-items: center;
  align-content: center;
  padding: 5px;
  height: 30px;
  flex-shrink: 0;
  background: var(--main-header-bg-color);
  .text {
    margin-left: 10px;
  }
}
.content {
  display: flex;
  flex-direction: column;
  height: 100%;
  width: 100%;
}
.inner-content {
  display: flex;
  flex-direction: column;
  overflow: hidden;
  overflow-y: auto;
  align-items: center;
  height: 100%;
}
.description {
  margin-top: 40px;
  text-align: center;
  line-break: anywhere;
}
.buttons {
  display: flex;
  margin: auto;
  margin-bottom: 10px;
}

.url {
  color: var(--link-color);
  margin-top: 5px;
  margin-bottom: 5px;
  overflow: auto;
  max-height: 200px;
}
.no-button {
  width: 80px;
}
</style>
