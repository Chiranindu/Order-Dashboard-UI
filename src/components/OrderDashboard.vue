<template>
  <div class="relative">
    <div class="fixed top-0 left-0 w-full z-50 bg-gray-100 shadow-sm">
      <!-- Header Section -->
      <h2 class="text-2xl font-bold mt-0 z-10">Order Dashboard</h2>
    </div>

    <!-- Filter Configuration (Save & Load) -->
    <div class="flex justify-between items-center gap-4 mt-2">
      <!-- Input for filter name -->
      <input
        v-model="filterNameInput"
        type="text"
        placeholder="Enter Preset Filter Name"
        class="border border-gray-300 rounded px-3 py-1 text-sm"
      />

      <!-- Button to save current filter configuration -->
      <button
        @click="saveFilterConfiguration"
        class="bg-gray-300 text-gray rounded text-[10px] px-2 py-0.5"
      >
        Save Configuration
      </button>

      <!-- Dropdown to load saved configurations -->
      <select
        v-model="selectedConfigName"
        @change="loadFilterConfiguration"
        class="border border-gray-300 rounded px-3 py-1 text-sm"
      >
        <option value="">Load Saved Configuration</option>
        <option
          v-for="config in savedConfigurations"
          :key="config.name"
          :value="config.name"
        >
          {{ config.name }}
        </option>
      </select>
    </div>
  </div>

  <!-- Main Table Section -->
  <div
    class="flex-grow overflow-y-auto shadow-lg pt-0 overflow-x-auto w-full"
    style="max-height: calc(100vh - 150px)"
  >
    <table
      class="w-full table-auto border border-collapse border-black rounded-lg"
    >
      <!-- Table Header -->
      <thead>
        <tr class="sticky top-0 z-20 bg-gray-700 text-white">
          <th
            v-for="header in headers"
            :key="header.key"
            @click="toggleSort(header.key)"
            class="text-sm border-b font-bold text-gray-100 hover:text-black cursor-pointer px-1 py-1 whitespace-nowrap justify-between"
          >
            <!-- Column Header with Sorting Arrow -->
            <span
              class="inline-flex items-center space-x-1 border bg-gray-700 text-white px-1 py-1 rounded w-full justify-between"
            >
              <span
                class="w-full text-left whitespace-nowrap overflow-hidden text-ellipsis"
              >
                {{ header.title }}
              </span>
              <!-- Sorting Arrow -->
              <span
                class="text-xs text-black bg-white px-1 py-0 rounded border border-black"
              >
                {{
                  sortKey === header.key
                    ? sortOrder === "asc"
                      ? "▲"
                      : "▼"
                    : "↕"
                }}
              </span>
            </span>
          </th>
        </tr>

        <!-- Filters Row Below Headers -->
        <tr class="sticky top-8 z-10 bg-gray-300">
          <th v-for="header in headers" :key="header.key" class="px-1 py-1">
            <!-- Use the OrderFilters component for each header -->
            <OrderFilters
              :headers="[header]"
              :filters="filters"
              :status-filter-options="statusFilterOptions"
              :days-since-order-ranges="daysSinceOrderRanges"
              :getUniqueOptions="getUniqueOptions"
              @select-all="selectAll"
              @clear-selection="clearSelection"
              @toggle-option="toggleOption"
            />
          </th>
        </tr>
      </thead>

      <!-- Table Body with Orders Data -->
      <tbody>
        <tr
          v-for="order in paginatedOrders"
          :key="order.oid"
          class="hover:bg-gray-50 border-b border-black"
        >
          <td
            v-for="header in headers"
            :key="header.key"
            class="px-1 py-1 text-left text-xs font-normal whitespace-nowrap"
          >
            <div class="bg-gray-50 border-gray-600 p-1 rounded">
              <!-- Order Status Column (Special Case for 'status' key) -->
              <div
                v-if="header.key === 'status'"
                class="flex flex-row items-start space-x-1 w-full"
              >
                <div
                  class="w-14 min-w-[56px] h-fit bg-gray-100 border mt-2 rounded border-gray-500 text-xs px-1 py-0.5 text-center leading-tight break-words flex items-center justify-center whitespace-normal"
                  style="height: 36px"
                >
                  {{ order.statusLeft }}
                </div>

                <div class="flex flex-col space-y-1 w-full">
                  <div class="flex space-x-1 items-center h-6">
                    <span
                      class="flex-1 h-full bg-gray-100 border border-gray-500 rounded px-2 text-xs flex items-center truncate"
                    >
                      {{ order.statusLeft }}
                    </span>

                    <span
                      class="w-6 h-6 flex items-center justify-center rounded border border-gray-500 bg-gray-100 text-xs text-gray-800"
                    >
                      L
                    </span>
                  </div>

                  <div class="flex space-x-1 items-center h-6">
                    <span
                      class="flex-1 h-full bg-gray-100 border border-gray-500 rounded px-2 text-xs flex items-center truncate"
                    >
                      {{ order.statusRight }}
                    </span>
                    <span
                      class="w-6 h-6 flex items-center justify-center rounded border border-gray-500 bg-gray-100 text-xs text-gray-800"
                    >
                      R
                    </span>
                  </div>
                </div>
              </div>

              <!-- Default Display for Other Columns -->
              <div v-else>
                {{ order[header.key] }}
              </div>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <!-- Pagination Controls -->
  <div
    class="sticky bottom-0 left-0 bg-white border-t px-4 py-2 flex justify-between items-center z-30"
  >
    <!-- Items per page dropdown -->
    <div class="flex items-center gap-2">
      <label for="itemsPerPage" class="text-sm">Items per page:</label>
      <select
        v-model="itemsPerPage"
        id="itemsPerPage"
        class="border rounded p-1 text-sm"
        style="padding: 0.15rem 0.4rem; font-size: 0.85rem"
      >
        <option v-for="option in [5, 10, 20, 30]" :key="option" :value="option">
          {{ option }}
        </option>
      </select>
    </div>

    <!-- Page Navigation -->
    <div class="flex items-center space-x-2">
      <button
        @click="previousPage"
        :disabled="currentPage === 1"
        class="border px-1 py-0.5 bg-gray-300 text-gray-700 text-xs"
        style="padding: 0.25rem 0.5rem; font-size: 0.85rem"
      >
        Previous
      </button>
      <span class="text-xs">Page {{ currentPage }} of {{ totalPages }}</span>
      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="border px-1 py-0.5 bg-gray-300 text-gray-700 text-xs"
        style="padding: 0.25rem 0.5rem; font-size: 0.85rem"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script>
