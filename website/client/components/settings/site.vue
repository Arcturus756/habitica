<template lang="pug">
  .row.standard-page
    restore-modal
    reset-modal
    delete-modal
    h1.col-12 {{ $t('settings') }}
    .col-6
      .form-horizontal
        h5 {{ $t('language') }}
        select.form-control(:value='user.preferences.language',
          @change='changeLanguage($event)')
          option(v-for='lang in availableLanguages', :value='lang.code') {{lang.name}}

        small
          | {{ $t('americanEnglishGovern') }}
          br
          strong(v-html="$t('helpWithTranslation')")
      hr

      .form-horizontal
        h5 {{ $t('dateFormat') }}
        select.form-control(v-model='user.preferences.dateFormat',
          @change='set("dateFormat")')
          option(v-for='dateFormat in availableFormats', :value='dateFormat') {{dateFormat}}
      hr

      .form-horizontal
        h5 {{ $t('audioTheme') }}
        select.form-control(v-model='user.preferences.sound',
          @change='set("sound")')
          option(v-for='sound in availableAudioThemes', :value='sound') {{ $t(`audioTheme_${sound}`) }}
      hr

      .form-horizontal(v-if='user.flags.classSelected && !user.preferences.disableClasses')
        h5 {{ $t('characterBuild') }}
        h6(v-once) {{ $t('class') + ': ' }}
          span {{ classText }}&nbsp;
          button.btn.btn-danger.btn-xs(@click='changeClass(null)', v-once) {{ $t('changeClass') }}
          small.cost 3
            span.Pet_Currency_Gem1x.inline-gems
      hr

      div
        .checkbox
          label
            input(type='checkbox', @click='hideHeader() ', v-model='user.preferences.hideHeader')
            span.hint(popover-trigger='mouseenter', popover-placement='right', :popover="$t('showHeaderPop')") {{ $t('showHeader') }}
        .checkbox
          label
            input(type='checkbox', @click='toggleStickyHeader()', v-model='user.preferences.stickyHeader', :disabled="user.preferences.hideHeader")
            span.hint(popover-trigger='mouseenter', popover-placement='right', :popover="$t('stickyHeaderPop')") {{ $t('stickyHeader') }}
        .checkbox
          label
            input(type='checkbox', v-model='user.preferences.newTaskEdit', @click='set("newTaskEdit")')
            span.hint(popover-trigger='mouseenter', popover-placement='right', :popover="$t('newTaskEditPop')") {{ $t('newTaskEdit') }}
        .checkbox
          label
            input(type='checkbox', v-model='user.preferences.tagsCollapsed', @change='set("tagsCollapsed")')
            span.hint(popover-trigger='mouseenter', popover-placement='right', :popover="$t('startCollapsedPop')") {{ $t('startCollapsed') }}
        .checkbox
          label
            input(type='checkbox', v-model='user.preferences.advancedCollapsed', @change='set("advancedCollapsed")')
            span.hint(popover-trigger='mouseenter', popover-placement='right', :popover="$t('startAdvCollapsedPop')") {{ $t('startAdvCollapsed') }}
        .checkbox
          label
            input(type='checkbox', v-model='user.preferences.dailyDueDefaultView', @change='set("dailyDueDefaultView")')
            span.hint(popover-trigger='mouseenter', popover-placement='right', :popover="$t('dailyDueDefaultViewPop')") {{ $t('dailyDueDefaultView') }}
        .checkbox(v-if='party.memberCount === 1')
          label
            input(type='checkbox', v-model='user.preferences.displayInviteToPartyWhenPartyIs1', @change='set("displayInviteToPartyWhenPartyIs1")')
            span.hint(popover-trigger='mouseenter', popover-placement='right', :popover="$t('displayInviteToPartyWhenPartyIs1')") {{ $t('displayInviteToPartyWhenPartyIs1') }}
        .checkbox
          input(type='checkbox', v-model='user.preferences.suppressModals.levelUp', @change='set("suppressModals", "levelUp")')
          label {{ $t('suppressLevelUpModal') }}
        .checkbox
          input(type='checkbox', v-model='user.preferences.suppressModals.hatchPet', @change='set("suppressModals", "hatchPet")')
          label {{ $t('suppressHatchPetModal') }}
        .checkbox
          input(type='checkbox', v-model='user.preferences.suppressModals.raisePet', @change='set("suppressModals", "raisePet")')
          label {{ $t('suppressRaisePetModal') }}
        .checkbox
          input(type='checkbox', v-model='user.preferences.suppressModals.streak', @change='set("suppressModals", "streak")')
          label {{ $t('suppressStreakModal') }}
        //- .checkbox
        //-   label {{ $t('confirmScoreNotes') }}
        //-     input(type='checkbox', v-model='user.preferences.tasks.confirmScoreNotes', @change='set({"preferences.tasks.confirmScoreNotes": user.preferences.tasks.confirmScoreNotes ? true: false})')

        //- .checkbox
        //-   label {{ $t('groupTasksByChallenge') }}
        //-     input(type='checkbox', v-model='user.preferences.tasks.groupByChallenge', @change='set({"preferences.tasks.groupByChallenge": user.preferences.tasks.groupByChallenge ? true: false})')

        hr

        button.btn.btn-primary(@click='showBailey()', popover-trigger='mouseenter', popover-placement='right', :popover="$t('showBaileyPop')") {{ $t('showBailey') }}
        button.btn.btn-primary(@click='openRestoreModal()', popover-trigger='mouseenter', popover-placement='right', :popover="$t('fixValPop')") {{ $t('fixVal') }}
        button.btn.btn-primary(v-if='user.preferences.disableClasses == true', @click='changeClass({})',
          popover-trigger='mouseenter', popover-placement='right', :popover="$t('enableClassPop')") {{ $t('enableClass') }}

        hr

        div
          h5 {{ $t('customDayStart') }}
          .alert.alert-warning {{ $t('customDayStartInfo1') }}
          .form-horizontal
            .form-group
              .col-7
                select.form-control(v-model='newDayStart')
                  option(v-for='option in dayStartOptions' :value='option.value') {{option.name}}

              .col-5
                button.btn.btn-block.btn-primary(@click='openDayStartModal()',
                  :disabled='newDayStart === user.preferences.dayStart')
                  | {{ $t('saveCustomDayStart') }}
          hr

        h5 {{ $t('timezone') }}
        .form-horizontal
          .form-group
            .col-12
              p(v-html="$t('timezoneUTC', {utc: timezoneOffsetToUtc})")
              p(v-html="$t('timezoneInfo')")

    .col-6
      h2 {{ $t('registration') }}
      .panel-body
        div
          ul.list-inline
            li(v-for='network in SOCIAL_AUTH_NETWORKS')
              button.btn.btn-primary(v-if='!user.auth[network.key].id', @click='socialLogin(network.key, user)') {{ $t('registerWithSocial', {network: network.name}) }}
              button.btn.btn-primary(disabled='disabled', v-if='!hasBackupAuthOption(network.key) && user.auth[network.key].id') {{ $t('registeredWithSocial', {network: network.name}) }}
              button.btn.btn-danger(@click='deleteSocialAuth(network.key)', v-if='hasBackupAuthOption(network.key) && user.auth[network.key].id') {{ $t('detachSocial', {network: network.name}) }}
          // hr
          // TODO
          // div(v-if='!user.auth.local.username')
            p {{ $t('addLocalAuth') }}
            form(ng-submit='http("post", "/api/v3/user/auth/local/register", localAuth, "addedLocalAuth")', name='localAuth', novalidate)
              //-.alert.alert-danger(ng-messages='changeUsername.$error && changeUsername.submitted') {{ $t('fillAll') }}
              .form-group
                input.form-control(type='text', placeholder="$t('username')", v-model='localAuth.username', required)
              .form-group
                input.form-control(type='text', placeholder="$t('email')", v-model='localAuth.email', required)
              .form-group
                input.form-control(type='password', placeholder="$t('password')", v-model='localAuth.password', required)
              .form-group
                input.form-control(type='password', placeholder="$t('confirmPass')", v-model='localAuth.confirmPassword', required)
              button.btn.btn-primary(type='submit', ng-disabled='localAuth.$invalid', value="$t('submit')")

        .usersettings(v-if='user.auth.local.username')
          p {{ $t('username') }}
            |: {{user.auth.local.username}}
          p
            small.muted
                | {{ $t('loginNameDescription') }}
          p {{ $t('email') }}
            |: {{user.auth.local.email}}
          hr

          h5 {{ $t('changeUsername') }}
          .form(v-if='user.auth.local', name='changeUsername', novalidate)
            //-.alert.alert-danger(ng-messages='changeUsername.$error && changeUsername.submitted') {{ $t('fillAll') }}
            .form-group
              input.form-control(type='text', :placeholder="$t('newUsername')", v-model='usernameUpdates.username')
            .form-group
              input.form-control(type='password', :placeholder="$t('password')", v-model='usernameUpdates.password')
            button.btn.btn-primary(type='submit', @click='changeUser("username", usernameUpdates)') {{ $t('submit') }}

          h5 {{ $t('changeEmail') }}
          .form(v-if='user.auth.local', name='changeEmail', novalidate)
            .form-group
              input.form-control(type='text', :placeholder="$t('newEmail')", v-model='emailUpdates.newEmail')
            .form-group
              input.form-control(type='password', :placeholder="$t('password')", v-model='emailUpdates.password')
            button.btn.btn-primary(type='submit', @click='changeUser("email", emailUpdates)') {{ $t('submit') }}

          h5 {{ $t('changePass') }}
          .form(v-if='user.auth.local', name='changePassword', novalidate)
            .form-group
              input.form-control(type='password', :placeholder="$t('oldPass')", v-model='passwordUpdates.password')
            .form-group
              input.form-control(type='password', :placeholder="$t('newPass')", v-model='passwordUpdates.newPassword')
            .form-group
              input.form-control(type='password', :placeholder="$t('confirmPass')", v-model='passwordUpdates.confirmPassword')
            button.btn.btn-primary(type='submit', @click='changeUser("password", passwordUpdates)') {{ $t('submit')  }}

          div
            h5 {{ $t('dangerZone') }}
            div
              button.btn.btn-danger(@click='openResetModal()',
                popover-trigger='mouseenter', popover-placement='right', :popover="$t('resetAccPop')") {{ $t('resetAccount') }}
              button.btn.btn-danger(@click='openDeleteModal()',
                popover-trigger='mouseenter', :popover="$t('deleteAccPop')") {{ $t('deleteAccount') }}
