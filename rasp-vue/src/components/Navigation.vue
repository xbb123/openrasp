<template>
  <div>
    <div class="header py-4">
      <div class="container">
        <div class="d-flex">
          <div class="dropdown">
            <a href="javascript:" class="nav-link pr-0 leading-none" data-toggle="dropdown" style="padding-left: 0">
              <span class="avatar" :style="{ 'background-image': current_app.language ? 'url(/static/images/lang/' + current_app.language + '.png)' : undefined }" />
              <span class="ml-2 d-none d-lg-block">
                <span class="text-muted" style="margin-left: -2px; ">
                  当前应用
                  <i class="fa fa-caret-down" />
                </span>
                <small class="text-default d-block mt-1">
                  {{ current_app.name }}
                </small>
              </span>
            </a>
            <div class="dropdown-menu dropdown-menu-left dropdown-menu-arrow">
              <div class="form-group" style="margin: 6px 15px 10px 15px; ">
                <input type="text" class="form-control form-control-sm" v-model="keyword">
              </div>
              <div style="max-height: 300px; overflow: scroll; ">
                <a v-for="row in app_list_filtered" :key="row.id" class="dropdown-item" href="javascript:" @click.prevent="setCurrentApp(row)">
                  <i class="dropdown-icon fe fe-lock" />
                  {{ row.name }}
                </a>
              </div>
              <div class="dropdown-divider" />
              <router-link :class="{'dropdown-item': true, 'active': false}" :to="{name: 'settings', params: {setting_tab: 'app'}}">
                <i class="dropdown-icon fe fe-settings" />
                应用管理 ({{ app_list.length }})
              </router-link>
            </div>
          </div>
          <div class="d-flex order-lg-2 ml-auto">
            <div class="nav-item d-none d-md-flex">
              <a href="javascript:" class="btn btn-sm btn-outline-primary" @click="showAddHostModal">
                添加主机
              </a>
            </div>
            <div class="dropdown">
              <a href="javascript:" class="nav-link pr-0 leading-none" data-toggle="dropdown">
                <span class="ml-2 d-none d-lg-block">
                  <span class="text-default">
                    openrasp <i class="fa fa-caret-down" />
                  </span>
                  <small class="text-muted d-block mt-1">
                    管理员权限
                  </small>
                </span>
              </a>
              <div class="dropdown-menu dropdown-menu-right dropdown-menu-arrow" x-placement="bottom-end">
                <!-- <a class="dropdown-item" href="javascript:">
                  <i class="dropdown-icon fe fe-settings"></i> 用户设置
                </a>
                <div class="dropdown-divider"></div> -->
                <a class="dropdown-item" href="javascript:" @click="doLogout()">
                  <i class="dropdown-icon fe fe-log-out" /> 退出登录
                </a>
              </div>
            </div>
          </div>
          <a href="#" class="header-toggler d-lg-none ml-3 ml-lg-0" data-toggle="collapse" data-target="#headerMenuCollapse">
            <span class="header-toggler-icon" />
          </a>
        </div>
      </div>
    </div>
    <div id="headerMenuCollapse" class="header collapse d-lg-flex p-0">
      <div class="container">
        <div class="row align-items-center">
          <div class="col-lg order-lg-first">
            <ul v-if="current_app.id" class="nav nav-tabs border-0 flex-column flex-lg-row">
              <li class="nav-item">
                <RouterLink :to="{ name: 'dashboard', params: { app_id: current_app.id } }" class="nav-link">
                  <i class="fe fe-home" />
                  安全总览
                </RouterLink>
              </li>
              <li class="nav-item">
                <RouterLink :to="{ name: 'events', params: { app_id: current_app.id } }" class="nav-link">
                  <i class="fe fe-bell" />
                  攻击事件
                </RouterLink>
              </li>
              <li class="nav-item dropdown">
                <RouterLink :to="{ name: 'baseline', params: { app_id: current_app.id } }" class="nav-link">
                  <i class="fe fe-check-square" />
                  安全基线
                </RouterLink>
              </li>
              <li class="nav-item">
                <RouterLink :to="{ name: 'hosts', params: { app_id: current_app.id } }" class="nav-link">
                  <i class="fe fe-cloud" />
                  主机管理
                </RouterLink>
              </li>
              <li class="nav-item">
                <RouterLink :to="{ name: 'plugins', params: { app_id: current_app.id } }" class="nav-link">
                  <i class="fe fe-zap" />
                  插件管理
                </RouterLink>
              </li>
              <li class="nav-item">
                <RouterLink :to="{ name: 'audit', params: { app_id: current_app.id } }" class="nav-link">
                  <i class="fe fe-user-check" />
                  操作审计
                </RouterLink>
              </li>
              <li class="nav-item dropdown">
                <RouterLink :to="{ name: 'settings', params: { app_id: current_app.id } }" class="nav-link">
                  <i class="fe fe-settings" />
                  系统设置
                </RouterLink>
              </li>
              <li class="nav-item">
                <a href="https://rasp.baidu.com/doc" target="_blank" class="nav-link">
                  <i class="fe fe-file-text" />
                  帮助文档
                </a>
              </li>
              <li class="nav-item">
                <a href="https://rasp.baidu.com/#section-support" target="_blank" class="nav-link">
                  <i class="fa fa-qq" />
                  技术支持
                </a>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>

    <AddHostModal ref="addHost" />
  </div>
</template>
<script>
import AddHostModal from '@/components/modals/addHostModal.vue'
import { mapGetters, mapActions, mapMutations } from 'vuex'
import Cookie from 'js-cookie'

export default {
  name: 'Navigation',
  components: {
    AddHostModal
  },
  data: function() {
    return {
      keyword: ''
    }
  },
  computed: {
    ...mapGetters(['current_app', 'app_list']),
    app_list_filtered: function() {
      var keyword = this.keyword.toLowerCase()
      return this.app_list.filter(function (app) {
        return app.name.toLowerCase().indexOf(keyword) != -1
      })
    }
  },
  watch: {
    current_app(app) {
      this.$router.push({
        name: this.$route.name,
        params: {
          app_id: app.id
        }
      })
    }
  },
  methods: {
    ...mapActions(['loadAppList']),
    ...mapMutations(['setCurrentApp']),
    showAddHostModal() {
      this.$refs.addHost.showModal()
    },
    doLogout() {
      return this.request.post('v1/user/logout', {})
        .then(res => {
          Cookie.set('RASP_AUTH_ID', null)
          location.href = '/#/login'
        })
    }
  }
}
</script>
