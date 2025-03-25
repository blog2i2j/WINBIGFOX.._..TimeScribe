<script setup lang="ts">
import AppActivitiesTab from '@/Pages/NewSettings/Tabs/AppActivitiesTab.vue';
import AutoStartBreakTab from '@/Pages/NewSettings/Tabs/AutoStartBreakTab.vue';
import GeneralTab from '@/Pages/NewSettings/Tabs/GeneralTab.vue';
import WorkScheduleTab from '@/Pages/NewSettings/Tabs/WorkScheduleTab.vue';
import { useForm } from '@inertiajs/vue3';
import { useDebounceFn } from '@vueuse/core';
import {
    AppWindowMac,
    CalendarClock,
    Cog,
    MonitorPause,
} from 'lucide-vue-next';
import { ref, watch } from 'vue';

export interface SettingProps {
    openAtLogin: boolean;
    showTimerOnUnlock: boolean;
    workdays: {
        monday: number;
        tuesday: number;
        wednesday: number;
        thursday: number;
        friday: number;
        saturday: number;
        sunday: number;
    };
    holidayRegion: string;
    stopBreakAutomatic: string;
    stopBreakAutomaticActivationTime: string;
    stopWorkTimeReset: number;
    stopBreakTimeReset: number;
    locale: string;
    appActivityTracking: boolean;
}

const props = defineProps<{
    openAtLogin?: boolean;
    showTimerOnUnlock?: boolean;
    workdays: {
        monday?: number;
        tuesday?: number;
        wednesday?: number;
        thursday?: number;
        friday?: number;
        saturday?: number;
        sunday?: number;
    };
    holidayRegion?: string;
    stopBreakAutomatic?: string;
    stopBreakAutomaticActivationTime?: string;
    stopWorkTimeReset?: number;
    stopBreakTimeReset?: number;
    locale: string;
    appActivityTracking?: boolean;
}>();

const form = useForm({
    openAtLogin: props.openAtLogin ?? false,
    showTimerOnUnlock: props.showTimerOnUnlock ?? false,
    workdays: {
        monday: props.workdays?.monday ?? 0,
        tuesday: props.workdays?.tuesday ?? 0,
        wednesday: props.workdays?.wednesday ?? 0,
        thursday: props.workdays?.thursday ?? 0,
        friday: props.workdays?.friday ?? 0,
        saturday: props.workdays?.saturday ?? 0,
        sunday: props.workdays?.sunday ?? 0,
    },
    holidayRegion: props.holidayRegion ?? '',
    stopBreakAutomatic: props.stopBreakAutomatic ?? '',
    stopBreakAutomaticActivationTime:
        props.stopBreakAutomaticActivationTime ?? '',
    stopWorkTimeReset: props.stopWorkTimeReset?.toString() ?? '0',
    stopBreakTimeReset: props.stopBreakTimeReset?.toString() ?? '0',
    locale: props.locale,
    appActivityTracking: props.appActivityTracking ?? false,
});

const submit = () => {
    form.patch(route('settings.new-update'), {
        preserveScroll: true,
        preserveState: true,
    });
};

const debouncedSubmit = useDebounceFn(submit, 500);
watch(
    () => [
        form.workdays,
        form.locale,
        form.stopBreakTimeReset,
        form.stopBreakAutomatic,
        form.stopBreakAutomaticActivationTime,
        form.stopWorkTimeReset,
        form.openAtLogin,
        form.showTimerOnUnlock,
        form.holidayRegion,
        form.appActivityTracking,
    ],
    debouncedSubmit,
    { deep: true },
);

const navItems = [
    {
        icon: Cog,
        text: 'app.general',
        component: GeneralTab,
    },
    {
        icon: CalendarClock,
        text: 'app.work schedule',
        component: WorkScheduleTab,
    },
    {
        icon: MonitorPause,
        text: 'app.auto start/break',
        component: AutoStartBreakTab,
    },
    {
        icon: AppWindowMac,
        text: 'app.app activities',
        component: AppActivitiesTab,
    },
];

const selectedItem = ref(0);
</script>

<template>
    <div class="flex h-full select-none">
        <div class="flex min-w-60 shrink-0 flex-col border-r dark:border-black">
            <div class="h-12" style="-webkit-app-region: drag"></div>
            <div class="flex flex-col p-2">
                <div
                    v-for="(item, index) in navItems"
                    :key="index"
                    :data-state="
                        selectedItem === index ? 'selected' : 'unselected'
                    "
                    @click="selectedItem = index"
                    class="data-[state=selected]:text-primary-foreground dark:data-[state=selected]:text-foreground text-foreground/80 data-[state=selected]:bg-primary data-[state=unselected]:[&_svg]:text-primary flex items-center gap-2 rounded p-1.5 text-sm leading-none"
                >
                    <component :is="item.icon" class="size-4" />
                    {{ $t(item.text) }}
                </div>
            </div>
        </div>
        <div
            class="bg-background flex w-full flex-col overflow-auto shadow  dark:shadow-neutral-900"
        >
            <div
                class="sticky inset-x-0 top-0 flex h-12 shrink-0 items-center px-5 font-semibold backdrop-blur-[100px] backdrop-brightness-110"
                style="-webkit-app-region: drag"
            >
                {{ $t(navItems[selectedItem].text) }}
            </div>

            <div class="grow px-4 pt-2 pb-4">
                <Component
                    :is="navItems[selectedItem].component"
                    v-model="form"
                />
            </div>
        </div>
    </div>
</template>

<style>
html,
body {
    background: transparent;
}
html.dark {
    --background: 222.2 0% 15.5%;
    --border: 222.2 0% 25%;
    --input: 217.2 0% 25%;
    --popover: 222.2 0% 10%;
}
</style>
