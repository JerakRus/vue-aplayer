<template>
  <div class="aplayer-controller">
    <v-progress
      :loadProgress="loadProgress"
      :playProgress="playProgress"
      :theme="theme"
      @dragbegin="val => $emit('dragbegin', val)"
      @dragend="val => $emit('dragend', val)"
      @dragging="val => $emit('dragging', val)"
    />
    <div class="aplayer-time">
      <div class="aplayer-time-inner">
        <span class="aplayer-ptime" :style="{color: theme}">
          - {{secondToTime(stat.playedTime)}}
          / {{secondToTime(stat.duration)}}
        </span>
      </div>
      <volume
        v-if="!$parent.isMobile"
        :volume="volume"
        :theme="theme"
        :muted="muted"
        @togglemute="$emit('togglemute')"
        @setvolume="v => $emit('setvolume', v)"
      />
      <icon-button v-if="!chatMode"
        class="aplayer-icon-mode"
        icon="shuffle"
        :theme="theme"
        :class="{ 'inactive': !shuffle }"
        @click.native="$emit('toggleshuffle')"
      />
      <icon-button v-if="!chatMode"
        class="aplayer-icon-mode"
        :icon="repeat === 'repeat-one' ? 'repeat-one' : 'repeat-all'"
        :class="{ 'inactive': repeat === 'no-repeat'}"
        :theme="theme"
        @click.native="$emit('nextmode')"
      />
      <icon-button v-if="chatMode"
        class="aplayer-icon-mode"
        icon="download"
        :theme="theme"
        @click.native="$emit('downloadaudio')"
      />
      <icon-button
        class="aplayer-icon-menu"
        icon="menu"
        :theme="theme"
        :class="{ 'inactive': !$parent.showList }"
        @click.native="$emit('togglelist')"
      />
    </div>
  </div>
</template>

<script>
  import IconButton from './aplayer-iconbutton.vue'
  import VProgress from './aplayer-controller-progress.vue'
  import Volume from './aplayer-controller-volume.vue'

  export default {
    components: {
      IconButton,
      VProgress,
      Volume,
    },
    props: ['shuffle', 'repeat', 'stat', 'theme', 'volume', 'muted', 'chatMode'],
    computed: {
      loadProgress () {
        if (this.stat.duration === 0) return 0
        return this.stat.loadedTime / this.stat.duration
      },
      playProgress () {
        if (this.stat.duration === 0) return 0
        return this.stat.playedTime / this.stat.duration
      },
    },
    methods: {
      secondToTime (second) {
        if (isNaN(second)) {
          return '00:00'
        }
        const pad0 = (num) => {
          return num < 10 ? '0' + num : '' + num
        }

        const min = Math.trunc(second / 60)
        const sec = Math.trunc(second - min * 60)
        const hours = Math.trunc(min / 60)
        const minAdjust = Math.trunc((second / 60) - (60 * Math.trunc((second / 60) / 60)))
        return second >= 3600 ? pad0(hours) + ':' + pad0(minAdjust) + ':' + pad0(sec) : pad0(min) + ':' + pad0(sec)
      },
    }
  }
</script>

<style lang="scss">

  .aplayer-controller {
    display: flex;
    align-items: center;
    position: relative;

    .aplayer-time {
      display: flex;
      align-items: center;
      position: relative;
      height: 17px;
      color: #999;
      font-size: 11px;
      padding-left: 7px;

      .aplayer-volume-wrap {
        margin-left: 4px;
        margin-right: 4px;
      }

      .aplayer-icon {
        cursor: pointer;
        transition: all 0.2s ease;
        margin-left: 4px;

        &.inactive {
          opacity: .3;
        }

        .aplayer-fill {
          fill: #666;
        }

        &:hover {
          .aplayer-fill {
            fill: #000;
          }
        }

        &.aplayer-icon-menu {
          display: none;
        }
      }
      .aplayer-volume-wrap + .aplayer-icon {
        margin-left: 0;
      }

      &.aplayer-time-narrow {
        .aplayer-icon-mode {
          display: none;
        }

        .aplayer-icon-menu {
          display: none;
        }
      }
    }
  }

</style>
