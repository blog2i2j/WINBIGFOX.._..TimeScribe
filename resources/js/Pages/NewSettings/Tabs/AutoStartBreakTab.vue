<script setup lang="ts">
import {
    Select,
    SelectContent,
    SelectItem,
    SelectTrigger,
    SelectValue,
} from '@/Components/ui/select';
import { Switch } from '@/Components/ui/switch';
import { SettingProps } from '@/Pages/NewSettings/Index.vue';
import { AlarmClockCheck, LockKeyhole, TimerReset } from 'lucide-vue-next';
import moment from 'moment/min/moment-with-locales';
import { ref, watch } from 'vue';

const form = defineModel<SettingProps>();

const stopBreakAutomatikCheck = ref(form.value?.stopBreakAutomatic !== '');
const stopBreakAutomatikActivationCheck = ref(
    form.value?.stopBreakAutomaticActivationTime !== '',
);
const stopTimeResetCheck = ref(
    !!(form.value?.stopWorkTimeReset || form.value?.stopBreakTimeReset),
);

watch(stopBreakAutomatikCheck, () => {
    if (stopBreakAutomatikCheck.value === false && form.value) {
        form.value.stopBreakAutomatic = '';
        stopBreakAutomatikActivationCheck.value = false && form.value;
    }
});
watch(stopBreakAutomatikActivationCheck, () => {
    if (stopBreakAutomatikActivationCheck.value === false && form.value) {
        form.value.stopBreakAutomaticActivationTime = '';
    }
});
watch(stopTimeResetCheck, () => {
    if (stopTimeResetCheck.value === false && form.value) {
        form.value.stopWorkTimeReset = 0;
        form.value.stopBreakTimeReset = 0;
    }
});
</script>

