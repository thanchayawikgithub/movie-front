<script setup lang="ts">
import { useAuthStore } from '@/stores/auth'
import {
  mdiAccountOutline,
  mdiLogout,
  mdiHistory,
  mdiTextBoxSearchOutline,
  mdiAccount
} from '@mdi/js'
import router from '@/router'
import { computed, onMounted, ref } from 'vue'
import { watch } from 'vue'
import type Customer from '@/types/customer'
const authStore = useAuthStore()

const customer = ref<Customer>()

const isMenuVisible = ref(false)

const logout = () => {
  authStore.signOut()
  router.push({ name: 'home' })
}
const checkTicket = () => {
  router.push({ name: 'checkTicket' })
}

const history = () => {
  router.push({ name: 'history' })
}
const items = [
  { icon: mdiHistory, title: 'ประวัติการจอง', action: history },
  { icon: mdiAccountOutline, title: 'ข้อมูลส่วนตัว' },
  {
    icon: mdiTextBoxSearchOutline,
    title: 'ตรวจสอบตั๋ว/รับอาหารเครื่องดื่ม',
    action: checkTicket
  },
  { icon: mdiLogout, title: 'ออกจากระบบ', action: logout }
]

const buttonText = computed(() => {
  if (authStore.isLoggedIn()) {
    return `${customer.value?.cusFirstname} ${customer.value?.cusLastname}`
  } else {
    return 'สมัครสมาชิก/เข้าสู่ระบบ'
  }
})

const buttonClickHandler = () => {
  if (authStore.isLoggedIn()) {
    isMenuVisible.value = true
    // authStore.signOut()
  } else {
    authStore.showLoginDialog = true
  }
}

onMounted(async () => {
  customer.value = await authStore.getCurrentUser()
})
</script>
<template>
  <v-app-bar style="font-family: 'Times New Roman', Times, serif" density="default" :elevation="2"
    ><v-row
      ><v-col cols="4"></v-col
      ><v-col cols="4" align="center">
        <p
          style="
            font-weight: bold;
            font-size: 42px;
            text-align: center;
            color: #b91c1c;
            cursor: pointer;
            display: inline;
          "
          @click="router.push({ name: 'home' })"
        >
          SCALA
        </p></v-col
      ><v-col cols="4">
        <v-menu>
          <template v-slot:activator="{ props }">
            <v-btn
              v-bind="props"
              @click="buttonClickHandler"
              :prepend-icon="mdiAccount"
              rounded="xl"
              style="
                background: linear-gradient(to right, #b91c1c, #ff6640);
                color: white;
                font-family: kanit;
                margin-top: 0.78rem;
              "
            >
              {{ buttonText }}
            </v-btn></template
          >
          <v-list v-if="isMenuVisible">
            <v-list-item
              v-for="(item, index) in items"
              :key="index"
              :value="index"
              @click="item.action"
            >
              <v-list-item-title>
                <v-icon class="mr-2">{{ item.icon }}</v-icon
                >{{ item.title }}</v-list-item-title
              >
            </v-list-item>
          </v-list>
        </v-menu>
      </v-col>
    </v-row>
  </v-app-bar>
</template>
<style scoped></style>
