<template>
  <img
    :src="
      typeof profileImage === 'string' && profileImage.length > 0 && !error
        ? profileImage
        : avatar.src
    "
    :class="[size, avatar.type === 'identicon' ? 'user-identicon' : 'user-avatar']"
    @error="error = true"
  />
</template>

<script>
import { mapGetters } from 'vuex';

export default {
  props: {
    address: String,
    name: [String, Boolean],
    size: {
      type: String,
      default: 'normal',
    },
    identicon: Boolean,
  },
  data: () => ({
    error: false,
  }),
  computed: {
    ...mapGetters(['getProfileImage', 'getIdentIconOrAvatar']),
    profileImage() {
      return this.getProfileImage(this.address);
    },
    avatar() {
      return this.name
        ? { src: this.getIdentIconOrAvatar(this.name), type: 'avatar' }
        : { src: this.getIdentIconOrAvatar(this.address), type: 'identicon' };
    },
  },
};
</script>

<style lang="scss" scoped>
$x-small-size: 20px;
$small-size: 30px;
$normal-size: 38px;
$lg-size: 64px;

.user-avatar,
.user-identicon {
  width: $normal-size;
  height: $normal-size;
  border-radius: 50%;
  overflow: hidden;
  display: inline-block;

  &.lg {
    height: $lg-size;
    width: $lg-size;
  }

  &.small {
    height: $small-size;
    width: $small-size;
  }

  &.x-small {
    height: $x-small-size;
    width: $x-small-size;
  }
}
</style>
