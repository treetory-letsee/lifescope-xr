<template>
    <div class="xr-help-menu-container"  :class="{ 'desktop-hud': !isMobile, 'mobile-menu': isMobile }">


        <div class="xr-help-menu" :style="helpStyleObject">

            <div class="help-controls">
                <ul class="desktop-controls" v-if="!isMobile">
                    <h2> Controls </h2>
                    <li> <b>H - </b> toggle help menu </li>

                    <li> <b>G - </b> toggle graphics settings menu </li>

                    <li> <b> WASD - </b> movement controls </li>

                    <li> <b> click and drag </b> to look around </li>

                    <li> <b> hover room name </b> to select new room </li>
                </ul>

                <ul class="mobile-controls" v-if="isMobile" >
                    <h2> Controls </h2>
                    <li> <b> swipe up/down </b> toggle help menu </li>

                    <!-- <li> <b> swipe up </b> toggle graphics settings menu </li> -->

                    <li> <b> joysticks at bottom of screen </b> movement controls </li>

                    <li> <b> rotate mobile device </b> to look around </li>

                </ul>
            </div>

        </div>
    </div> 

</template>

<script>
import { mapState } from 'vuex';

import HudUtils from './hudutils';

import { SceneLayoutEnum } from '../../store/modules/xr/index.js';

export default {

    data() {
        return {
            helpStyleObject: {
                visibility: 'hidden',//'visible',
                opacity: 0
            },
            SceneLayoutEnum: SceneLayoutEnum,
        }
    },

    computed: {
      ...mapState('xr',
      [
        'isMobile',
        'sceneLayout',
      ]),

      ...mapState('xr/hud', [
          'helpMenuVisible'
      ]),
    },

    watch: {
        helpMenuVisible: function (newVal, oldVal) {
            this.toggleHelpVisibility();
        },
    },

    mounted() {
        var self = this;
        self.hudUtils = new HudUtils();

        if (this.helpMenuVisible) {
            this.toggleHelpVisibility();
        }

        document.body.addEventListener('keypress', self.keypressListener);
    },

    beforeDestroy() {
        document.body.removeEventListener('keypress', this.keypressListener)
    },

    methods: {
        keypressListener(evt) {
            if (evt.target.tagName == 'BODY' && evt.key == 'h') {
                this.$store.commit('xr/hud/SET_HELP_MENU_ACTIVE', !this.helpMenuVisible);
            }
        },
        toggleHelpVisibility() {
            if (CONFIG.DEBUG) {console.log("toggleHelpVisibility");}
            this.hudUtils.toggleHud(this.helpStyleObject);
        },
        toggleLayout() {
            if (CONFIG.DEBUG) {console.log("toggleLayout");}
            var newVal = this.sceneLayout == SceneLayoutEnum.GRID ? 'GALLERY' : 'GRID';
            this.$store.commit('xr/SET_LAYOUT', newVal);
        },
    },
}
</script>

<style src="./HelpMenu.scss"></style>
