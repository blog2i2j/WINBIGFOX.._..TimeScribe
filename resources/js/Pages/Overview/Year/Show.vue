<script lang="ts" setup>
import { TimeWheel } from '@/Components/ui-custom/time-wheel'
import { Button } from '@/Components/ui/button'
import { secToFormat } from '@/lib/utils'
import { Head, Link, router, usePage } from '@inertiajs/vue3'
import { useCssVar } from '@vueuse/core'
import { ApexOptions } from 'apexcharts'
import de from 'apexcharts/dist/locales/de.json'
import en from 'apexcharts/dist/locales/en.json'
import fr from 'apexcharts/dist/locales/fr.json'
import zhCn from 'apexcharts/dist/locales/zh-cn.json'
import { trans } from 'laravel-vue-i18n'
import moment from 'moment/min/moment-with-locales'

const props = defineProps<{
    date: string
    workTimes: number[]
    breakTimes: number[]
    plans: number[]
    overtimes: number[]
    xaxis: string[]
    sumBreakTime: number
    sumWorkTime: number
    sumOvertime: number
    sumPlan: number
    groups: { title: string; cols: number }[]
    links: string[]
}>()

const localeMapping = {
    'de-DE': 'de',
    'en-GB': 'en',
    'en-US': 'en',
    'fr-FR': 'fr',
    'fr-CA': 'fr',
    'zh-CN': 'zh-cn'
}
const currentLocale = localeMapping[usePage().props.js_locale]

const showWeek = (opts) => {
    router.get(props.links[opts.dataPointIndex], {
        preserveScroll: true,
        preserveState: true
    })
}
const data = {
    series: [
        {
            name: trans('app.work hours'),
            data: props.workTimes
        },
        {
            name: trans('app.overtime'),
            data: props.overtimes
        },
        {
            name: trans('app.break time'),
            data: props.breakTimes
        }
    ],
    chartOptions: {
        colors: ['var(--color-primary)', 'var(--color-amber-400)', 'var(--color-pink-400)'],
        chart: {
            events: {
                dataPointSelection: (_1, _2, opts) => showWeek(opts)
            },
            background: 'transparent',
            fontFamily: 'var(--font-sans)',
            locales: [de, en, fr, zhCn],
            defaultLocale: currentLocale,
            type: 'bar',
            stacked: true,
            toolbar: {
                show: false
            },
            zoom: {
                enabled: false
            },
            animations: {
                enabled: false
            },
            parentHeightOffset: 0,
            offsetX: 0
        },
        plotOptions: {
            bar: {
                horizontal: false,
                borderRadius: 2,
                borderRadiusApplication: 'end',
                borderRadiusWhenStacked: 'last' // 'all', 'last'
            }
        },
        dataLabels: {
            enabled: false
        },
        xaxis: {
            type: 'category',
            categories: props.xaxis,
            tickAmount: 28,
            labels: {
                show: true,
                rotate: -90,
                rotateAlways: true,
                offsetX: -1,
                style: {
                    colors: 'var(--color-foreground)',
                    fontSize: '10px',
                    fontWeight: 'var(--font-normal)',
                    cssClass: ''
                }
            },
            group: {
                groups: props.groups,
                style: {
                    colors: 'var(--color-foreground)',
                    fontSize: '12px'
                }
            },
            axisBorder: {
                show: true,
                color: 'var(--color-sidebar-border)'
            },
            axisTicks: {
                show: true,
                borderType: 'solid',
                color: 'var(--color-sidebar-border)',
                width: 6
            }
        },
        noData: {
            text: trans('app.no times available'),
            style: {
                color: 'var(--color-foreground)'
            }
        },
        yaxis: {
            stepSize: 14400,
            labels: {
                offsetX: -13,
                style: {
                    colors: 'var(--color-foreground)',
                    fontSize: '12px',
                    cssClass: ''
                },
                formatter: (value) => {
                    return secToFormat(value, true, true, true)
                }
            },
            axisBorder: {
                show: true,
                color: 'var(--color-sidebar-border)'
            },
            axisTicks: {
                show: true,
                borderType: 'solid',
                color: 'var(--color-sidebar-border)',
                width: 6
            }
        },
        grid: {
            borderColor: 'var(--color-sidebar-border)',
            strokeDashArray: 2,
            row: {
                opacity: 0
            },
            padding: {
                left: 0,
                right: 0
            }
        },
        states: {
            active: {
                filter: {
                    type: 'none'
                }
            },
            hover: {
                filter: {
                    type: 'none'
                }
            }
        },
        fill: {
            opacity: 1
        },
        tooltip: {
            style: {
                fontSize: useCssVar('--text-sm').value
            },
            x: {
                formatter: (value) => `${trans('app.week')} ${value}`
            },
            y: {
                formatter: (value) => {
                    const time = secToFormat(value, true, true, true)
                    if (value >= 3600) {
                        return `${time} ${trans('app.h')}`
                    }
                    return `${time} ${trans('app.min')}`
                }
            }
        },
        legend: {
            position: 'top',
            horizontalAlign: 'left',
            labels: {
                colors: 'var(--color-foreground)'
            },
            fontSize: '14px',
            offsetX: -35,
            offsetY: 0,
            markers: {
                size: 6,
                shape: 'circle',
                offsetX: -4,
                strokeWidth: 0
            },
            itemMargin: {
                horizontal: 10,
                vertical: 0
            }
        }
    } as ApexOptions
}

const reload = () => {
    router.flushAll()
    router.reload({
        showProgress: false
    })
}

if (window.Native) {
    window.Native.on('App\\Events\\TimerStarted', reload)
    window.Native.on('App\\Events\\TimerStopped', reload)
}
</script>

<template>
    <Head title="Week Overview" />

    <div class="mb-4 flex items-center gap-4">
        <div class="text-foreground/80 text-base font-medium">{{ $t('app.yearly overview') }}</div>
        <div class="flex flex-1 items-center justify-center text-sm">
            <TimeWheel :date="props.date" route="overview.year.show" type="year" />
        </div>
        <div>
            <Button
                :as="Link"
                :href="route('overview.year.show', { date: moment().format('YYYY-MM-DD') })"
                prefetch
                size="sm"
                variant="outline"
            >
                {{ $t('app.today') }}
            </Button>
        </div>
    </div>
    <div class="mt-2 mb-6 h-full">
        <apexchart :options="data.chartOptions" :series="data.series" height="100%" type="bar"></apexchart>
    </div>
</template>