</template>

<style scoped>
  .usersettings h5 {
    margin-top: 1em;
  }
</style>

<script>
import hello from 'hellojs';
import moment from 'moment';
import axios from 'axios';
import { mapState } from 'client/libs/store';

import restoreModal from './restoreModal';
import resetModal from './resetModal';
import deleteModal from './deleteModal';
import { SUPPORTED_SOCIAL_NETWORKS } from '../../../common/script/constants';
import changeClass from  '../../../common/script/ops/changeClass';
// @TODO: this needs our window.env fix
// import { availableLanguages } from '../../../server/libs/i18n';

export default {
  components: {
    restoreModal,
    resetModal,
    deleteModal,
  },
  data () {
    let dayStartOptions = [];
    for (let number = 0; number < 24; number += 1) {
      let meridian = number < 12 ? 'AM' : 'PM';
      let hour = number % 12;
      let option = {
        value: number,
        name: `${hour ? hour : 12}:00 ${meridian}`,
      };
      dayStartOptions.push(option);
    }

    return {
      SOCIAL_AUTH_NETWORKS: [],
      party: {},
      // Made available by the server as a script
      availableFormats: ['MM/dd/yyyy', 'dd/MM/yyyy', 'yyyy/MM/dd'],
      dayStartOptions,
      newDayStart: 0,
      usernameUpdates: {},
      emailUpdates: {},
      passwordUpdates: {},
    };
  },
  mounted () {
    this.SOCIAL_AUTH_NETWORKS = SUPPORTED_SOCIAL_NETWORKS;
    // @TODO: We may need to request the party here
    this.party = this.$store.state.party;
    this.newDayStart = this.user.preferences.dayStart;
  },
  computed: {
    ...mapState({
      user: 'user.data',
      availableLanguages: 'i18n.availableLanguages',
      content: 'content',
    }),
    availableAudioThemes () {
      return ['off', ...this.content.audioThemes];
    },
    timezoneOffsetToUtc () {
      let offset = this.user.preferences.timezoneOffset;
      let sign = offset > 0 ? '-' : '+';

      offset = Math.abs(offset) / 60;

      let hour = Math.floor(offset);

      let minutesInt = (offset - hour) * 60;
      let minutes = minutesInt < 10 ? `0${minutesInt}` : minutesInt;

      return `UTC${sign}${hour}:${minutes}`;
    },
    dayStart () {
      return this.user.preferences.dayStart;
    },
  },
  methods: {
    set (preferenceType, subtype) {
      let settings = {};
      if (!subtype) {
        settings[`preferences.${preferenceType}`] = this.user.preferences[preferenceType];
      } else {
        settings[`preferences.${preferenceType}.${subtype}`] = this.user.preferences[preferenceType][subtype];
      }
      this.$store.dispatch('user:set', settings);
    },
    hideHeader () {
      this.set('hideHeader');
      if (!this.user.preferences.hideHeader || !this.user.preferences.stickyHeader) return;
      this.user.preferences.hideHeader = false;
      this.set('stickyHeader');
    },
    toggleStickyHeader () {
      this.set('stickyHeader');
    },
    showTour  () {
      // @TODO: Do we still use this?
      // User.set({'flags.showTour':true});
      // Guide.goto('intro', 0, true);
    },
    showBailey () {
      this.user.flags.newStuff = true;
      this.set('flags', 'newStuff');
    },
    hasBackupAuthOption (networkKeyToCheck) {
      if (this.user.auth.local.username) {
        return true;
      }

      return find(this.SOCIAL_AUTH_NETWORKS, (network) => {
        if (network.key !== networkKeyToCheck) {
          if (this.user.auth.hasOwnProperty(network.key)) {
            return this.user.auth[network.key].id;
          }
        }
      });
    },
    calculateNextCron () {
      let nextCron = moment().hours(this.newDayStart).minutes(0).seconds(0).milliseconds(0);

      let currentHour = moment().format('H');
      if (currentHour >= this.newDayStart) {
        nextCron = nextCron.add(1, 'day');
      }

      return nextCron.format('x');
    },
    openDayStartModal () {
      let nextCron = this.calculateNextCron();
      // @TODO: Add generic modal
      if (!confirm(`Are you sure you want to change cron? Next cron will be ${nextCron}`)) return;
      this.saveDayStart();
      // $rootScope.openModal('change-day-start', { scope: $scope });
    },
    async saveDayStart () {
      this.user.preferences.dayStart = this.newDayStart;
      await axios.post('/api/v3/user/custom-day-start', {
        dayStart: this.newDayStart,
      });
      // @TODO
      // Notification.text(response.data.data.message);
    },
    changeLanguage (e) {
      const newLang = e.target.value;
      this.user.preferences.language = newLang;
      this.set('language');
      window.location.href = '/';
    },
    async changeUser (attribute, updates) {
      await axios.put(`/api/v3/user/auth/update-${attribute}`, updates);
      alert(this.$t(`${attribute}Success`));
      this.user[attribute] = updates[attribute];
      updates = {};
    },
    openRestoreModal () {
      this.$root.$emit('show::modal', 'restore');
    },
    openResetModal () {
      this.$root.$emit('show::modal', 'reset');
    },
    openDeleteModal () {
      this.$root.$emit('show::modal', 'delete');
    },
    async deleteSocialAuth (networkKey) {
      // @TODO: What do we use this for?
      // let networktoRemove = find(SOCIAL_AUTH_NETWORKS, function (network) {
      //   return network.key === networkKey;
      // });

      await axios.get(`/api/v3/user/auth/social/${networkKey}`);
      // @TODO:
      // Notification.text(env.t("detachedSocial", {network: network.name}));
      // User.sync();
    },
    async socialAuth (network) {
      let auth = await hello(network).login({scope: 'email'});

      await this.$store.dispatch('auth:socialAuth', {
        auth,
      });

      this.$router.go('/tasks');
    },
    async changeClass () {
      if (!confirm('Are you sure you want to change your class for 3 gems?')) return;
      try {
        changeClass(this.user);
        await axios.post('/api/v3/user/change-class');
      } catch (e) {
        alert(e.message);
      }
    },
  },
};
</script>
