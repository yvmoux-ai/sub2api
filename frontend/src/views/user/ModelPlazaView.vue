<template>
  <AppLayout>
    <div class="space-y-6">
      <section class="plaza-hero card relative overflow-hidden p-6 md:p-7">
        <div class="plaza-hero-glow plaza-hero-glow-left"></div>
        <div class="plaza-hero-glow plaza-hero-glow-right"></div>

        <div class="relative z-10 flex flex-col gap-6 xl:flex-row xl:items-end xl:justify-between">
          <div class="max-w-3xl space-y-4">
            <span class="inline-flex w-fit items-center gap-2 rounded-full border border-sky-200 bg-white/80 px-3 py-1 text-xs font-semibold uppercase tracking-[0.24em] text-sky-700 shadow-sm dark:border-sky-800/50 dark:bg-dark-700/80 dark:text-sky-300">
              <Icon name="sparkles" size="sm" />
              {{ t('modelPlaza.heroBadge') }}
            </span>
            <div class="space-y-2">
              <h1 class="text-2xl font-semibold tracking-tight text-gray-950 dark:text-white md:text-3xl">
                {{ t('modelPlaza.heroTitle') }}
              </h1>
              <p class="max-w-2xl text-sm leading-7 text-gray-600 dark:text-gray-300 md:text-base">
                {{ t('modelPlaza.heroDescription') }}
              </p>
            </div>
          </div>

          <div class="grid grid-cols-1 gap-3 sm:grid-cols-3 xl:min-w-[420px]">
            <div
              v-for="stat in plazaStats"
              :key="stat.label"
              class="rounded-2xl border border-white/70 bg-white/80 p-4 shadow-sm backdrop-blur dark:border-white/10 dark:bg-dark-700/80"
            >
              <div class="mb-3 inline-flex rounded-xl p-2" :class="stat.iconWrapClass">
                <Icon :name="stat.icon" size="md" :class="stat.iconClass" />
              </div>
              <div class="text-2xl font-semibold text-gray-950 dark:text-white">{{ stat.value }}</div>
              <div class="mt-1 text-xs uppercase tracking-[0.18em] text-gray-500 dark:text-gray-400">
                {{ stat.label }}
              </div>
            </div>
          </div>
        </div>
      </section>

      <section class="card p-4 md:p-5">
        <div class="flex flex-col gap-4 lg:flex-row lg:items-center lg:justify-between">
          <div class="relative w-full lg:max-w-md">
            <Icon
              name="search"
              size="md"
              class="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400 dark:text-gray-500"
            />
            <input
              v-model="searchQuery"
              type="text"
              :placeholder="t('modelPlaza.searchPlaceholder')"
              class="input pl-10"
            />
          </div>

          <button
            type="button"
            class="btn btn-secondary shrink-0"
            :disabled="loading"
            @click="loadModels"
          >
            <Icon name="refresh" size="md" :class="loading ? 'animate-spin' : ''" />
            <span>{{ t('modelPlaza.refreshCta') }}</span>
          </button>
        </div>
      </section>

      <section v-if="loading" class="grid gap-4 md:grid-cols-2 2xl:grid-cols-3">
        <div
          v-for="index in 6"
          :key="index"
          class="card h-[280px] animate-pulse bg-gradient-to-br from-gray-100 to-gray-50 dark:from-dark-700 dark:to-dark-800"
        ></div>
      </section>

      <section v-else-if="filteredModels.length === 0" class="card p-10 text-center">
        <div class="mx-auto flex h-14 w-14 items-center justify-center rounded-2xl bg-sky-100 text-sky-600 dark:bg-sky-900/30 dark:text-sky-300">
          <Icon name="sparkles" size="xl" />
        </div>
        <h2 class="mt-4 text-lg font-semibold text-gray-900 dark:text-white">
          {{ t('modelPlaza.empty.title') }}
        </h2>
        <p class="mx-auto mt-2 max-w-xl text-sm leading-7 text-gray-500 dark:text-gray-400">
          {{ t('modelPlaza.empty.description') }}
        </p>
      </section>

      <section v-else class="grid gap-4 md:grid-cols-2 2xl:grid-cols-3">
        <article
          v-for="model in filteredModels"
          :key="model.id"
          class="plaza-card card group relative overflow-hidden p-5"
          :class="model.cardClass"
        >
          <div class="plaza-card-aura" :class="model.auraClass"></div>

          <div class="relative z-10 flex h-full flex-col gap-5">
            <div class="flex items-start justify-between gap-3">
              <div class="min-w-0">
                <div
                  class="mb-3 inline-flex items-center gap-2 rounded-full border px-3 py-1 text-[11px] font-semibold uppercase tracking-[0.18em]"
                  :class="model.platformBadgeClass"
                >
                  <PlatformIcon :platform="model.platform" size="sm" />
                  {{ model.platform }}
                </div>
                <h2 class="truncate text-lg font-semibold text-gray-950 dark:text-white">
                  {{ model.name }}
                </h2>
              </div>

              <div class="rounded-2xl border border-white/70 bg-white/80 p-2.5 shadow-sm dark:border-white/10 dark:bg-dark-700/80">
                <Icon name="brain" size="lg" :class="model.iconClass" />
              </div>
            </div>

            <div class="grid grid-cols-2 gap-3">
              <div class="rounded-2xl border border-gray-100 bg-white/80 p-3 dark:border-dark-600 dark:bg-dark-700/80">
                <div class="text-[11px] font-semibold uppercase tracking-[0.16em] text-gray-500 dark:text-gray-400">
                  {{ t('modelPlaza.card.availableOn') }}
                </div>
                <div class="mt-2 text-sm font-medium text-gray-900 dark:text-white">
                  {{ channelCountText(model.channels.length) }}
                </div>
              </div>
              <div class="rounded-2xl border border-gray-100 bg-white/80 p-3 dark:border-dark-600 dark:bg-dark-700/80">
                <div class="text-[11px] font-semibold uppercase tracking-[0.16em] text-gray-500 dark:text-gray-400">
                  {{ t('modelPlaza.card.pricing') }}
                </div>
                <div class="mt-2 text-sm font-medium text-gray-900 dark:text-white">
                  {{ pricingVariantText(model.pricingVariantCount) }}
                </div>
              </div>
            </div>

            <div class="rounded-2xl border border-gray-100/80 bg-white/80 p-4 dark:border-dark-600 dark:bg-dark-700/80">
              <div class="flex items-center gap-2 text-[11px] font-semibold uppercase tracking-[0.16em] text-gray-500 dark:text-gray-400">
                <Icon name="bolt" size="sm" />
                {{ t('modelPlaza.card.pricing') }}
              </div>
              <div class="mt-3 space-y-1.5">
                <div class="flex flex-wrap items-center gap-2 text-sm font-medium text-gray-900 dark:text-white">
                  {{ billingModeLabel(selectedPricingByModel[model.id] ?? null) }}
                  <span
                    v-if="multiplierSummary(selectedPricingByModel[model.id] ?? null, selectedGroupsByModel[model.id] ?? null)"
                    class="inline-flex items-center rounded-full border border-amber-200 bg-amber-50 px-2.5 py-0.5 text-xs font-semibold text-amber-700 dark:border-amber-800/60 dark:bg-amber-900/30 dark:text-amber-300"
                  >
                    {{ multiplierSummary(selectedPricingByModel[model.id] ?? null, selectedGroupsByModel[model.id] ?? null) }}
                  </span>
                </div>
                <div class="sr-only">
                  {{ displayPricingSummaryFixed(selectedPricingByModel[model.id] ?? null, selectedGroupsByModel[model.id] ?? null) }}
                </div>
                <div class="space-y-2 text-sm text-gray-600 dark:text-gray-300">
                  <div
                    v-for="item in pricingDetailRows(selectedPricingByModel[model.id] ?? null, selectedGroupsByModel[model.id] ?? null)"
                    :key="item.label"
                    class="flex items-center justify-between gap-3"
                  >
                    <span class="text-gray-500 dark:text-gray-400">{{ item.label }}</span>
                    <span class="font-medium text-gray-900 dark:text-white">{{ item.value }}</span>
                  </div>
                  <div
                    v-for="tier in imageTierRows(selectedPricingByModel[model.id] ?? null, selectedGroupsByModel[model.id] ?? null)"
                    :key="tier.label"
                    class="flex items-center justify-between gap-3 rounded-xl border border-dashed border-gray-200 bg-gray-50/80 px-3 py-2 dark:border-dark-500 dark:bg-dark-800/60"
                  >
                    <span class="text-gray-500 dark:text-gray-400">{{ tier.label }}</span>
                    <span class="font-medium text-gray-900 dark:text-white">{{ tier.value }}</span>
                  </div>
                </div>
              </div>
            </div>

            <div class="space-y-3">
              <div>
                <div class="mb-2 flex items-center gap-2 text-[11px] font-semibold uppercase tracking-[0.16em] text-gray-500 dark:text-gray-400">
                  <Icon name="users" size="sm" />
                  {{ t('modelPlaza.card.accessibleGroups') }}
                </div>
                <div v-if="model.groups.length > 0" class="flex flex-wrap gap-1.5">
                  <button
                    v-for="group in model.groups"
                    :key="group.id"
                    type="button"
                    class="rounded-lg transition focus:outline-none focus:ring-2 focus:ring-sky-400/60 focus:ring-offset-1 dark:focus:ring-offset-dark-700"
                    :class="selectedGroupsByModel[model.id]?.id === group.id
                      ? 'ring-2 ring-sky-400/70 ring-offset-1 dark:ring-sky-500/60 dark:ring-offset-dark-700'
                      : 'opacity-85 hover:opacity-100'"
                    @click="selectGroup(model.id, group.id)"
                  >
                    <GroupBadge
                      :name="group.name"
                      :platform="group.platform"
                      :subscription-type="group.subscription_type"
                      :rate-multiplier="group.rate_multiplier"
                      :user-rate-multiplier="userGroupRates[group.id] ?? null"
                      always-show-rate
                    />
                  </button>
                </div>
                <p v-else class="text-sm text-gray-500 dark:text-gray-400">
                  {{ t('modelPlaza.card.noGroups') }}
                </p>
              </div>

              <div>
                <div class="mb-2 flex items-center gap-2 text-[11px] font-semibold uppercase tracking-[0.16em] text-gray-500 dark:text-gray-400">
                  <Icon name="cube" size="sm" />
                  {{ t('modelPlaza.card.availableOn') }}
                </div>
                <div class="flex flex-wrap gap-2">
                  <span
                    v-for="channel in model.channels"
                    :key="channel"
                    class="inline-flex items-center rounded-full border border-gray-200 bg-white/80 px-3 py-1 text-xs text-gray-600 dark:border-dark-500 dark:bg-dark-800/80 dark:text-gray-300"
                  >
                    {{ channel }}
                  </span>
                </div>
              </div>
            </div>
          </div>
        </article>
      </section>
    </div>
  </AppLayout>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import { useI18n } from 'vue-i18n'
