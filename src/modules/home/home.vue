<style lang="scss">
@import "./home.scss";
</style>
<template>
  <div
    class="layout"
    v-bind:class="{'layout-header-hide': hide('top') , 'layout-hide-menu' :hide('left') }"
  >
    <b-header v-if="!hide('top')">
      <template slot="right">
        <ul class="topbar-menu">
          <li>
            <Tooltip content="首页" placement="bottom">
              <router-link :to="{name:'default'}">
                <Button type="text" icon="ios-home"></Button>
              </router-link>
            </Tooltip>
          </li>
        </ul>
      </template>
    </b-header>
    <Layout :class="'ivu-layout-has-sider'" class="layout-content">
      <sip-sidebar v-if="!hide('left')" :menus="menu"></sip-sidebar>
      <Content>
        <b-router-tab></b-router-tab>
        <div class="bvue-page-tabcontent">
          <keep-alive :include="keepAlives">
            <router-view></router-view>
          </keep-alive>
        </div>
      </Content>
    </Layout>
    <sip-contextmenu ref="contextMenu"></sip-contextmenu>
  </div>
</template>
<script>
import { menuService } from "mvue-components";
import mvueCore from "mvue-toolkit";
import sipSidebar from "@libs/sip/components/sidebar/sip-sidebar.component.vue";
import SipContextmenu from "@libs/sip/components/contextmenu/sip-contextmenu.component.vue";
// import asyncLoadComp from "@libs/sip/components/asyncLoadComp.vue"
import { SipHookModelRouter } from "@libs/sip/components";
export default {
  components: {
    "sip-sidebar": sipSidebar,
    "sip-contextmenu": SipContextmenu
  },
  data: function() {
    return {
      /**缓存母页（这里指有子页面，即由此页打开子页面） */
      keepAlives: [],
      menu: [
        {
          title: "demo",
          id: "test-2222",
          children: [
            {
              title: "test-http",
              id: "test-http11111",
              url: "#/sip/test/test-http"
            },
            {
              title: "test-router1",
              id: "test-router-11111",
              url: "#/sip/test/test-router"
            },
            {
              title: "test-modal",
              id: "test-modal-11111",
              url: "#/sip/test/test-modal"
            },
            {
              title: "test-demo-list",
              id: "test-demo-list",
              url: "#/sip/UIDemo/list"
            }
          ]
        },
        {
          title: "云服务",
          id: "servcie-main",
          children: [
            {
              title: "存储",
              id: "volume",
              // url: "#/sip/test/test-http",
              children: [
                {
                  title: "存储",
                  id: "volumn-list",
                  url: "#/sip/iaas/volume/volume-list"
                }
              ]
            }
          ]
        }
      ]
    };
  },
  render(h) {
    console.log("render111 ");
    return h("div", {});
  },
  computed: {
    // keepAlive: function() {
    //   return this.$route.meta.keepAlive;
    // }
  },
  beforeRouteEnter(to, from, next) {
    // this.post = null
    // console.log("beforeRouteEnter", to, to.matched);
    SipHookModelRouter(to, from, next);
  },
  created: function() {
    /**sip 使用 */
    this.$router.beforeEach((to, from, next) => {
      SipHookModelRouter(to, from, next);
    });
    this.$router.afterEach(route => {
    });
    this.$root.$sipHome = this;
    /**End sip 使用 */
  },
  mounted: function() {},
  methods: {
    setKeepAlives: function(name) {
      var keepAlives = this.keepAlives;
      var index = keepAlives.indexOf(name);
      if (index >= 0) {
        this.keepAlives = keepAlives.slice(0, index + 1);
      } else {
        this.keepAlives = [name];
      }
    },
    sipOpen(vueName, path, query, params) {
      return new Promise(resolve => {
        this.setKeepAlives(vueName);
        setTimeout(() => {
          this.$router.push(
            { path: path, params: params, query: query },
            route => resolve(route),
            route => resolve(route)
          );
        }, 1);
      });
    },
    sipShowContextMenu(e, items) {
      let contextMenu = this.$refs.contextMenu;
      return contextMenu.show(e, items);
    },
    hide(type) {
      var types = this.$route.query._hide;
      if (!types) {
        return false;
      }
      types = types.split(",");
      return _.includes(types, type);
    }
  }
};
</script>
