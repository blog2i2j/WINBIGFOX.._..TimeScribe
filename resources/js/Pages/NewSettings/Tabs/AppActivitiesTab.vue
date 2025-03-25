<script setup lang="ts">
import { Switch } from '@/Components/ui/switch';
import { SettingProps } from '@/Pages/NewSettings/Index.vue';
import { AppWindowMac } from 'lucide-vue-next';

const form = defineModel<SettingProps>();
</script>

<template>
    <div class="mt-0 space-y-4">
        <div class="flex items-start space-x-4 rounded-md border p-4">
            <AppWindowMac />
            <div class="flex-1 space-y-1">
                <p class="text-sm leading-none font-medium">
                    {{ $t('app.record app activities') }}
                </p>
                <p class="text-muted-foreground text-sm">
                    {{
                        $t(
                            'app.records your app activity and saves which app you were active in and for how long.',
                        )
                    }}
                </p>
            </div>
            <Switch v-model:checked="form.appActivityTracking" />
        </div>
    </div>
</template>