import AppLayout from '@/components/layout/AppLayout.vue'
import Icon from '@/components/icons/Icon.vue'
import PlatformIcon from '@/components/common/PlatformIcon.vue'
import GroupBadge from '@/components/common/GroupBadge.vue'
import userChannelsAPI, {
  type UserAvailableChannel,
  type UserSupportedModelPricing
} from '@/api/channels'
import userGroupsAPI from '@/api/groups'
import { useAppStore } from '@/stores/app'
import { extractApiErrorMessage } from '@/utils/apiError'
import type { Group, GroupPlatform, SubscriptionType } from '@/types'

interface ModelCardGroup {
  id: number
  name: string
  platform: GroupPlatform
  subscription_type: SubscriptionType
  rate_multiplier: number
  image_rate_independent: boolean
  image_rate_multiplier: number
}

interface ModelCard {
  id: string
  name: string
  platform: GroupPlatform
  groups: ModelCardGroup[]
  channels: string[]
  representativePricing: UserSupportedModelPricing | null
  pricingByGroupId: Record<number, UserSupportedModelPricing | null>
  pricingVariantCount: number
  cardClass: string
  auraClass: string
  iconClass: string
  platformBadgeClass: string
}

const { t, locale } = useI18n()
const appStore = useAppStore()

const loading = ref(false)
const searchQuery = ref('')
const models = ref<ModelCard[]>([])
const userGroupRates = ref<Record<number, number>>({})
const selectedGroupIds = ref<Record<string, number>>({})

