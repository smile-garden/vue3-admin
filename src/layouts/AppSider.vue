<template>
  <a-layout-sider
    :style="{
      position: 'fixed',
      top: 0,
      bottom: 0,
      left: 0,
      height: '100vh',
      overflow: 'auto',
    }"
    :collapsed="collapsed"
    :trigger="null"
    collapsible>
    <div class="sider-logo">
      <div class="sider-logo__inner">
        <SmileFilled
          class="sider-logo__img"
        />
        <span class="sider-logo__text">{{ $t('menu.head') }}</span>
      </div>
    </div>
    <a-menu
      mode="inline"
      theme="dark"
      :selectedKeys="selectedKeys"
      :open-keys="openKeys"
      @openChange="onOpenChange"
      @click="handleClick">
      <template v-for="item in menus">
        <a-sub-menu
          v-if="item.children && item.children.length"
          :key="item.path">
          <template #icon>
            <component :is="item.meta.icon" />
          </template>
          <template #title>{{ $t(`menu.${item.meta.title}`) }}</template>
          <template v-for="subItem in item.children">
            <a-sub-menu
              v-if="subItem.children && subItem.children.length"
              :key="subItem.path">
              <template #title>{{ $t(`menu.${subItem.meta.title}`) }}</template>
              <a-menu-item
                v-for="l3Item in subItem.children"
                :key="l3Item.path">
                {{ $t(`menu.${l3Item.meta.title}`) }}
              </a-menu-item>
            </a-sub-menu>
            <a-menu-item
              v-else
              :key="subItem.path">
              {{ $t(`menu.${subItem.meta.title}`) }}
            </a-menu-item>
          </template>
        </a-sub-menu>
        <a-menu-item
          v-else
          :key="item.path">
          <template #icon>
            <component :is="item.meta.icon" />
          </template>
          {{ $t(`menu.${item.meta.title}`) }}
        </a-menu-item>
      </template>
    </a-menu>
  </a-layout-sider>
</template>

<script setup>
import {
  reactive,
  computed,
  toRefs,
} from 'vue';
import { useRouter, useRoute } from 'vue-router';
import { asyncRoutes } from '@/router/routes/index';
import { SmileFilled } from '@ant-design/icons-vue';

defineProps({
  collapsed: {
    type: Boolean,
    required: true,
  },
});

const state = reactive({
  menus: asyncRoutes[0].children,
  openKeys: [],
});
const rootSubmenuKeys = state.menus.map((menu) => menu.path);
const router = useRouter();
const route = useRoute();
const selectedKeys = computed(() => [route.path]);
const matchPaths = (route.matched || []).map((v) => v.path);
state.openKeys = matchPaths.length > 1
  ? matchPaths.slice(1, matchPaths.length - 1) : matchPaths;

const onOpenChange = (openKeys) => {
  const latestOpenKey = openKeys.find((key) => !state.openKeys.includes(key));
  if (rootSubmenuKeys.includes(latestOpenKey)) {
    state.openKeys = latestOpenKey ? [latestOpenKey] : [];
  } else {
    state.openKeys = openKeys;
  }
};

const handleClick = ({ key }) => {
  if (route.path !== key) {
    router.push(key);
  }
};

const { menus, openKeys } = toRefs(state);
</script>

<!--<script>
import {
  reactive,
  computed,
  toRefs,
} from 'vue';
import { useRouter, useRoute } from 'vue-router';
import { asyncRoutes } from '@/router/routes/index';
import { SmileFilled } from '@ant-design/icons-vue';

export default {
  components: {
    SmileFilled,
  },
  props: {
    collapsed: {
      type: Boolean,
      required: true,
    },
  },
  setup() {
    const state = reactive({
      menus: asyncRoutes[0].children,
      openKeys: [],
    });
    const rootSubmenuKeys = state.menus.map((menu) => menu.path);
    const router = useRouter();
    const route = useRoute();
    const selectedKeys = computed(() => [route.path]);
    const matchPaths = (route.matched || []).map((v) => v.path);
    state.openKeys = matchPaths.length > 1
      ? matchPaths.slice(1, matchPaths.length - 1) : matchPaths;

    const onOpenChange = (openKeys) => {
      const latestOpenKey = openKeys.find((key) => !state.openKeys.includes(key));
      if (rootSubmenuKeys.includes(latestOpenKey)) {
        state.openKeys = latestOpenKey ? [latestOpenKey] : [];
      } else {
        state.openKeys = openKeys;
      }
    };

    const handleClick = ({ key }) => {
      if (route.path !== key) {
        router.push(key);
      }
    };

    return {
      ...toRefs(state),
      selectedKeys,
      onOpenChange,
      handleClick,
    };
  },
};
</script>
-->

<style lang='less' scoped>
.sider {
  &-logo {
    position: relative;
    padding: 12px;
    height: 64px;

    &__inner {
      height: 40px;
      background-color: rgba(255,255,255,.3);
      font-size: 24px;
      line-height: 40px;
      text-align: center;
      color: #fff;
      overflow: hidden;
    }

    &__img {
      margin: 0 6px;
    }

    &__text {
      margin: 0 6px;
      vertical-align: top;
      font-size: 20px;
    }
  }
}
</style>
