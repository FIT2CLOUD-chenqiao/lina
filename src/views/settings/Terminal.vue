<template>
  <IBox>
    <GenericCreateUpdateForm v-bind="$data" />
  </IBox>
</template>
<script>
import GenericCreateUpdateForm from '@/layout/components/GenericCreateUpdateForm'
import { IBox } from '@/components'
export default {
  name: 'Terminal',
  components: {
    GenericCreateUpdateForm,
    IBox
  },
  data() {
    return {
      fields: [
        [
          'KoKo',
          [
            'TERMINAL_PASSWORD_AUTH', 'TERMINAL_PUBLIC_KEY_AUTH',
            'TERMINAL_ASSET_LIST_SORT_BY', 'TERMINAL_ASSET_LIST_PAGE_SIZE',
            'TERMINAL_TELNET_REGEX'
          ]
        ],
        [
          'XRDP',
          [
            'XRDP_ENABLED', 'TERMINAL_RDP_ADDR'
          ]
        ]
      ],
      fieldsMeta: {
        TERMINAL_TELNET_REGEX: {
          type: 'input'
        },
        TERMINAL_RDP_ADDR: {
          hidden: () => {
            return !this.$store.getters.hasValidLicense
          }
        },
        XRDP_ENABLED: {
          hidden: () => {
            return !this.$store.getters.hasValidLicense
          },
          el: {
            hiddenGroup: true
          }
        }
      },
      url: '/api/v1/settings/setting/?category=terminal',
      hasDetailInMsg: false,
      submitMethod() {
        return 'put'
      },
      cleanFormValue(data) {
        Object.keys(data).forEach(
          function(key) {
            if (data[key] === null) {
              delete data[key]
            }
          }
        )
        return data
      }
    }
  },
  mounted() {
  },
  methods: {
  }
}
</script>

<style scoped>

</style>
