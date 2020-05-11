<template>
  <b-container
    class="py-3"
  >
    <c-content-header
      :title="$t('title')"
    />

    <b-card
      v-if="false"
    >
      // filter
    </b-card>

    <b-card
      no-body
      class="shadow-sm"
      header-bg-variant="white"
      footer-bg-variant="white"
    >
      <b-card-body
        class="p-0"
      >
        <b-table
          id="resource-list"
          responsive
          hover
          class="mb-0 small"
          head-variant="light"
          :items="items"
          :fields="fields"
          :tbody-tr-class="trClass"
        >
          <template #table-busy>
            <div class="text-center m-5">
              <div>
                <b-spinner
                  small
                  class="align-middle m-2"
                />
              </div>
              <div>{{ $t('loading') }}</div>
            </div>
          </template>
          <template #cell(timestamp)="{ item: e }">
            {{ e.timestamp | locLongDate }}
          </template>
          <template #cell(user)="{ item: e }">
            <span v-if="e.userID">
              {{ e.userName || e.userEmail || e.userID }}
            </span>
          </template>
          <template #cell(severityHandle)="row" />
        </b-table>
      </b-card-body>
    </b-card>
  </b-container>
</template>

<script>
import listHelpers from 'corteza-webapp-admin/src/mixins/listHelpers'

const defSeverity = 6
const severity = [
  { label: 'emergency',
    class: 'bg-danger' },
  { label: 'alert',
    class: 'bg-danger' },
  { label: 'critical',
    class: 'bg-danger' },
  { label: 'error',
    class: 'bg-danger' },
  { label: 'warning',
    class: 'bg-warning' },
  { label: 'notice',
    class: 'bg-success' },
  { label: 'info',
    class: 'bg-success' },
  { label: 'debug',
    class: '' },
]

export default {
  mixins: [
    listHelpers,
  ],

  i18nOptions: {
    namespaces: [ 'system.auditlog' ],
    keyPrefix: 'list',
  },

  data () {
    return {
      id: 'auditlog',

      filter: {
        query: '',
      },

      paging: {
        perPage: 100,
      },

      fields: [
        {
          key: 'severityHandle',
          label: '',
          tdClass: (v, k, item) => severity[item.severity].class,
        },
        {
          key: 'timestamp',
          tdClass: 'text-nowrap',
          // formatter: (v) => moment(v).fromNow(),
        },
        {
          key: 'description',
        },
        {
          key: 'target',
        },
        {
          key: 'user',
        },
        {
          key: 'event',
        },
      ].map(c => ({
        // Generate column label translation key
        label: this.$t(`columns.${c.key}`),
        ...c,
      })),
    }
  },

  methods: {
    items () {
      return this.procListResults(this.$SystemAPI.auditlogList(this.encodeListParams()))
    },

    trClass (item, type) {
      if (type === 'row') {
        const { severity: s = defSeverity } = item
        return severity[s].tdClass
      }
    },
  },
}
</script>