const filteredModels = computed(() => {
  const query = searchQuery.value.trim().toLowerCase()
  if (!query) return models.value

  return models.value.filter((model) => {
    const haystack = [
      model.name,
      model.platform,
      ...model.channels,
      ...model.groups.map((group) => group.name)
    ]
      .join(' ')
      .toLowerCase()

    return haystack.includes(query)
  })
})

const selectedGroupsByModel = computed<Record<string, ModelCardGroup | null>>(() => {
  const result: Record<string, ModelCardGroup | null> = {}

  for (const model of models.value) {
    const selectedId = selectedGroupIds.value[model.id]
    result[model.id] = model.groups.find((group) => group.id === selectedId) ?? model.groups[0] ?? null
  }

  return result
})

const selectedPricingByModel = computed<Record<string, UserSupportedModelPricing | null>>(() => {
  const result: Record<string, UserSupportedModelPricing | null> = {}

  for (const model of models.value) {
    const selectedGroup = selectedGroupsByModel.value[model.id]
    result[model.id] = selectedGroup
      ? (model.pricingByGroupId[selectedGroup.id] ?? model.representativePricing)
      : model.representativePricing
  }

  return result
})

const plazaStats = computed(() => {
  const platformCount = new Set(models.value.map((item) => item.platform)).size
  const groupCount = new Set(models.value.flatMap((item) => item.groups.map((group) => group.id))).size

  return [
    {
      label: t('modelPlaza.stats.models'),
      value: models.value.length,
      icon: 'brain' as const,
      iconWrapClass: 'bg-sky-100 text-sky-600 dark:bg-sky-900/30 dark:text-sky-300',
      iconClass: 'text-sky-600 dark:text-sky-300'
    },
    {
      label: t('modelPlaza.stats.platforms'),
      value: platformCount,
      icon: 'globe' as const,
      iconWrapClass: 'bg-emerald-100 text-emerald-600 dark:bg-emerald-900/30 dark:text-emerald-300',
      iconClass: 'text-emerald-600 dark:text-emerald-300'
    },
    {
      label: t('modelPlaza.stats.groups'),
      value: groupCount,
      icon: 'users' as const,
      iconWrapClass: 'bg-amber-100 text-amber-600 dark:bg-amber-900/30 dark:text-amber-300',
      iconClass: 'text-amber-600 dark:text-amber-300'
    }
  ]
})

