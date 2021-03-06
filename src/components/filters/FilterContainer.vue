<template>
  <div id="filter-container">
    <string-filter
      label="Name / ID / acronym"
      v-model="search"
      :initiallyCollapsed="!$store.state.route.query.search"
      placeholder
      description="type a keyword, ID, or acronym and press enter"
    ></string-filter>
    <diagnosis-available-filters></diagnosis-available-filters>
    <checkbox-filters
      v-for="filter in filters"
      :key="filter.name"
      v-bind="filter"
      :value="filter.filters"
      @input="value => filterChange(filter.name, value)"
    />
  </div>
</template>

<style>
.filter-card {
  margin-bottom: 1rem;
}

.filter-header {
  /* Same color as BiobankCard */
  background-color: #f5f5f5;
}

.filter-header:hover {
  cursor: pointer;
}
</style>

<script>
import StringFilter from './StringFilter'
import DiagnosisAvailableFilters from './DiagnosisAvailableFilters.vue'
import { UPDATE_FILTER, SET_SEARCH } from '../../store/mutations'
import {
  GET_COUNTRY_OPTIONS,
  GET_MATERIALS_OPTIONS,
  GET_BIOBANK_TYPE_OPTIONS,
  GET_TYPES_OPTIONS,
  GET_DATA_TYPE_OPTIONS
} from '../../store/actions'
import { mapGetters, mapMutations } from 'vuex'
import CheckboxFilters from './CheckboxFilters'

export default {
  computed: {
    ...mapGetters({
      countryOptions: 'getCountryOptions',
      materialOptions: 'getMaterialOptions',
      sampleCollectionTypeOptions: 'getBiobankTypeOptions',
      typesOptions: 'getTypesOptions',
      dataTypeOptions: 'getDataTypeOptions',
      showCountryFacet: 'showCountryFacet'
    }),
    search: {
      get () {
        return this.$store.state.search
      },
      set (search) {
        const updatedRouteQuery = Object.assign(
          {},
          this.$store.state.route.query,
          { search: search.length === 0 ? undefined : search }
        )
        this.$router.push({ query: updatedRouteQuery })
        this.$store.commit(SET_SEARCH, search)
      }
    },
    filters () {
      return [
        {
          name: 'materials',
          label: 'Materials',
          options: this.materialOptions,
          initiallyCollapsed: !this.$store.state.route.query.materials,
          filters: this.$store.state.materials.filters,
          maxVisibleOptions: 25
        },
        {
          name: 'sampleCollectionType',
          label: 'Sample collection types',
          options: this.sampleCollectionTypeOptions,
          initiallyCollapsed: !this.$store.state.route.query.sampleCollectionType,
          filters: this.$store.state.sampleCollectionType.filters,
          maxVisibleOptions: 25
        },
        {
          name: 'dataType',
          label: 'Data types',
          options: this.dataTypeOptions,
          initiallyCollapsed: !this.$store.state.route.query.dataType,
          filters: this.$store.state.dataType.filters,
          maxVisibleOptions: 25
        }
        /* {
          name: 'country',
          label: 'Countries',
          options: this.countryOptions,
          initiallyCollapsed: !this.$store.state.route.query.country,
          filters: this.$store.state.country.filters
        }, */
      ].filter(facet => {
        // config option showCountryFacet is used to toggle Country facet
        return !(this.showCountryFacet === false && facet.name === 'country')
      })
    }
  },
  methods: {
    ...mapMutations({ updateFilter: UPDATE_FILTER }),
    filterChange (name, filters) {
      this.updateFilter({ name, filters })
      const value = filters.length === 0 ? undefined : filters.join(',')
      this.$router.push({
        query: { ...this.$store.state.route.query, [name]: value }
      })
    }
  },
  mounted () {
    this.$store.dispatch(GET_COUNTRY_OPTIONS)
    this.$store.dispatch(GET_MATERIALS_OPTIONS)
    this.$store.dispatch(GET_BIOBANK_TYPE_OPTIONS)
    this.$store.dispatch(GET_TYPES_OPTIONS)
    this.$store.dispatch(GET_DATA_TYPE_OPTIONS)
  },
  components: { StringFilter, CheckboxFilters, DiagnosisAvailableFilters }
}
</script>
