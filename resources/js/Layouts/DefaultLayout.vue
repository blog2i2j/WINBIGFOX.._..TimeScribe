<script lang="ts" setup>
import AppSidebar from '@/Components/AppSidebar.vue'
import { SidebarInset, SidebarProvider } from '@/Components/ui/sidebar'
import BasicLayout from '@/Layouts/BasicLayout.vue'
import { usePage } from '@inertiajs/vue3'
import { useColorMode } from '@vueuse/core'
import { Modal } from 'inertia-modal'
import moment from 'moment/min/moment-with-locales'

moment.locale(usePage().props.js_locale)
if (window.Native) {
    window.Native.on('App\\Events\\LocaleChanged', () => {
        window.location.reload()
    })
}
useColorMode()
</script>

<template>
    <BasicLayout>
        <SidebarProvider>
            <AppSidebar />
            <SidebarInset class="overflow-clip px-8 pt-4 md:peer-data-[variant=inset]:rounded-r-lg">
                <slot />
            </SidebarInset>
            <div class="absolute inset-x-0 top-0 -z-10 h-8" style="-webkit-app-region: drag" />
        </SidebarProvider>
        <Modal />
    </BasicLayout>
</template>