function pricingFingerprint(pricing: UserSupportedModelPricing | null): string {
  if (!pricing) return 'none'
  return JSON.stringify({
    billing_mode: pricing.billing_mode,
    input_price: pricing.input_price,
    output_price: pricing.output_price,
    cache_write_price: pricing.cache_write_price,
    cache_read_price: pricing.cache_read_price,
    image_output_price: pricing.image_output_price,
    per_request_price: pricing.per_request_price,
    intervals: pricing.intervals
  })
}

function getPlatformTheme(platform: GroupPlatform) {
  if (platform === 'anthropic') {
    return {
      cardClass: 'from-orange-50/90 to-white dark:from-orange-950/18 dark:to-dark-700',
      auraClass: 'bg-orange-300/40 dark:bg-orange-500/20',
      iconClass: 'text-orange-500 dark:text-orange-300',
      platformBadgeClass: 'border-orange-200 bg-orange-50/90 text-orange-700 dark:border-orange-800/50 dark:bg-orange-900/30 dark:text-orange-300'
    }
  }
  if (platform === 'gemini') {
    return {
      cardClass: 'from-blue-50/90 to-white dark:from-blue-950/18 dark:to-dark-700',
      auraClass: 'bg-blue-300/40 dark:bg-blue-500/20',
      iconClass: 'text-blue-500 dark:text-blue-300',
      platformBadgeClass: 'border-blue-200 bg-blue-50/90 text-blue-700 dark:border-blue-800/50 dark:bg-blue-900/30 dark:text-blue-300'
    }
  }
  if (platform === 'openai') {
    return {
      cardClass: 'from-emerald-50/90 to-white dark:from-emerald-950/18 dark:to-dark-700',
      auraClass: 'bg-emerald-300/40 dark:bg-emerald-500/20',
      iconClass: 'text-emerald-500 dark:text-emerald-300',
      platformBadgeClass: 'border-emerald-200 bg-emerald-50/90 text-emerald-700 dark:border-emerald-800/50 dark:bg-emerald-900/30 dark:text-emerald-300'
    }
  }

  return {
    cardClass: 'from-violet-50/90 to-white dark:from-violet-950/18 dark:to-dark-700',
    auraClass: 'bg-violet-300/40 dark:bg-violet-500/20',
    iconClass: 'text-violet-500 dark:text-violet-300',
    platformBadgeClass: 'border-violet-200 bg-violet-50/90 text-violet-700 dark:border-violet-800/50 dark:bg-violet-900/30 dark:text-violet-300'
  }
}

