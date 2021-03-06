<template>
  <div class="bg-gray-100 min-h-screen">
    <!-- TOPBAR -->
    <div class="fixed z-20 top-0 left-0 w-full h-12 bg-white border-b pl-12 overflow-hidden">
      <slot name="topbar" v-bind:pageTitle="currentPageTitle"></slot>
    </div>
    <!-- END TOPBAR -->

    <!-- MENUBAR -->
    <input type="checkbox" class="hidden peer" id="open-menu-bar">
    <label class="fixed z-20 opacity-0 top-0 left-0 bg-black w-screen h-screen transition-opacity pointer-events-none 
      peer-checked:opacity-60 peer-checked:pointer-events-auto" for="open-menu-bar"></label>
    <div class="fixed z-20 top-0 left-0 w-12 h-screen bg-white border-r overflow-hidden select-none transition-all 
      peer-checked:w-64">

      <!-- EXPAND BUTTON -->
      <label class="block w-full h-12 p-3 leading-6 cursor-pointer border-b" for="open-menu-bar">
        <ion-icon class="text-xl" name="grid-outline"></ion-icon>
      </label>
      <!-- END EXPAND BUTTON -->

      <template v-if="menuItems">
        <component 
          v-for="item in menuItems" 
          :key="item.label" 
          :is="item.type === 'link' ? 'nuxt-link' : 'div'" 
          :class="
            item.type === 'link' ? 
              'flex w-full h-12 p-3 leading-6 m-active:bg-gray-100' : 
              'h-[1px] bg-gray-300 mx-2'"
          :to="item.type === 'link' ? item.href : false"
          :active="item.type === 'link' ? checkActive(item.href) : false"
        >
          <template v-if="item.type === 'link'">
            <span class="pr-4">
              <ion-icon class="text-xl" :name="`${item.icon}-outline`"></ion-icon>
            </span>
            <span class="m-menu-label">{{ item.label }}</span>
          </template>
        </component>
      </template>
    </div>
    <!-- END MENUBAR -->

    <!-- CONTENT -->
    <div class="pl-12 pt-12 
      peer-checked:blur-sm">
      <slot>
        <Nuxt />
      </slot>
    </div>
    <!-- END CONTENT -->
  </div>
</template>

<script>
export default {
  props: ['menuItems'],

  data(){
    return {
      currentTheme: null
    }
  },

  computed: {
    currentPageTitle(){
      for (let item of this.menuItems){
        if (this.checkActive(item.href)){
          return item.label;
        }
      }

      return null;
    }
  },

  methods: {
    setTheme(type){
      this.currentTheme = type;
      localStorage.setItem('theme', this.currentTheme)
      document.body.setAttribute('theme', this.currentTheme);
    },

    checkActive(path){
      if (path === '/'){
        return this.$route.path === path;
      }

      return this.$route.path.indexOf(path) === 0;
    }
  },

  mounted(){
    this.$nextTick(() => {
      this.setTheme(localStorage.getItem('theme') || 'light');
    });
  },
}
</script>