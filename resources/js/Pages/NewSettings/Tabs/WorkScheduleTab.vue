<script setup lang="ts">
import WorkdayTimeInput from '@/Components/WorkdayTimeInput.vue';
import { SettingProps } from '@/Pages/NewSettings/Index.vue';
import { CalendarClock } from 'lucide-vue-next';
import { computed } from 'vue';

const form = defineModel<SettingProps>();
const weekWorkTime = computed(() => {
    return Object.values(form.value?.workdays ?? {}).reduce(
        (acc, curr) => (isNaN(curr) ? 0 : curr) + acc,
        0,
    );
});
</script>

<template>
    <div class="mt-0 space-y-4" v-if="form">
        <div class="flex items-center space-x-4 rounded-md border p-4">
            <CalendarClock />
            <div class="flex-1 space-y-1">
                <p class="text-sm leading-none font-medium">
                    {{ $t('app.weekly work hours') }}
                </p>
            </div>
            {{ weekWorkTime.toLocaleString($page.props.locale) }}
            {{ $t('app.hours') }}
        </div>
        <div class="flex flex-col gap-2 rounded-md border p-4">
            <WorkdayTimeInput
                :workday="$t('app.monday')"
                v-model="form.workdays.monday"
            />
            <WorkdayTimeInput
                :workday="$t('app.tuesday')"
                v-model="form.workdays.tuesday"
            />
            <WorkdayTimeInput
                :workday="$t('app.wednesday')"
                v-model="form.workdays.wednesday"
            />
            <WorkdayTimeInput
                :workday="$t('app.thursday')"
                v-model="form.workdays.thursday"
            />
            <WorkdayTimeInput
                :workday="$t('app.friday')"
                v-model="form.workdays.friday"
            />
            <WorkdayTimeInput
                :workday="$t('app.saturday')"
                v-model="form.workdays.saturday"
            />
            <WorkdayTimeInput
                :workday="$t('app.sunday')"
                v-model="form.workdays.sunday"
            />
        </div>
    </div>
</template>