function aggregateModels(channels: UserAvailableChannel[], availableGroups: Group[]): ModelCard[] {
  const availableGroupMap = new Map(availableGroups.map((group) => [group.id, group]))
  const map = new Map<
    string,
    {
      name: string
      platform: GroupPlatform
      groups: Map<number, ModelCardGroup>
      channels: Set<string>
      pricingFingerprints: Set<string>
      representativePricing: UserSupportedModelPricing | null
      pricingByGroupId: Map<number, UserSupportedModelPricing | null>
    }
  >()

  for (const channel of channels) {
    for (const section of channel.platforms) {
      const platform = section.platform as GroupPlatform
      for (const model of section.supported_models) {
        const key = `${platform}::${model.name}`
        const existing = map.get(key) ?? {
          name: model.name,
          platform,
          groups: new Map<number, ModelCardGroup>(),
          channels: new Set<string>(),
          pricingFingerprints: new Set<string>(),
          representativePricing: null,
          pricingByGroupId: new Map<number, UserSupportedModelPricing | null>()
        }

        existing.channels.add(channel.name)
        for (const group of section.groups) {
          const fullGroup = availableGroupMap.get(group.id)
          existing.groups.set(group.id, {
            id: group.id,
            name: group.name,
            platform: group.platform as GroupPlatform,
            subscription_type: (group.subscription_type || 'standard') as SubscriptionType,
            rate_multiplier: group.rate_multiplier,
            image_rate_independent: fullGroup?.image_rate_independent ?? false,
            image_rate_multiplier: fullGroup?.image_rate_multiplier ?? 1
          })
          if (!existing.pricingByGroupId.has(group.id)) {
            existing.pricingByGroupId.set(group.id, model.pricing ?? null)
          }
        }
        if (!existing.representativePricing && model.pricing) {
          existing.representativePricing = model.pricing
        }
        if (model.pricing) {
          existing.pricingFingerprints.add(pricingFingerprint(model.pricing))
        }

        map.set(key, existing)
      }
    }
  }

  return [...map.entries()]
    .map(([id, item]) => ({
      id,
      name: item.name,
      platform: item.platform,
      groups: [...item.groups.values()].sort((a, b) => a.name.localeCompare(b.name)),
      channels: [...item.channels.values()].sort((a, b) => a.localeCompare(b)),
      representativePricing: item.representativePricing,
      pricingByGroupId: Object.fromEntries(item.pricingByGroupId),
      pricingVariantCount: item.pricingFingerprints.size,
      ...getPlatformTheme(item.platform)
    }))
    .sort((a, b) => a.name.localeCompare(b.name) || a.platform.localeCompare(b.platform))
}

function formatPrice(value: number | null): string | null {
  if (value === null || Number.isNaN(value)) return null
  const fixed = value >= 1 ? value.toFixed(2) : value.toFixed(4)
  return `$${fixed.replace(/\.?0+$/, '')}`
}

function formatPerMillionPrice(value: number | null): string | null {
  if (value === null || Number.isNaN(value)) return null
  return formatPrice(value * 1_000_000)
}

function priceLabel(key: 'input' | 'output' | 'cacheWrite' | 'cacheRead' | 'perRequest' | 'defaultPrice') {
  const zh = locale.value.startsWith('zh')
  switch (key) {
    case 'input':
      return zh ? '输入' : 'Input'
    case 'output':
      return zh ? '输出' : 'Output'
    case 'cacheWrite':
      return zh ? '缓存写入' : 'Cache write'
    case 'cacheRead':
      return zh ? '缓存读取' : 'Cache read'
    case 'perRequest':
      return zh ? '单次价格' : 'Per request'
    default:
      return zh ? '默认价格' : 'Default price'
  }
}

function effectiveMultiplier(pricing: UserSupportedModelPricing, group: ModelCardGroup): number {
  if (pricing.billing_mode === 'image' && group.image_rate_independent) {
    return group.image_rate_multiplier || 1
  }
  return userGroupRates.value[group.id] ?? group.rate_multiplier ?? 1
}

