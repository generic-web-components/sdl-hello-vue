<template>
<div id="app">

  <app-header-layout>

    <app-header  >
      <app-toolbar>
        <paper-icon-button id="toggle" @click="toggleMenu" icon="menu"></paper-icon-button>
      </app-toolbar>
    </app-header>

    <app-drawer-layout ref="drawerlayout" @narrow-changed="narrowChanged" id="drawerLayout">

      <app-drawer slot="drawer" ref="drawer">
        <div class="drawer-content" ref="drawerContent">
          <paper-icon-item>
            <iron-icon icon="inbox" slot="item-icon"></iron-icon> <span>Inbox</span>
          </paper-icon-item>
          <paper-icon-item>
            <iron-icon icon="query-builder" slot="item-icon"></iron-icon> <span>Snoozed</span>
          </paper-icon-item>
          <paper-icon-item>
            <iron-icon icon="done" slot="item-icon"></iron-icon> <span>Done</span>
          </paper-icon-item>
          <paper-icon-item>
            <iron-icon icon="drafts" slot="item-icon"></iron-icon> <span>Drafts</span>
          </paper-icon-item>
          <paper-icon-item>
            <iron-icon icon="send" slot="item-icon"></iron-icon> <span>Sent</span>
          </paper-icon-item>
          <paper-icon-item>
            <iron-icon icon="delete" slot="item-icon"></iron-icon> <span>Trash</span>
          </paper-icon-item>
          <paper-icon-item>
            <iron-icon icon="report" slot="item-icon"></iron-icon> <span>Spam</span>
          </paper-icon-item>
        </div>
      </app-drawer>

      <div>

        <vaadin-tabs :selected="selectedPage" v-on:selected-changed="selectedPage=$event.detail.value">
          <vaadin-tab>All Contacts</vaadin-tab>
          <vaadin-tab>Add New</vaadin-tab>
        </vaadin-tabs>

        <iron-pages :selected="selectedPage">
        <div class="card">
          <sdl-srch-grid ref="grid" v-html="gridHtml" url="./data/srch-data-8000.txt">  
          </sdl-srch-grid>
        </div>

        <div class="card">
        <iron-form ref="form">
          <form>
            <vaadin-form-layout>

              <vaadin-form-item>
                <label slot="label">First Name</label>
                <vaadin-text-field
                  :value="newUser.firstName"
                  @input="newUser.firstName=$event.target.value"
                  required
                  error-message="Please enter first name"
                  class="full-width">
                </vaadin-text-field>
              </vaadin-form-item>

              <vaadin-form-item>
                <label slot="label">Last Name</label>
                <vaadin-text-field
                  :value="newUser.lastName"
                  @input="newUser.lastName=$event.target.value"
                  required
                  error-message="Please enter last name"
                  class="full-width">
                </vaadin-text-field>
              </vaadin-form-item>

              <vaadin-form-item>
                <label slot="label">Birth date</label>
                <vaadin-date-picker class="full-width"></vaadin-date-picker>
              </vaadin-form-item>

              <vaadin-form-item>
                <label slot="label">Language</label>
                <vaadin-combo-box class="full-width" :items="langauges"></vaadin-combo-box>
              </vaadin-form-item>

              <vaadin-form-item colspan="2">
                <label slot="label">Notes</label>
                <vaadin-text-area class="full-width"></vaadin-text-area>
              </vaadin-form-item>

              <vaadin-form-item colspan="2">
                <vaadin-checkbox>I have read the <a href @click="toggleDialog">terms and conditions</a></vaadin-checkbox>
              </vaadin-form-item>

              <vaadin-form-item colspan="2">
                <vaadin-button @click="submitForm">Submit</vaadin-button>
              </vaadin-form-item>

            </vaadin-form-layout>
          </form>
        </iron-form>
        </div>

        </iron-pages>


      </div>

    </app-drawer-layout>

  </app-header-layout>  



<vaadin-notification ref="formSubmitted" duration="4000" v-html="successNotificationHtml">
</vaadin-notification>

<vaadin-notification ref="formInvalid" duration="4000" v-html="errorNotificationHtml">
</vaadin-notification>

<vaadin-dialog :opened="dialogOpen" v-html="dialogHtml">
</vaadin-dialog>
</div>
</template>

<script>
import '@vaadin/vaadin-core';
import '@polymer/iron-pages';
import '@polymer/iron-form';
import '@polymer/iron-icon';
import '@polymer/iron-icons';
import '@polymer/paper-icon-button';
import '@polymer/paper-item';
import '@polymer/app-layout';
import '@polymer/app-layout/app-toolbar/app-toolbar.js';

import '@polymer/app-layout/app-scroll-effects/app-scroll-effects.js';
import '@polymer/app-layout/app-drawer-layout/app-drawer-layout.js';

