<template>
  <div>
    <h5><router-link :to='"/collection/"+collection.id'>{{collection.name}}</router-link></h5>
    <report-description :description="collection.description" :maxLength="200"></report-description>
    <report-details-list :reportDetails="collection.content"></report-details-list>
    <div v-if="collection.subCollections.length" class="m-3">
      <h6>Sub collections</h6>
      <report-sub-collection v-for="subCollection in collection.subCollections" :collection="subCollection" :level="1"
                             :key="subCollection.id"></report-sub-collection>
    </div>
  </div>
</template>

<script>
import ReportDescription from '../report-components/ReportDescription.vue'
import ReportDetailsList from '../report-components/ReportDetailsList.vue'
import ReportSubCollection from './ReportSubCollection.vue'

export default {
  name: 'ReportCollection',
  components: { ReportDescription, ReportDetailsList, ReportSubCollection },
  props: {
    collection: {
      description: String,
      parentCollection: Object,
      subCollections: Array,
      name: String,
      id: String,
      content: {
        stringValues: Object,
        listValues: {
          order_of_magnitude: {
            values: Array,
            badgeColor: String
          },
          Materials: {
            values: Array,
            badgeColor: String
          },
          Data: {
            values: Array,
            badgeColor: String
          }
        }
      }
    }
  }
}
</script>