function formatMultiplier(value: number): string {
  if (!Number.isFinite(value)) return '-'
  return `${value.toFixed(value >= 1 ? 2 : 3).replace(/\.?0+$/, '')}x`
}

function multiplierSummary(pricing: UserSupportedModelPricing | null, group: ModelCardGroup | null): string {
  if (!pricing || !group) return ''

  const label = pricing.billing_mode === 'image'
    ? (locale.value.startsWith('zh') ? '图片倍率' : 'Image multiplier')
    : (locale.value.startsWith('zh') ? '有效倍率' : 'Effective multiplier')

  return `${label}: ${formatMultiplier(effectiveMultiplier(pricing, group))}`
}

function scaledValue(
  pricing: UserSupportedModelPricing,
  group: ModelCardGroup | null,
  basePrice: number | null
): number | null {
  if (basePrice === null || Number.isNaN(basePrice)) return null
  if (!group) return basePrice
  return basePrice * effectiveMultiplier(pricing, group)
}

function billingModeLabel(pricing: UserSupportedModelPricing | null): string {
  if (!pricing) return t('modelPlaza.card.noPricing')
  if (pricing.billing_mode === 'per_request') return t('modelPlaza.card.billingMode.perRequest')
  if (pricing.billing_mode === 'image') return t('modelPlaza.card.billingMode.image')
  return t('modelPlaza.card.billingMode.token')
}

function channelCountText(count: number): string {
  if (locale.value.startsWith('zh')) return t('modelPlaza.card.channelCount', { count })
  return `${count} ${count === 1 ? 'channel' : 'channels'}`
}

function pricingVariantText(count: number): string {
  if (locale.value.startsWith('zh')) return t('modelPlaza.card.pricingVariants', { count })
  return `${count} ${count === 1 ? 'pricing setup' : 'pricing setups'}`
}

function displayPricingSummaryFixed(pricing: UserSupportedModelPricing | null, group: ModelCardGroup | null): string {
  if (!pricing) return t('modelPlaza.card.noPricing')

  if (pricing.billing_mode === 'per_request') {
    const price = scaledValue(pricing, group, pricing.per_request_price)
    return price !== null ? `${formatPrice(price)} / req` : t('modelPlaza.card.noPricing')
  }

  if (pricing.billing_mode === 'image') {
    const imagePrice = scaledValue(pricing, group, pricing.per_request_price ?? pricing.image_output_price)
    return imagePrice !== null ? `${formatPrice(imagePrice)} / image` : t('modelPlaza.card.noPricing')
  }

  const parts: string[] = []
  const inputPrice = scaledValue(pricing, group, pricing.input_price)
  const outputPrice = scaledValue(pricing, group, pricing.output_price)
  if (inputPrice !== null) parts.push(`${priceLabel('input')} ${formatPerMillionPrice(inputPrice)}`)
  if (outputPrice !== null) parts.push(`${priceLabel('output')} ${formatPerMillionPrice(outputPrice)}`)
  if (parts.length === 0) return t('modelPlaza.card.noPricing')
  return `${parts.join(locale.value.startsWith('zh') ? ' / ' : ' | ')} / 1M`
}

function pricingDetailRows(
  pricing: UserSupportedModelPricing | null,
  group: ModelCardGroup | null
): Array<{ label: string; value: string }> {
  if (!pricing) return []

  if (pricing.billing_mode === 'token') {
    return [
      {
        label: priceLabel('input'),
        value: formatPerMillionPrice(scaledValue(pricing, group, pricing.input_price)) ?? '-'
      },
      {
        label: priceLabel('output'),
        value: formatPerMillionPrice(scaledValue(pricing, group, pricing.output_price)) ?? '-'
      },
      {
        label: priceLabel('cacheWrite'),
        value: formatPerMillionPrice(scaledValue(pricing, group, pricing.cache_write_price)) ?? '-'
      },
      {
        label: priceLabel('cacheRead'),
        value: formatPerMillionPrice(scaledValue(pricing, group, pricing.cache_read_price)) ?? '-'
      }
    ]
  }

  if (pricing.billing_mode === 'per_request') {
    const value = scaledValue(pricing, group, pricing.per_request_price)
    return [
      {
        label: priceLabel('perRequest'),
        value: value !== null ? `${formatPrice(value)} / req` : '-'
      }
    ]
  }

  const basePrice = scaledValue(pricing, group, pricing.per_request_price ?? pricing.image_output_price)
  return [
    {
      label: priceLabel('defaultPrice'),
      value: basePrice !== null ? `${formatPrice(basePrice)} / image` : '-'
    }
  ]
}

