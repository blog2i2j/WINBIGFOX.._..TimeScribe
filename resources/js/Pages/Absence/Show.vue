<script lang="ts" setup>
import { TimeWheel } from '@/Components/ui-custom/time-wheel'
import { Button } from '@/Components/ui/button'
import { Absence, Date } from '@/types'
import { Head, Link, router } from '@inertiajs/vue3'
import { Cross, Drama, Trash, TreePalm } from 'lucide-vue-next'
import moment from 'moment/min/moment-with-locales'
import { computed } from 'vue'

const props = defineProps<{
    date: string
    plans: Record<string, number>
    absences: Absence[]
    holidays: Date[]
}>()

const momentSelectedDate = computed(() => moment(props.date, 'DD.MM.YYYY'))

const calendar = computed(() => {
    const startMonth = momentSelectedDate.value.clone().startOf('month')
    const startWeek = startMonth.clone().startOf('week')

    const days = Array.from({ length: 6 * 7 }, (_, i) => startWeek.clone().add(i, 'days'))

    return Array.from({ length: 6 }, (_, i) => days.slice(i * 7, i * 7 + 7))
})

const absences = computed(() =>
    props.absences.reduce(
        (acc, absence) => {
            acc[absence.date.date] = absence
            return acc
        },
        {} as Record<string, Absence>
    )
)

const holidays = computed(() =>
    props.holidays.reduce(
        (acc, holiday) => {
            acc[holiday.date] = holiday
            return acc
        },
        {} as Record<string, Date>
    )
)

const createAbsence = (type: 'vacation' | 'sick', date: string, duration?: number) => {
    router.flushAll()
    router.post(
        route('absence.store', {
            date: moment(props.date, 'DD.MM.YYYY').format('YYYY-MM-DD')
        }),
        {
            type,
            date: date + ' 00:00:00',
            duration
        },
        {
            preserveScroll: true,
            preserveState: true
        }
    )
}

const removeAbsence = (id: number) => {
    router.flushAll()
    router.delete(
        route('absence.destroy', {
            date: moment(props.date, 'DD.MM.YYYY').format('YYYY-MM-DD'),
            absence: id
        }),
        {
            preserveScroll: true,
            preserveState: true
        }
    )
}
</script>

<template>
    <Head title="Absence" />
    <div class="mb-4 flex items-center gap-4">
        <div class="text-foreground/80 text-base font-medium">{{ $t('app.absences') }}</div>
        <div class="flex flex-1 items-center justify-center text-sm">
            <TimeWheel :date="props.date" route="absence.show" type="month" />
        </div>
        <div>
            <Button
                :as="Link"
                :href="route('absence.show', { date: moment().format('YYYY-MM-DD') })"
                prefetch
                size="sm"
                variant="outline"
            >
                {{ $t('app.today') }}
            </Button>
        </div>
    </div>
    <div class="mb-6 flex grow flex-col divide-y overflow-clip rounded-lg select-none" ref="el">
        <div class="grid h-8 grid-cols-7">
            <div :key="day" class="px-2" v-for="day in 7">
                {{
                    moment()
                        .weekday(day - 1)
                        .format('dd')
                }}
            </div>
        </div>

        <div
            :key="weekIndex"
            class="border-muted grid flex-1 grid-cols-7 divide-x"
            v-for="(week, weekIndex) in calendar"
        >
            <div
                :class="{
                    'bg-muted/50 text-muted-foreground dark:bg-sidebar': day.day() === 0 || day.day() === 6
                }"
                :key="dayIndex"
                class="border-muted flex flex-col"
                v-for="(day, dayIndex) in week"
            >
                <div
                    :class="{
                        'py-0.75': day.day() === 0 || day.day() === 6,
                        'bg-muted text-muted-foreground dark:bg-muted/30':
                            day.day() !== 0 && day.day() !== 6 && holidays[day.format('YYYY-MM-DD')],
                        'opacity-40': !day.isSame(momentSelectedDate, 'month'),
                        'ring-primary ring-2': day.isSame(moment(), 'day'),
                        'text-background! bg-rose-400!': absences[day.format('YYYY-MM-DD')]?.type === 'sick',
                        'text-background! bg-emerald-500!': absences[day.format('YYYY-MM-DD')]?.type === 'vacation'
                    }"
                    class="border-muted group dark:border-muted/50 relative m-0.75 flex grow flex-col rounded-lg pr-1 pl-2 transition-all duration-500"
                >
                    <div class="flex items-center justify-between">
                        <div>
                            {{ day.format('D') }}
                        </div>
                        <div
                            :class="{
                                'bg-background! text-foreground! line-through opacity-50':
                                    absences[day.format('YYYY-MM-DD')]
                            }"
                            class="bg-primary text-primary-foreground rounded px-1.5 text-xs"
                            v-if="props.plans[day.format('YYYY-MM-DD')] && !holidays[day.format('YYYY-MM-DD')]"
                        >
                            {{ props.plans[day.format('YYYY-MM-DD')] }} {{ $t('app.h') }}
                        </div>
                        <Drama class="size-4" v-if="holidays[day.format('YYYY-MM-DD')]" />
                    </div>
                    <div
                        class="text-foreground hidden grow items-center justify-center gap-2 group-hover:flex"
                        v-if="!absences[day.format('YYYY-MM-DD')]"
                    >
                        <Button
                            @click="createAbsence('vacation', day.format('YYYY-MM-DD'))"
                            class="rounded-full"
                            size="icon"
                            v-if="!holidays[day.format('YYYY-MM-DD')] && props.plans[day.format('YYYY-MM-DD')]"
                            variant="outline"
                        >
                            <TreePalm />
                        </Button>
                        <Button
                            @click="createAbsence('sick', day.format('YYYY-MM-DD'))"
                            class="rounded-full"
                            size="icon"
                            variant="outline"
                        >
                            <Cross />
                        </Button>
                    </div>
                    <div class="relative flex grow items-center justify-center pb-2 text-xs" v-else>
                        <div
                            @click.prevent="removeAbsence(absences[day.format('YYYY-MM-DD')].id)"
                            class="text-background/80 absolute inset-0 z-10 flex items-center justify-center gap-1.5 rounded-full pb-2 opacity-0 transition-all duration-500 group-hover:opacity-100"
                        >
                            <Trash class="pointer-events-none size-4" />
                        </div>
                        <div
                            class="text-background/80 flex items-center gap-1.5 rounded-full bg-emerald-500 transition-all duration-500 group-hover:opacity-0"
                            v-if="absences[day.format('YYYY-MM-DD')].type === 'vacation'"
                        >
                            <TreePalm class="size-4" />
                            {{ $t('app.leave') }}
                        </div>

                        <div
                            class="text-background/80 flex items-center gap-1.5 rounded-full bg-rose-400 transition-all duration-500 group-hover:opacity-0"
                            v-if="absences[day.format('YYYY-MM-DD')].type === 'sick'"
                        >
                            <Cross class="size-4" />
                            {{ $t('app.sick') }}
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
