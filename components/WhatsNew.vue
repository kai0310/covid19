<template>
  <div class="WhatsNew">
    <div class="WhatsNew-heading">
      <h3 class="WhatsNew-title">
        <v-icon size="2.4rem" class="WhatsNew-title-icon">
          mdi-information
        </v-icon>
        {{ $t('最新のお知らせ') }}
      </h3>
      <div class="WhatsNew-linkGroup">
        <link-to-information-about-emergency-measure v-if="isEmergency" />
      </div>
    </div>
    <ul class="WhatsNew-list">
      <li v-for="(item, i) in listItems" :key="i" class="WhatsNew-list-item">
        <nuxt-link
          v-if="item.url && isInternalLink(item.url)"
          class="WhatsNew-list-item-anchor"
          :to="localePath(item.url)"
        >
          <time
            class="WhatsNew-list-item-anchor WhatsNew-list-item-anchor-time px-2"
            :datetime="formattedDate(item.date)"
          >
            {{ formattedDateForDisplay(item.date) }}
          </time>
          <span class="WhatsNew-list-item-anchor-link">
            {{ $t(item.text) }}
          </span>
        </nuxt-link>
        <a
          v-else-if="item.url"
          class="WhatsNew-list-item-anchor"
          :href="item.url"
          target="_blank"
          rel="noopener noreferrer"
        >
          <time
            class="WhatsNew-list-item-anchor WhatsNew-list-item-anchor-time px-2"
            :datetime="formattedDate(item.date)"
          >
            {{ formattedDateForDisplay(item.date) }}
          </time>
          <span class="WhatsNew-list-item-anchor-link">
            {{ $t(item.text) }}
            <v-icon
              v-if="!isInternalLink(item.url)"
              class="WhatsNew-item-ExternalLinkIcon"
              size="1.2rem"
            >
              mdi-open-in-new
            </v-icon>
          </span>
        </a>
        <div v-else class="WhatsNew-list-item-anchor">
          <time
            class="WhatsNew-list-item-anchor WhatsNew-list-item-anchor-time px-2"
            :datetime="formattedDate(item.date)"
          >
            {{ formattedDateForDisplay(item.date) }}
          </time>
          <span>{{ $t(item.text) }}</span>
        </div>
      </li>
      <div
        v-if="items.length - showItems > 0"
        class="WhatsNew-Button"
        @click="isMore"
      >
        <span>
          {{ $t('もっと見る') }}
        </span>
      </div>
    </ul>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import LinkToInformationAboutEmergencyMeasure from '@/components/LinkToInformationAboutEmergencyMeasure.vue'

import { convertDateToISO8601Format } from '@/utils/formatDate'

export default Vue.extend({
  components: {
    LinkToInformationAboutEmergencyMeasure
  },
  props: {
    items: {
      type: Array,
      required: true
    },
    showItems: {
      type: Number,
      required: false,
      default: 3
    },
    isEmergency: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  computed: {
    listItems() {
      const list = this.items
      return list.slice(0, this.showItems)
    }
  },
  methods: {
    isInternalLink(path: string): boolean {
      return !/^https?:\/\//.test(path)
    },
    formattedDate(dateString: string) {
      return convertDateToISO8601Format(dateString)
    },
    formattedDateForDisplay(dateString: string) {
      return this.$d(new Date(dateString), 'date')
    },
    isMore() {
      // TypeScriptのread-only propertyエラーを回避
      Object.assign(this, { showItems: this.showItems + 3 })
    }
  }
})
</script>

<style lang="scss">
.WhatsNew {
  @include card-container();

  padding: 10px;
  margin-bottom: 20px;

  .WhatsNew-Button {
    margin: 15px;
    flex: 1 0 auto;
    text-align: left;
    > span {
      @include button-text('sm');
    }

    @include lessThan($small) {
      margin-top: 4px;
    }
  }

  .WhatsNew-heading {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    margin-bottom: 12px;

    .WhatsNew-title {
      display: flex;
      align-items: center;
      color: $gray-2;
      @include card-h2();
      &-icon {
        margin: 3px;
      }
    }

    .WhatsNew-linkGroup {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      justify-content: flex-end;

      @include lessThan($medium) {
        justify-content: flex-start;
      }
    }
  }

  .WhatsNew-list {
    padding-left: 0;
    list-style-type: none;

    &-item {
      &-anchor {
        text-decoration: none;
        margin: 5px;
        @include font-size(14);

        @include lessThan($medium) {
          display: flex;
          flex-wrap: wrap;
        }

        &-time {
          flex: 0 0 90px;

          @include lessThan($medium) {
            flex: 0 0 100%;
          }

          color: $gray-1;
        }

        &-link {
          flex: 0 1 auto;

          @include text-link();

          @include lessThan($medium) {
            padding-left: 8px;
          }
        }

        &-ExternalLinkIcon {
          margin-left: 2px;
          color: $gray-3 !important;
        }
      }
    }
  }
}
</style>