function imageTierRows(
  pricing: UserSupportedModelPricing | null,
  group: ModelCardGroup | null
): Array<{ label: string; value: string }> {
  if (!pricing || pricing.billing_mode !== 'image' || !pricing.intervals?.length) return []

  return pricing.intervals.map((interval) => {
    const tierPrice = scaledValue(pricing, group, interval.per_request_price)
    return {
      label: interval.tier_label?.trim() || `${interval.min_tokens}-${interval.max_tokens ?? 'max'}`,
      value: tierPrice !== null ? `${formatPrice(tierPrice)} / image` : '-'
    }
  })
}

function selectGroup(modelId: string, groupId: number) {
  selectedGroupIds.value = {
    ...selectedGroupIds.value,
    [modelId]: groupId
  }
}

function initializeSelectedGroups(nextModels: ModelCard[]) {
  const nextSelected: Record<string, number> = {}

  for (const model of nextModels) {
    const currentId = selectedGroupIds.value[model.id]
    const matched = model.groups.find((group) => group.id === currentId)
    nextSelected[model.id] = matched?.id ?? model.groups[0]?.id ?? 0
  }

  selectedGroupIds.value = nextSelected
}

async function loadModels() {
  loading.value = true
  try {
    const [channels, groups, rates] = await Promise.all([
      userChannelsAPI.getAvailable(),
      userGroupsAPI.getAvailable().catch((error: unknown) => {
        console.error('Failed to load available groups:', error)
        return [] as Group[]
      }),
      userGroupsAPI.getUserGroupRates().catch((error: unknown) => {
        console.error('Failed to load user group rates:', error)
        return {} as Record<number, number>
      })
    ])

    userGroupRates.value = rates
    const nextModels = aggregateModels(channels, groups)
    initializeSelectedGroups(nextModels)
    models.value = nextModels
  } catch (error: unknown) {
    appStore.showError(extractApiErrorMessage(error, t('common.error')))
  } finally {
    loading.value = false
  }
}

onMounted(loadModels)
</script>

<style scoped>
.plaza-hero {
  background:
    radial-gradient(circle at top left, rgba(56, 189, 248, 0.18), transparent 38%),
    radial-gradient(circle at right 20%, rgba(16, 185, 129, 0.16), transparent 32%),
    linear-gradient(135deg, rgba(250, 250, 250, 0.98), rgba(240, 249, 255, 0.92));
}

.dark .plaza-hero {
  background:
    radial-gradient(circle at top left, rgba(56, 189, 248, 0.15), transparent 38%),
    radial-gradient(circle at right 20%, rgba(16, 185, 129, 0.12), transparent 32%),
    linear-gradient(135deg, rgba(17, 24, 39, 0.98), rgba(15, 23, 42, 0.94));
}

.plaza-hero-glow {
  position: absolute;
  border-radius: 9999px;
  filter: blur(30px);
  opacity: 0.7;
  pointer-events: none;
}

.plaza-hero-glow-left {
  left: -60px;
  top: -50px;
  width: 180px;
  height: 180px;
  background: rgba(59, 130, 246, 0.18);
}

.plaza-hero-glow-right {
  right: 4%;
  bottom: -80px;
  width: 220px;
  height: 220px;
  background: rgba(16, 185, 129, 0.15);
}

.plaza-card {
  background-image: linear-gradient(135deg, var(--tw-gradient-from), var(--tw-gradient-to));
  transition:
    transform 0.18s ease,
    box-shadow 0.18s ease,
    border-color 0.18s ease;
}

.plaza-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 16px 40px rgba(15, 23, 42, 0.08);
}

.dark .plaza-card:hover {
  box-shadow: 0 18px 42px rgba(2, 6, 23, 0.34);
}

.plaza-card-aura {
  position: absolute;
  right: -32px;
  top: -28px;
  width: 120px;
  height: 120px;
  border-radius: 9999px;
  filter: blur(24px);
  opacity: 0.55;
  pointer-events: none;
}
</style>
