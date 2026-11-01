<script setup lang="ts">
import { useAtprotoAccountStore } from '@owdproject/nuxt-module-atproto/runtime/stores/storeAtprotoAccount'
import AtprotoDesktopOwner from '../AtprotoDesktop/AtprotoDesktopOwner.vue'
import { onBeforeMount, watch } from 'vue'
import { computed } from '@vue/reactivity'
import { useI18n } from 'vue-i18n'
import { useAtproto } from '#imports'

const props = defineProps<{
  window: IWindowController
}>()

const atproto = useAtproto()

const atprotoAccountStore = useAtprotoAccountStore()
const { t } = useI18n()

onBeforeMount(() => {
  if (atprotoAccountStore.isAccountLogged) {
    props.window.actions.setTitleOverride(atprotoAccountStore.account.handle)
  }
})

watch(
  () => atprotoAccountStore.account,
  (account) => {
    if (atprotoAccountStore.isAccountLogged) {
      props.window.actions.setTitleOverride(account.handle)
    } else {
      props.window.actions.resetTitleOverride()
    }
  },
)

function onServiceResolverChange() {
  if (atprotoAccountStore.isAccountLogged) {
    // already logged, can't change resolver now
    return
  }

  const customServiceResolver = window.prompt(
    t('atproto.login.resolver.prompt'),
    atprotoAccountStore.handleResolver,
  )

  if (customServiceResolver) {
    atprotoAccountStore.handleResolver = customServiceResolver
  }
}

const accountLabel = computed(() => {
  if (
    (atprotoAccountStore.isAccountLogged &&
      !atprotoAccountStore.account.avatar) ||
    !atprotoAccountStore.isAccountLogged
  ) {
    return '?'
  }

  return undefined
})

const accountAvatar = computed(() => {
  if (
    atprotoAccountStore.isAccountLogged &&
    atprotoAccountStore.account.avatar
  ) {
    return atprotoAccountStore.account.avatar
  }

  return undefined
})

function onSignIn() {
  atproto.signIn()
}

function onSignOut() {
  atproto.signOut()
  atprotoAccountStore.reset()
}
</script>

<template>
  <DesktopWindow :window="window" :content="{ padded: true, centered: true }">
    <div class="flex flex-col h-full">
      <AtprotoDesktopOwner />

      <div class="owd-atproto-login flex-1 text-center">
        <div>
          <div class="owd-atproto-avatar">
            <img
              v-if="accountAvatar"
              :src="accountAvatar"
              alt=""
              class="owd-atproto-avatar__img"
            >
            <span v-else class="owd-atproto-avatar__label" v-text="accountLabel" />
            <div
              v-if="atprotoAccountStore.state.fetching"
              class="owd-atproto-avatar__skeleton"
            />
          </div>

          <button
            v-if="
              !atprotoAccountStore.isAccountLogged &&
              !atprotoAccountStore.state.fetching
            "
            type="button"
            class="owd-atproto-button"
            v-text="$t('atproto.login.actions.login')"
            @click="onSignIn"
          />

          <button
            v-if="atprotoAccountStore.isAccountLogged"
            type="button"
            class="owd-atproto-button"
            v-text="$t('atproto.login.actions.logout')"
            @click="onSignOut"
          />
        </div>
      </div>

      <div class="text-center opacity-35">
        {{ $t('atproto.login.resolver.using') }}
        <span
          :class="{
            'cursor-pointer': !atprotoAccountStore.isAccountLogged,
          }"
          v-text="atprotoAccountStore.handleResolverHostname"
          @click="onServiceResolverChange()"
        />
      </div>
    </div>
  </DesktopWindow>
</template>

<style scoped lang="scss">
.owd-atproto {
  &-login {
    align-content: center;

    .owd-atproto-avatar {
      position: relative;
      width: 120px;
      height: 120px;
      margin: 0 auto 32px;
      border-radius: 50%;
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
      background: rgba(127, 127, 127, 0.15);
      text-transform: uppercase;
      font-size: 32px;
      font-weight: 600;

      &__img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        border-radius: 50%;
      }

      &__skeleton {
        position: absolute;
        inset: 0;
        border-radius: 50%;
        background: rgba(127, 127, 127, 0.25);
        animation: owd-atproto-pulse 1.2s ease-in-out infinite;
      }
    }

    .owd-atproto-button {
      min-width: 140px;
      padding: 10px 18px;
      border-radius: 8px;
      border: 1px solid currentColor;
      font: inherit;
      cursor: pointer;
      background: transparent;
      color: inherit;
      opacity: 0.9;

      &:hover {
        opacity: 1;
      }
    }
  }
}

@keyframes owd-atproto-pulse {
  0%,
  100% {
    opacity: 0.4;
  }
  50% {
    opacity: 0.8;
  }
}
</style>
