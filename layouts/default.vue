<template>
  <div v-if="user && user.entId">
    <loading :active.sync="isLoading" :can-cancel="false" :is-full-page="true" :height="34"></loading>
    <sidebar/>

    <b-navbar toggleable="md" type="light" fixed="top" class="navbar-light bg-light">
      <sidebar-toggle/>
      <b-container>
        <b-navbar-brand tag="h1" @click="handleClick" class="logo-nav mb-0">UNFAC
          <div class="small-logo">
            <small>Management</small>
            <small>Console</small>
          </div>
        </b-navbar-brand>
        <!-- Right aligned nav items -->
        <b-navbar-nav class="nav-side ml-auto">
          <div class="scrolling-wrapper">
            <b-button
              variant="link"
              class="list"
              @click="()=>{$router.push('/overview'), autoToggle()}"
              v-if="user.entId"
            >หน้าแรก</b-button>
            <b-button
              variant="link"
              class="list"
              color="blue"
              @click="handleClick"
              v-if="user.entId"
            >แผงควบคุม</b-button>
            <b-button
              variant="link"
              class="list"
              @click="()=>{$router.push('/account'), autoToggle()}"
            >บัญชีของฉัน</b-button>
            <b-button
              variant="link"
              class="list"
              @click="()=>{$router.push('/setting/application'), autoToggle()}"
              v-if="user.entId"
            >ตั้งค่าแอปพลิเคชัน</b-button>
          </div>
        </b-navbar-nav>
      </b-container>
    </b-navbar>
    <div class="content-margin-top">
      <!-- <b-breadcrumb :items="items" class="container mb-0 bg-transparent"/> -->
      <nuxt-child/>
    </div>
  </div>
</template>

<script>
import Firebase from "./../configs/firebase.sdk.js";
import Sidebar from "./../components/AppSidebar/sidebar";
import SidebarToggle from "./../components/AppSidebar/sidebarToggle";
export default {
  transition: "bounce",
  components: {
    Sidebar,
    SidebarToggle
  },
  data() {
    return {
      items: [
        {
          text: "เปลี่ยนแผนบริการ",
          to: { name: "overview" }
        },
        {
          text: "ยังไม่เชื่อมต่อ",
          active: true
        }
      ]
    };
  },
  created() {
    this.checkAuth();
  },
  computed: {
    auth() {
      return this.$store.state.auth;
    },
    user() {
      let user = this.$store.state.user;
      if (user && !user.entId) {
        this.$router.push("/join");
      } else {
        return this.$store.state.user;
      }
    },
    isLoading() {
      return this.$store.state.loading;
    },
    open() {
      return this.$store.state.sidebarOpen;
    }
  },
  methods: {
    checkAuth() {
      let self = this;
      Firebase.auth().onAuthStateChanged(function(user) {
        if (user) {
          self.$store.commit("setLoading", false);
        } else {
          self.$store.dispatch("signInWithToken", self.$store.state.token);
          self.$store.commit("setLoading", false);
        }
      });
    },
    handleClick() {
      this.$store.dispatch("toggleSidebar");
    },
    autoToggle() {
      if (this.open) {
        this.$store.dispatch("toggleSidebar");
      }
    }
  }
};
</script>
<style lang="scss" scoped>
.nav-side {
  width: 100%;
  transition: 300ms ease-in-out;
}
.scrolling-wrapper {
  -webkit-overflow-scrolling: touch;
}
.scrolling-wrapper {
  overflow-x: scroll;
  overflow-y: hidden;
  white-space: nowrap;

  .list {
    display: inline-block;
    padding: 1rem;
    padding-bottom: 1.3rem;
    transition: 300ms ease-in-out;
  }
}
</style>

