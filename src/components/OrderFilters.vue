<template>
  <div class="flex flex-wrap gap-4 relative z-0 bg-white">
    <!-- headers to create filter sections -->
    <div
      v-for="header in headers"
      :key="header.key"
      class="flex flex-col w-full min-h-[112px] max-h-[112px] overflow-y-auto text-left justify-between"
    >
      <div
        class="bg-white border border-gray-300 shadow max-h-40 overflow-y-auto text-sm"
      >
        <div>
          <!-- Dynamic filter options for 'statusFilterOptions'-->
          <div v-if="header.key === 'status'">
            <div
              v-for="option in statusFilterOptions"
              :key="option"
              class="flex items-center gap-1 px-1 py-0.5 text-[11px]"
            >
              <input
                type="checkbox"
                :checked="filters[header.key].includes(option)"
                @change="$emit('toggle-option', header.key, option)"
                class="w-3 h-3"
              />
              <span class="text-xs text-gray-700 font-normal truncate w-full">
                {{ option }}
              </span>
            </div>
          </div>

          <!-- Dynamic filter options for 'daysSinceOrder'-->
          <div v-else-if="header.key === 'daysSinceOrder'">
            <div
              v-for="option in daysSinceOrderRanges"
              :key="option"
              class="flex items-center gap-1 px-1 py-0.5 text-[11px]"
            >
              <input
                type="checkbox"
                :checked="filters[header.key].includes(option)"
                @change="$emit('toggle-option', header.key, option)"
                class="w-3 h-3"
              />
              <span class="text-xs text-gray-700 font-normal truncate w-full">
                {{ option }}
              </span>
            </div>
          </div>

          <!-- For other columns, dynamically get filter options -->
          <div v-else>
            <div
              v-for="option in getUniqueOptions(header.key)"
              :key="option"
              class="flex items-center gap-1 px-1 py-0.5 text-[11px]"
            >
              <input
                type="checkbox"
                :checked="filters[header.key].includes(option)"
                @change="$emit('toggle-option', header.key, option)"
                class="w-3 h-3"
              />
              <span class="text-xs text-gray-700 font-normal truncate w-full">
                {{ option }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- Buttons to select all or clear the selection -->
      <div class="flex justify-between text-[10px] text-black cursor-pointer">
        <button
          class="whitespace-nowrap"
          @click="$emit('select-all', header.key)"
        >
          Select all
        </button>

        <button
          class="whitespace-nowrap"
          @click="$emit('clear-selection', header.key)"
        >
          Clear
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    // Props related to filtering logic for the table
    headers: Array,
    filters: Object,
    statusFilterOptions: Array,
    daysSinceOrderRanges: Array,
    getUniqueOptions: Function,
  },
};
</script>