import mockOrders from "../data/mockOrders.json";
import OrderFilters from "./OrderFilters.vue";

export default {
  components: {
    OrderFilters, // OrderFilters component
  },
  data() {
    return {
      // Orders Data
      orders: mockOrders,
      filters: {},

      // Sorting Data
      sortKey: "",
      sortOrder: "asc",

      // Pagination Data
      currentPage: 1,
      itemsPerPage: 10,

      // Filter Configurations
      savedConfigurations: [],
      filterNameInput: "",
      selectedConfigName: "",

      // Table Headers Configuration
      headers: [
        { title: "OID", key: "oid" },
        { title: "Status", key: "status" },
        { title: "Type", key: "type" },
        { title: "Lock", key: "lock" },
        { title: "Customer", key: "customer" },
        { title: "Days since order", key: "daysSinceOrder" },
        { title: "Model", key: "model" },
        { title: "Designer", key: "designer" },
      ],
    };
  },
  created() {
    // filters for each header key
    this.headers.forEach((header) => {
      this.filters[header.key] = [];
    });
  },

  computed: {
    statusFilterOptions() {
      const uniqueStatuses = new Set();
      this.orders.forEach((order) => {
        if (order.statusLeft) uniqueStatuses.add(order.statusLeft.trim());
        if (order.statusRight) uniqueStatuses.add(order.statusRight.trim());
      });
      return Array.from(uniqueStatuses);
    },

    // Filter Logic for Orders
    filteredOrders() {
      return this.orders.filter((order) =>
        Object.keys(this.filters).every((key) => {
          if (this.filters[key].length === 0) return true;

          if (key === "status") {
            return this.filters.status.some(
              (val) => val === order.statusLeft || val === order.statusRight
            );
          }

          if (key === "daysSinceOrder") {
            // Filter based on selected range like <5, <10, etc.
            return this.filters[key].some((range) => {
              const rangeValue = parseInt(range.replace("<", ""));
              return order[key] < rangeValue;
            });
          }

          return this.filters[key].includes(order[key]);
        })
      );
    },

    // Sort the Filtered Orders
    filteredSortedOrders() {
      const data = this.filteredOrders;
      if (!this.sortKey) return data;
      return [...data].sort((a, b) => {
        const valueA = a[this.sortKey];
        const valueB = b[this.sortKey];
        if (valueA < valueB) return this.sortOrder === "asc" ? -1 : 1;
        if (valueA > valueB) return this.sortOrder === "asc" ? 1 : -1;
        return 0;
      });
    },

    // daysSinceOrder values and sort them
    daysSinceOrderRanges() {
      const uniqueDays = [
        ...new Set(this.orders.map((order) => order.daysSinceOrder)),
      ].sort((a, b) => a - b);

      const ranges = [];
      let prev = 0;
      const step = 5; // Define the step size (e.g., 5, 10, 20)

      for (let i = step; i <= Math.max(...uniqueDays); i += step) {
        ranges.push(`<${i}`);
      }

      return ranges;
    },

    // Total Pages for Pagination
    totalPages() {
      return Math.ceil(this.filteredSortedOrders.length / this.itemsPerPage);
    },

    // Paginated Orders to Display
    paginatedOrders() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.filteredSortedOrders.slice(start, end);
    },
  },

  methods: {
    // Toggle Sorting for a Column
    toggleSort(key) {
      if (this.sortKey === key) {
        this.sortOrder = this.sortOrder === "asc" ? "desc" : "asc";
      } else {
        this.sortKey = key;
        this.sortOrder = "asc";
      }
    },

    // Toggle a Filter Option
    toggleOption(key, value) {
      const current = this.filters[key];
      if (key === "daysSinceOrder") {
        if (current.includes(value)) {
          this.filters[key] = current.filter((v) => v !== value);
        } else {
          this.filters[key] = [...current, value];
        }
      } else {
        this.filters[key] = current.includes(value)
          ? current.filter((v) => v !== value)
          : [...current, value];
      }
      this.sortKey = key;
      this.sortOrder = "asc";
    },

    // Save the Current Filter Configuration
    saveFilterConfiguration() {
      if (!this.filterNameInput.trim()) {
        alert("Enter a name for the filter configuration.");
        return;
      }
      this.savedConfigurations.push({
        name: this.filterNameInput,
        filters: JSON.parse(JSON.stringify(this.filters)), // Deep Copy Filters
      });
      this.filterNameInput = "";
    },

    // Load a Saved Filter Configuration
    loadFilterConfiguration() {
      const config = this.savedConfigurations.find(
        (c) => c.name === this.selectedConfigName
      );
      if (config) {
        this.filters = JSON.parse(JSON.stringify(config.filters)); // Deep Copy Filters
      }
    },

    // Unique Options for a Given Header Key
    getUniqueOptions(key) {
      return [...new Set(this.orders.map((order) => order[key] || ""))].sort();
    },

    // Select All Options for a Filter Key
    selectAll(key) {
      if (key === "status") {
        this.filters[key] = [...this.statusFilterOptions];
      } else {
        this.filters[key] = this.getUniqueOptions(key);
      }
    },

    // Clear All Selections for a Filter Key
    clearSelection(key) {
      this.filters[key] = [];
    },

    // Navigates to the previous page
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },

    // Navigates to the next page
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
  },
};
</script>
