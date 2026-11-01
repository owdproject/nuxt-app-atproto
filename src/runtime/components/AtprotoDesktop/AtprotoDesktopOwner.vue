<script setup lang="ts">
import {
  routeAtprotoOS,
  routeBlueskyProfile,
} from '@owdproject/nuxt-module-atproto/runtime/utils/utilRoute'
import { useRuntimeConfig } from 'nuxt/app'

const runtimeConfig = useRuntimeConfig()
</script>

<template>
  <div class="owd-atproto-desktop-owner text-center">
    <a :href="routeAtprotoOS('/desktop')" target="_blank" class="text-2xl mt-4">
      <span
        class="font-light"
        v-text="runtimeConfig.public.desktop.atprotoDesktop.name.title"
      />
      <span
        class="font-bold"
        v-text="runtimeConfig.public.desktop.atprotoDesktop.name.affix"
      />
    </a>
    <div
      class="mb-3 opacity-50"
      v-text="$t('atproto.desktop.instanceBy')"
    />
    <div>
      <div class="inline-flex mt-1">
        <div class="flex items-center pr-2">
          <a
            :href="
              routeBlueskyProfile(
                runtimeConfig.public.desktop.atprotoDesktop.owner.profile?.handle,
              )
            "
            target="_blank"
          >
            <img
              v-if="runtimeConfig.public.desktop.atprotoDesktop.owner.profile?.avatar"
              :src="runtimeConfig.public.desktop.atprotoDesktop.owner.profile.avatar"
              alt=""
              class="owd-atproto-owner-avatar"
            >
          </a>
        </div>
        <div
          class="flex items-center"
          v-text="runtimeConfig.public.desktop.atprotoDesktop.owner.profile.displayName"
        />
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.owd-atproto {
  &-desktop-owner {
    a {
      display: inline-flex;
    }

    .owd-atproto-owner-avatar {
      width: 22px;
      height: 22px;
      border-radius: 50%;
      object-fit: cover;
    }
  }
}
</style>
