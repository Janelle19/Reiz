<template>
  <div>
    <div>
      <label for="sort-select">Sort by: </label>
      <select id="sort-select" v-model="sortOrder">
        <option value="asc">Name (A-Z)</option>
        <option value="desc">Name (Z-A)</option>
      </select>
    </div>
    <div>
      <label for="area-select">Filter by area compared to: </label>
      <select id="area-select" v-model="areaCompare">
        <option value="">No filter</option>
        <option v-for="country in countries" :value="country.area">{{ country.name }}</option>
      </select>
    </div>
    <div>
      <label for="area-size">and display countries that are: </label>
      <select id="area-size" v-model="areaSize">
        <option value="smaller">Smaller</option>
        <option value="bigger">Bigger</option>
      </select>
    </div>
    <div>
      <label for="region-select">Filter by region: </label>
      <select id="region-select" v-model="regionFilter">
        <option value="">All regions</option>
        <option v-for="region in regions" :value="region">{{ region }}</option>
      </select>
    </div>
    <ul>
      <li v-for="country in filteredCountries" :key="country.name">{{ country.name }} ({{ country.region }}) - {{ country.area }} km²</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      countries: [],
      sortOrder: 'asc',
      areaCompare: '',
      areaSize: 'smaller',
      regionFilter: ''
    }
  },
  computed: {
    filteredCountries() {
      let filtered = this.countries
      if (this.areaCompare) {
        filtered = filtered.filter(c => {
          if (this.areaSize === 'smaller') {
            return c.area < this.areaCompare
          } else {
            return c.area > this.areaCompare
          }
        })
      }
      if (this.regionFilter) {
        filtered = filtered.filter(c => c.region === this.regionFilter)
      }
      if (this.sortOrder === 'asc') {
        filtered.sort((a, b) => a.name.localeCompare(b.name))
      } else {
        filtered.sort((a, b) => b.name.localeCompare(a.name))
      }
      return filtered
    },
    regions() {
      const uniqueRegions = new Set(this.countries.map(c => c.region))
      return Array.from(uniqueRegions).sort()
    }
  },
  mounted() {
    fetch('https://restcountries.com/v2/all?fields=name,region,area')
      .then(response => response.json())
      .then(data => {
        this.countries = data
      })
  }
}
</script>