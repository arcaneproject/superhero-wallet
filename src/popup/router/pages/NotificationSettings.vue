<template>
  <div class="popup notification-settings">
    <div class="header">
      {{ $t('pages.notification-settings.header') }}
    </div>
    <CheckBox
      v-for="setting in notificationSettings"
      :key="setting.type"
      :class="['iframe', { unchecked: !setting.checked }]"
      :disabled="setting.type === 'wallet'"
      v-model="setting.checked"
    >
      {{ setting.text }}
    </CheckBox>
  </div>
</template>

<script>
import { mapState } from 'vuex';
import CheckBox from '../components/CheckBox';

export default {
  components: { CheckBox },
  computed: mapState(['notificationSettings']),
};
</script>

<style lang="scss" scoped>
@import '../../../common/variables';

.popup.notification-settings {
  padding: 0;
  font-size: 0.95rem;

  .header,
  .checkbox-container {
    text-align: left;
    align-items: center;
    padding: 1.5rem 0.7rem;
    border-bottom: 2px solid #1c1c24;

    &.unchecked {
      color: $text-color;
    }
  }

  .header {
    padding: 0.75rem;
    color: $gray-1;
    font-size: 1.05rem;
  }
}
</style>
