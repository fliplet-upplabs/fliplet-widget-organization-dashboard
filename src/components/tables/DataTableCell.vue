<template>
  <div>
    <span v-if="cellType === 'date'">{{ this.transformDate(cellValue) }}</span>
    <div v-else-if="cellType === 'dynamic'" class="multiline-cell">
      <p>{{ cellValue[0] }}</p>
      <small v-if="cellValue[0] === cellValue[1]">({{ perсent }})</small>
      <small v-else-if="cellValue[0] > cellValue[1]" class="text-success">(&#8593;{{ this.perсent }})</small>
      <small v-else class="text-danger">(&#8595;{{ this.perсent }})</small>
      <small>
        {{ cellValue[1] }}
      </small>
      <small>
        Previous Period
      </small>
    </div>
    <span v-else-if="cellType === 'action'" @click.stop="onCellAction(cellValue)" class="link">{{ cellValue.title }}</span>
    <span v-else>
      {{ cellValue }}
    </span>
  </div>
</template>

<script>
import { calculateDynamic } from '../../services/analytics';

export default {
  data() {
    return {
      perсent: 0
    };
  },
  props: {
    cellValue: {
      type: Array,
      default() {
        return [];
      }
    },
    cellType: {
      type: String,
      default: 'raw'
    }
  },
  methods: {
    transformDate: function(date) {
      if (date === null) {
        return '—';
      }

      return moment(date).format('D MMM YYYY');
    },
    openUserProfile: function(options) {
      Fliplet.Studio.emit('overlay', {
        name: 'edit-organization-user',
        options: {
          size: 'large',
          title: 'Edit User',
          userId: options.userId
        }
      });
    },
    openAppAnalytics: function(options) {
      Fliplet.Studio.emit('overlay', {
        name: 'app-analytics',
        options: {
          size: 'large',
          title: options.title,
          appId: options.appId
        }
      });
    },
    onCellAction: function(options) {
      if ('appId' in options) {
        this.openAppAnalytics(options);
      } else {
        this.openUserProfile(options);
      }
    }
  },
  mounted: function() {
    if (this.cellType === 'dynamic') {
      this.perсent = calculateDynamic(this.cellValue[0], this.cellValue[1]);
    }
  }
};
</script>