import '@polymer/app-layout/app-drawer/app-drawer.js';
import '@polymer/app-layout/app-header/app-header.js';
import '@polymer/app-layout/app-header-layout/app-header-layout.js';
import '@polymer/app-layout/app-toolbar/app-toolbar.js';
import '@sdl-web/sdl-srch-grid/src/components/sdl-srch-grid.js';

import Person from './Person';

export default {
  name: 'app',

  data () {
    return {
      users: JSON.stringify([]),
      selectedUsers: [],
      newUser: new Person(),

      langauges: JSON.stringify(['Dutch', 'English', 'French']),
      selectedPage: 0,
      dialogOpen: false,

  gridHtml: `
    <form slot="search-slot">
        <input name="input1"></input>
    </form>
    <vaadin-grid name="vaadin-grid" slot="grid-slot"> 
        <vaadin-grid-column>
        <template class="header">Name</template>
        <template>[[item.name]]</template>
        </vaadin-grid-column>
        <vaadin-grid-column>
        <template class="header"><vaadin-grid-sorter path="gender">Gender</vaadin-grid-sorter></template>
        <template>[[item.gender]]</template>
        </vaadin-grid-column>
        <vaadin-grid-column>
        <template class="header">Eye Color</template>
        <template>[[item.eyeColor]]</template>
        </vaadin-grid-column> 
    </vaadin-grid>   
    `,
      successNotificationHtml: `
  <template>
    A new contact has been added successfully.
  </template>
      `,
      errorNotificationHtml: `
  <template>
    Some fields are missing or invalid.
  </template>
      `,
      dialogHtml: `
  <template>
    <vaadin-vertical-layout theme="spacing">
      <div>
        <h1>The content of dialog</h1>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin maximus magna et orci lacinia maximus. Fusce ut tincidunt ex. Morbi sed vehicula metus. Phasellus vel leo a elit viverra congue. Donec finibus iaculis eros vel vestibulum. Cras vehicula neque enim, eget faucibus ligula tempus vel. Integer felis nisi, sollicitudin at lectus at, bibendum vulputate risus. In ut massa et massa scelerisque viverra.</p>
      </div>
      <vaadin-button @click="toggleDialog">OK</vaadin-button>
    </vaadin-vertical-layout>
  </template>
      `
    }
  },

  created() {
    fetch("https://demo.vaadin.com/demo-data/1.0/people?count=200")
      .then(res => res.json())
      .then(
        (result) => {
          this.users = JSON.stringify(result.result);
        },
        (error) => {
          // Handle Error
        }
      );
  },

  methods: {
    narrowChanged(e) {
      var narrow = e.detail.value;
      var drawer = this.$refs.drawer;
      if(narrow) {
        setTimeout(function(){ 
          drawer.style.position = "fixed"; 
        }, 100);
      } else {
        this.$refs.drawer.style.position = "unset";
      }
    },
    toggleDialog() {
      this.dialogOpen = !this.dialogOpen;
    },

    toggleMenu(e) {
      console.log("CLICKED MENU",e);
      if (this.$refs.drawerlayout.forceNarrow || !this.$refs.drawerlayout.narrow) {
        this.$refs.drawerlayout.forceNarrow = !this.$refs.drawerlayout.forceNarrow;
        console.log("NARROW1=",this.$refs.drawerlayout.narrow);
      } else {
        this.$refs.drawerlayout.drawer.toggle();
        console.log("NARROW2=",this.$refs.drawerlayout.narrow);
      }
    },

    submitForm(form) {
      if (this.$refs.form.validate()) {
        this.$refs.formSubmitted.open();

        let grid = this.$refs.grid;
        grid.items.unshift(this.newUser);
        grid.selectedItems = [];
        grid.selectItem(this.newUser);
        grid.clearCache();

        this.newUser = new Person();
        this.selectedPage = 0; // Go back
      } else {
        this.$refs.formInvalid.open();
      }
    }
  }
}
</script>

<style>
    :host {
 
    }

    .card {
      width: 70%;
      margin: var(--lumo-space-m);
      padding: var(--lumo-space-m);
      border-radius: var(--lumo-border-radius);
      background-color: var(--lumo-base-color);
      box-shadow: var(--lumo-box-shadow-s);
    }

    body {
      margin: 0;
      font-family: 'Roboto', 'Noto', sans-serif;
      background-color: #eee;
    }

    app-header {
      background-color: #4285f4;
      color: #fff;
    }

    app-header paper-icon-button {
      --paper-icon-button-ink-color: #fff;
    }

    app-drawer-layout {
      --app-drawer-layout-content-transition: margin 0.2s;
    }

    app-drawer {
      position: unset;
      --app-drawer-content-container: {
        background-color: #eee;
        padding: 50px 0;
      }
    }

    .drawer-content {
      overflow: auto;
    }

</style>

