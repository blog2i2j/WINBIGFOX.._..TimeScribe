<script setup lang="ts">
import {
    Select,
    SelectContent,
    SelectItem,
    SelectSeparator,
    SelectTrigger,
    SelectValue,
} from '@/Components/ui/select';
import { Switch } from '@/Components/ui/switch';
import { SettingProps } from '@/Pages/NewSettings/Index.vue';
import { useColorMode } from '@vueuse/core';
import {
    CalendarMinus,
    Eye,
    KeyRound,
    Languages,
    SunMoon,
} from 'lucide-vue-next';
import { ref, watch } from 'vue';

const form = defineModel<SettingProps>();
const { store } = useColorMode();
const holidayCheck = ref(form.value?.holidayRegion !== null);
watch(holidayCheck, () => {
    if (holidayCheck.value === false && form.value) {
        form.value.holidayRegion = '';
    }
});

</script>

<template>
    <div class="mt-0 space-y-4" v-if="form">
        <div class="flex items-center space-x-4 rounded-md border p-4">
            <KeyRound />
            <div class="flex-1 space-y-1">
                <p class="text-sm leading-none font-medium">
                    {{ $t('app.start at login') }}
                </p>
            </div>
            <Switch v-model:checked="form.openAtLogin" />
        </div>
        <div class="flex items-start space-x-4 rounded-md border p-4">
            <Languages />
            <div class="flex-1 space-y-1">
                <p class="text-sm leading-none font-medium">
                    {{ $t('app.language') }}
                </p>
                <div class="mt-2">
                    <Select size="5" v-model="form.locale">
                        <SelectTrigger>
                            <SelectValue :placeholder="$t('app.language')" />
                        </SelectTrigger>
                        <SelectContent>
                            <SelectItem value="de-DE">
                                {{ $t('app.german') }}
                            </SelectItem>
                            <SelectItem value="en-GB">
                                {{ $t('app.english (UK)') }}
                            </SelectItem>
                            <SelectItem value="en-US">
                                {{ $t('app.english (US)') }}
                            </SelectItem>
                        </SelectContent>
                    </Select>
                </div>
            </div>
        </div>
        <div class="flex items-start space-x-4 rounded-md border p-4">
            <SunMoon />
            <div class="flex-1 space-y-1">
                <p class="text-sm leading-none font-medium">
                    {{ $t('app.appearance') }}
                </p>
                <p class="text-muted-foreground text-sm text-balance">
                    {{ $t('app.choose the appearance of the application.') }}
                </p>
                <div class="mt-2">
                    <Select size="5" v-model="store">
                        <SelectTrigger>
                            <SelectValue :placeholder="$t('app.appearance')" />
                        </SelectTrigger>
                        <SelectContent>
                            <SelectItem value="auto">
                                {{ $t('app.system') }}
                            </SelectItem>
                            <SelectItem value="light">
                                {{ $t('app.light') }}
                            </SelectItem>
                            <SelectItem value="dark">
                                {{ $t('app.dark') }}
                            </SelectItem>
                        </SelectContent>
                    </Select>
                </div>
            </div>
        </div>
        <div class="flex items-start space-x-4 rounded-md border p-4">
            <Eye />
            <div class="flex-1 space-y-1">
                <p class="text-sm leading-none font-medium">
                    {{ $t('app.show timer automatically') }}
                </p>
                <p class="text-muted-foreground text-sm text-balance">
                    {{
                        $t(
                            'app.when the computer is unlocked, the timer can be displayed.',
                        )
                    }}
                </p>
            </div>
            <Switch v-model:checked="form.showTimerOnUnlock" />
        </div>

        <div class="flex items-start space-x-4 rounded-md border p-4">
            <CalendarMinus />
            <div class="flex-1 space-y-1">
                <p class="text-sm leading-none font-medium">
                    {{ $t('app.consider public holidays') }}
                </p>
                <p class="text-muted-foreground text-sm">
                    {{
                        $t(
                            'app.working hours on public holidays are fully credited.',
                        )
                    }}
                </p>
                <div class="mt-2" v-if="holidayCheck">
                    <Select size="5" v-model="form.holidayRegion">
                        <SelectTrigger>
                            <SelectValue placeholder="Region" />
                        </SelectTrigger>
                        <SelectContent>
                            <SelectItem value="DE"> Deutschland</SelectItem>
                            <SelectSeparator />
                            <SelectItem value="DE-BB"> Brandenburg</SelectItem>
                            <SelectItem value="DE-BE"> Berlin</SelectItem>
                            <SelectItem value="DE-BW">
                                Baden-Württemberg
                            </SelectItem>
                            <SelectItem value="DE-BY"> Bayern</SelectItem>
                            <SelectItem value="DE-HB"> Bremen</SelectItem>
                            <SelectItem value="DE-HE"> Hessen</SelectItem>
                            <SelectItem value="DE-HH"> Hamburg</SelectItem>
                            <SelectItem value="DE-MV">
                                Mecklenburg-Vorpommern
                            </SelectItem>
                            <SelectItem value="DE-NI">
                                {{ $t('app.never') }}dersachsen
                            </SelectItem>
                            <SelectItem value="DE-NW">
                                Nordrhein-Westfalen
                            </SelectItem>
                            <SelectItem value="DE-RP">
                                Rheinland-Pfalz
                            </SelectItem>
                            <SelectItem value="DE-SH">
                                Schleswig-Holstein
                            </SelectItem>
                            <SelectItem value="DE-SL"> Saarland</SelectItem>
                            <SelectItem value="DE-SN"> Sachsen</SelectItem>
                            <SelectItem value="DE-ST">
                                Sachsen-Anhalt
                            </SelectItem>
                            <SelectItem value="DE-TH"> Thüringen</SelectItem>
                        </SelectContent>
                    </Select>
                </div>
            </div>
            <Switch v-model:checked="holidayCheck" />
        </div>
    </div>
</template>
