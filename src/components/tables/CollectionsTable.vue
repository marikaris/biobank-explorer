<template>
  <table class="table table-condensed table-responsive">
    <thead>
    <tr>
      <th>Collection</th>
      <th>Type</th>
      <th>Materials</th>
      <th>Standards</th>
      <th>#Samples</th>
    </tr>
    </thead>
    <tbody>
    <template v-for="collection in topLevelElements">
      <tr>
        <td
          :class="{'table-text-content-columns-has-sub': hasSubCollections(collection), 'table-text-content-columns': !hasSubCollections(collection)}"
          v-for="column in columns">
              <span v-if="column === 'name'">
                <a :href="'/menu/main/dataexplorer/details/eu_bbmri_eric_collections/' + collection.id">{{collection[column]}}</a>
              </span>
              <span v-else-if="column === 'quality'">
              <quality-column :qualities="collection[column]" :spacing=0></quality-column>
              </span>

          <span v-else-if="column === 'type'">{{ getCollectionType(collection) }}</span>
          <span v-else-if="column === 'materials'">{{ getCollectionMaterials(collection) }}</span>
          <span v-else-if="column === 'size'">{{ getCollectionSize(collection) }}</span>
        </td>
      </tr>
      <tr v-if="hasSubCollections(collection)">
        <td colspan="5" class="sub-table-cell">
          <sub-collections-table :subCollections="collection.sub_collections"></sub-collections-table>
        </td>
      </tr>
    </template>
    </tbody>
  </table>
</template>

<style>
  .table-text-content-columns {
    font-size: 13px;
    font-weight: bold;
  }

  .table-text-content-columns-has-sub {
    font-size: 13px;
    font-weight: bold;
    border-style: hidden;
    border-width: 0px;
  }

  .sub-table-cell {
    padding-top: 0px;
  }
</style>

<script>
  import utils from '../../utils'
  import SubCollectionsTable from './SubCollectionsTable'
  import QualityColumn from './QualityColumn'

  export default {
    name: 'collections-table',
    props: {
      collections: {
        type: Array,
        required: true
      },
      isSubCollectionsTableVisible: {
        type: Boolean,
        required: false,
        default: false
      }
    },
    computed: {
      topLevelElements () {
        return this.collections.filter(collection => !collection.parent_collection)
      }
    },
    data () {
      return {
        columns: ['name', 'type', 'materials', 'quality', 'size'],
        quality_logo: {height: 4, width: 9}
      }
    },
    methods: {
      getCollectionMaterials (collection) {
        return utils.getUniqueIdArray(collection.materials.map(material => material.label)).join(', ')
      },
      getCollectionType (collection) {
        return utils.getUniqueIdArray(collection.type.map(type => type.label)).join(', ')
      },
      hasSubCollections (collection) {
        return collection.sub_collections.length > 0
      },
      getCollectionSize (collection) {
        return collection.size || collection.order_of_magnitude.size
      }
    },
    components: {
      SubCollectionsTable, QualityColumn
    }
  }
</script>