<template>
    <div class="mt-0 space-y-4">
        <div class="rounded-md border">
            <div class="flex items-start space-x-4 p-4">
                <LockKeyhole />
                <div class="flex-1 space-y-1">
                    <p class="text-sm leading-none font-medium">
                        {{ $t('app.auto start/break') }}
                    </p>
                    <p class="text-muted-foreground text-sm">
                        {{
                            $t(
                                'app.when the computer is locked, the working time can be automatically stopped, or the break can be started.',
                            )
                        }}
                    </p>
                    <div class="mt-4" v-if="stopBreakAutomatikCheck">
                        <Select v-model="form.stopBreakAutomatic">
                            <SelectTrigger>
                                <SelectValue placeholder="Aktion" />
                            </SelectTrigger>
                            <SelectContent>
                                <SelectItem value="stop">
                                    {{ $t('app.stop working time') }}
                                </SelectItem>
                                <SelectItem value="break">
                                    {{ $t('app.start break') }}
                                </SelectItem>
                            </SelectContent>
                        </Select>
                    </div>
                </div>
                <Switch v-model:checked="stopBreakAutomatikCheck" />
            </div>
            <div
                class="flex items-start space-x-4 border-t p-4"
                v-if="stopBreakAutomatikCheck"
            >
                <AlarmClockCheck />
                <div class="flex-1 space-y-1">
                    <p class="text-sm leading-none font-medium">
                        {{ $t('app.activate based on time') }}
                    </p>
                    <p class="text-muted-foreground text-sm">
                        {{
                            $t(
                                'app.the auto start/break feature will only be activated at a specified time.',
                            )
                        }}
                    </p>
                    <div class="mt-4" v-if="stopBreakAutomatikActivationCheck">
                        <Select v-model="form.stopBreakAutomaticActivationTime">
                            <SelectTrigger>
                                <SelectValue :placeholder="$t('app.time')" />
                            </SelectTrigger>
                            <SelectContent>
                                <SelectItem
                                    :key="hour"
                                    :value="`${hour + 12}`"
                                    v-for="hour in 11"
                                >
                                    {{
                                        $t('app.from :time', {
                                            time: moment(
                                                hour + 12,
                                                'HH',
                                            ).format('LT'),
                                        })
                                    }}
                                </SelectItem>
                            </SelectContent>
                        </Select>
                    </div>
                    <p
                        class="text-muted-foreground text-xs italic"
                        v-if="
                            stopBreakAutomatikActivationCheck &&
                            form.stopBreakAutomaticActivationTime
                        "
                    >
                        {{
                            $t(
                                'app.the automatic system is active until :time on the following day.',
                                {
                                    time: moment(5, 'H').format('LT'),
                                },
                            )
                        }}
                    </p>
                </div>
                <Switch v-model:checked="stopBreakAutomatikActivationCheck" />
            </div>
        </div>
        <div class="rounded-md border">
            <div class="flex items-start space-x-4 p-4">
                <TimerReset />
                <div class="flex-1 space-y-1">
                    <p class="text-sm leading-none font-medium">
                        {{ $t('app.forgotten stop') }}
                    </p>
                    <p class="text-muted-foreground text-sm">
                        {{
                            $t(
                                'app.if you forget to stop the working or break time, it will be automatically stopped retroactively.',
                            )
                        }}
                    </p>
                    <p class="text-muted-foreground my-2 text-xs italic">
                        {{
                            $t(
                                'app.if an absence exceeds the configured time, the working or break time will be stopped retroactively.',
                            )
                        }}
                    </p>
                    <div class="mt-4" v-if="stopTimeResetCheck">
                        <p class="mb-2 text-sm leading-none">
                            {{ $t('app.stop working time after:') }}
                        </p>
                        <Select v-model="form.stopWorkTimeReset">
                            <SelectTrigger>
                                <SelectValue placeholder="Zeit" />
                            </SelectTrigger>
                            <SelectContent>
                                <SelectItem value="0">
                                    {{ $t('app.never') }}
                                </SelectItem>
                                <SelectItem value="5">
                                    5
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="10">
                                    10
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="20">
                                    20
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="30">
                                    30
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="40">
                                    40
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="50">
                                    50
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="60">
                                    1:00
                                    {{ $t('app.hour') }}
                                </SelectItem>
                                <SelectItem value="90">
                                    1:30
                                    {{ $t('app.hour') }}
                                </SelectItem>
                                <SelectItem value="120">
                                    2:00
                                    {{ $t('app.hours') }}
                                </SelectItem>
                                <SelectItem value="150">
                                    2:30
                                    {{ $t('app.hours') }}
                                </SelectItem>
                            </SelectContent>
                        </Select>
                    </div>
                    <div class="mt-4" v-if="stopTimeResetCheck">
                        <p class="mb-2 text-sm leading-none">
                            {{ $t('app.stop break time after:') }}
                        </p>
                        <Select v-model="form.stopBreakTimeReset">
                            <SelectTrigger>
                                <SelectValue placeholder="Zeit" />
                            </SelectTrigger>
                            <SelectContent>
                                <SelectItem value="0">
                                    {{ $t('app.never') }}
                                </SelectItem>
                                <SelectItem value="5">
                                    5
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="10">
                                    10
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="20">
                                    20
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="30">
                                    30
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="40">
                                    40
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="50">
                                    50
                                    {{ $t('app.minutes') }}
                                </SelectItem>
                                <SelectItem value="60">
                                    1:00
                                    {{ $t('app.hour') }}
                                </SelectItem>
                                <SelectItem value="90">
                                    1:30
                                    {{ $t('app.hour') }}
                                </SelectItem>
                                <SelectItem value="120">
                                    2:00
                                    {{ $t('app.hours') }}
                                </SelectItem>
                                <SelectItem value="150">
                                    2:30
                                    {{ $t('app.hours') }}
                                </SelectItem>
                            </SelectContent>
                        </Select>
                    </div>
                </div>
                <Switch v-model:checked="stopTimeResetCheck" />
            </div>
        </div>
    </div>
</template>
