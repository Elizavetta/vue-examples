<template>
  <div>
    <div class="elements-panel-actions">
      <!--<h4>профили: {{profilesAll.length}} наборы: {{lists.length}} элементы: {{elements.length}}</h4>-->
      <select v-model="active_profile_id" @change="setActiveProfile()">
        <option v-for="p in profilesAll" :value="p.id" :key="p.id">{{p.name}}</option>
      </select>
      <div class="elements-panel-profile-top" v-if="active_profile && active_profile.id">
        <span>{{active_profile.name}} {{active_profile.id}}</span>
        <span
          class="elements-panel-profile-top-valid"
          v-if="profileDepthsValid()"
        >глубины установлены</span>
        <span
          class="elements-panel-profile-top-error"
          v-if="profileHasDepths()&& !profileDepthsValid()"
        >не все глубины установлены!</span>
        <span
          class="elements-panel-profile-top-error"
          v-if="!profileHasDepths()&& !profileDepthsValid()"
        >глубины не установлены!</span>

        <span
          class="elements-panel-profile-top-error"
          v-if="!hasGlasses()"
        >стеклопакеты не установлены</span>
        <span class="elements-panel-profile-top-valid" v-if="hasGlasses()">стеклопакеты установлены</span>

        <span
          class="elements-panel-profile-top-error"
          v-if="!activeGlassesHaveBeeds()"
        >штапики не установлены</span>
        <span
          class="elements-panel-profile-top-valid"
          v-if="activeGlassesHaveBeeds()"
        >штапики установлены</span>
      </div>
    </div>

    <!-- all lists -->
    <div class="elements-panel-container" v-if="active_profile && active_profile.depths">
      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Рама</h5>
      </div>

      <div class="elements-panel-line-item" v-if="active_profile.depths.frame">
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">A</span>
          <span
            v-if="!edit_depth_frame"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.frame.a}}</span>
          <input
            v-if="edit_depth_frame"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.frame.a"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">B</span>
          <span
            v-if="!edit_depth_frame"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.frame.b}}</span>
          <input
            v-if="edit_depth_frame"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.frame.b"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">C</span>
          <span
            v-if="!edit_depth_frame"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.frame.c}}</span>
          <input
            v-if="edit_depth_frame"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.frame.b"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">D</span>
          <span
            v-if="!edit_depth_frame"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.frame.d}}</span>
          <input
            v-if="edit_depth_frame"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.frame.b"
          />
        </div>
        <div
          v-if="!edit_depth_frame"
          class="elements-panel-depth-edit"
          @click="openDepthFrame()"
        >&#x1F589;</div>
        <div
          v-if="edit_depth_frame"
          class="elements-panel-depth-save"
          @click="saveDepthsFrame()"
        >сохранить</div>
        <div
          v-if="edit_depth_frame"
          class="elements-panel-depth-edit close"
          @click="closeDepthFrame()"
        >X</div>
      </div>

      <div class="elements-panel-line-item-toggle" @click="toggleRamaContents()">состав</div>

      <div v-if="rama_contents_on">
        <list-contents :list_id="active_profile.rama_list_id"></list-contents>
      </div>

      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Рама с подставочным профилем</h5>
      </div>

      <div class="elements-panel-line-item" v-if="active_profile.depths.still">
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">A</span>
          <span
            v-if="!edit_depth_frame_still"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.still.a}}</span>
          <input
            v-if="edit_depth_frame_still"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.still.a"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">B</span>
          <span
            v-if="!edit_depth_frame_still"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.still.b}}</span>
          <input
            v-if="edit_depth_frame_still"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.still.b"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">C</span>
          <span
            v-if="!edit_depth_frame_still"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.still.c}}</span>
          <input
            v-if="edit_depth_frame_still"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.still.c"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">D</span>
          <span
            v-if="!edit_depth_frame_still"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.still.d}}</span>
          <input
            v-if="edit_depth_frame_still"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.still.d"
          />
        </div>

        <div
          v-if="!edit_depth_frame_still"
          class="elements-panel-depth-edit"
          @click="openDepthFrameStill()"
        >&#x1F589;</div>
        <div
          v-if="edit_depth_frame_still"
          class="elements-panel-depth-save"
          @click="saveDepthsFrameStill()"
        >сохранить</div>
        <div
          v-if="edit_depth_frame_still"
          class="elements-panel-depth-edit close"
          @click="closeDepthFrameStill()"
        >X</div>
      </div>

      <div class="elements-panel-line-item-toggle" @click="toggleRamaStillContents()">состав</div>
      <div v-if="rama_still_contents_on">
        <list-contents :list_id="active_profile.rama_still_list_id"></list-contents>
      </div>

      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Импост</h5>
      </div>

      <div class="elements-panel-line-item" v-if="active_profile.depths.impost">
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">A</span>
          <span
            v-if="!edit_depth_impost"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.impost.a}}</span>
          <input
            v-if="edit_depth_impost"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.impost.a"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">B</span>
          <span
            v-if="!edit_depth_impost"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.impost.b}}</span>
          <input
            v-if="edit_depth_impost"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.impost.b"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">C</span>
          <span
            v-if="!edit_depth_impost"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.impost.c}}</span>
          <input
            v-if="edit_depth_impost"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.impost.c"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">D</span>
          <span
            v-if="!edit_depth_impost"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.impost.d}}</span>
          <input
            v-if="edit_depth_impost"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.impost.d"
          />
        </div>

        <div
          v-if="!edit_depth_impost"
          class="elements-panel-depth-edit"
          @click="openDepthImpost()"
        >&#x1F589;</div>
        <div
          v-if="edit_depth_impost"
          class="elements-panel-depth-save"
          @click="saveDepthsImpost()"
        >сохранить</div>
        <div
          v-if="edit_depth_impost"
          class="elements-panel-depth-edit close"
          @click="closeDepthImpost()"
        >X</div>
      </div>

      <div class="elements-panel-line-item-toggle" @click="toggleImpostContents()">состав</div>
      <div v-if="impost_contents_on">
        <list-contents :list_id="active_profile.impost_list_id"></list-contents>
      </div>

      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Створка</h5>
      </div>

      <div class="elements-panel-line-item" v-if="active_profile.depths.sash">
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">A</span>
          <span class="elements-panel-line-item-val">{{active_profile.depths.sash.a}}</span>
          <input
            v-if="edit_depth_sash"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.sash.a"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">B</span>
          <span
            v-if="!edit_depth_sash"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.sash.b}}</span>
          <input
            v-if="edit_depth_sash"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.sash.b"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">C</span>
          <span
            v-if="!edit_depth_sash"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.sash.c}}</span>
          <input
            v-if="edit_depth_sash"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.sash.c"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">D</span>
          <span
            v-if="!edit_depth_sash"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.sash.d}}</span>
          <input
            v-if="edit_depth_sash"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.sash.d"
          />
        </div>
        <div
          v-if="!edit_depth_sash"
          class="elements-panel-depth-edit"
          @click="openDepthSash()"
        >&#x1F589;</div>
        <div
          v-if="edit_depth_sash"
          class="elements-panel-depth-save"
          @click="saveDepthsSash()"
        >сохранить</div>
        <div
          v-if="edit_depth_sash"
          class="elements-panel-depth-edit close"
          @click="closeDepthSash()"
        >X</div>
      </div>

      <div class="elements-panel-line-item-toggle" @click="toggleStvorkaContents()">состав</div>
      <div v-if="stvorka_contents_on">
        <list-contents :list_id="active_profile.stvorka_list_id"></list-contents>
      </div>

      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Штульп</h5>
      </div>

      <div class="elements-panel-line-item" v-if="active_profile.depths.shtulp">
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">A</span>
          <span
            v-if="!edit_depth_shtulp"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.shtulp.a}}</span>
          <input
            v-if="edit_depth_shtulp"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.shtulp.a"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">B</span>
          <span
            v-if="!edit_depth_shtulp"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.shtulp.b}}</span>
          <input
            v-if="edit_depth_shtulp"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.shtulp.b"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">C</span>
          <span
            v-if="!edit_depth_shtulp"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.shtulp.c}}</span>
          <input
            v-if="edit_depth_shtulp"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.shtulp.c"
          />
        </div>
        <div class="elements-panel-depth-item">
          <span class="elements-panel-label-depth">D</span>
          <span
            v-if="!edit_depth_shtulp"
            class="elements-panel-line-item-val"
          >{{active_profile.depths.shtulp.d}}</span>
          <input
            v-if="edit_depth_shtulp"
            class="elements-panel-line-item-val input"
            type="number"
            v-model="active_profile.depths.shtulp.d"
          />
        </div>

        <div
          v-if="!edit_depth_shtulp"
          class="elements-panel-depth-edit"
          @click="openDepthShtulp()"
        >&#x1F589;</div>
        <div
          v-if="edit_depth_shtulp"
          class="elements-panel-depth-save"
          @click="saveDepthsShtulp()"
        >сохранить</div>
        <div
          v-if="edit_depth_shtulp"
          class="elements-panel-depth-edit close"
          @click="closeDepthShtulp()"
        >X</div>
      </div>

      <div class="elements-panel-line-item-toggle" @click="toggleShtulpContents()">состав</div>
      <div v-if="shtulp_contents_on">
        <list-contents :list_id="active_profile.shtulp_list_id"></list-contents>
      </div>

      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Стеклопакеты</h5>
        <div class="elements-panel-line-item half" v-for="g in allGlasses()" :key="g.id">
          <span>
            <input
              class="elements-panel-line-item-check"
              type="checkbox"
              :checked="glassHasThisProfile(g.id)"
              @change="toggleGlassProfile(g)"
            />
          </span>
          <span>{{ g.name }}</span>
          <span>&nbsp; [{{ g.glass_width }}]</span>
          <span
            v-if="glassHasThisProfile(g.id) && !glassHasBeeds(g)"
            class="elements-panel-profile-top-error"
          >штапик для стекла не установлен!</span>
          <span
            v-if="glassHasThisProfile(g.id) && glassHasBeeds(g)"
            class="elements-panel-profile-top-valid"
          >штапик установлен</span>
        </div>
      </div>

      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Штапики</h5>
        <!--{{activeGlassWidths}} {{unsetBeeds}}-->
        <div class="elements-panel-line-item" v-for="w in unsetBeeds" :key="w">
          <span class="elements-panel-label-depth">{{w}}</span>
          <select v-model="unset_beeds_edit[w]">
            <option v-for="sht in listsOfGroup(5)" :key="sht.id" :value="sht.id">{{sht.name}}</option>
          </select>
          <div class="elements-panel-depth-save left-2" @click="saveNewBeed(w)">сохранить</div>
        </div>
        <div class="elements-panel-line-item" v-for="beed in allBeeds" :key="beed.id">
          <span class="elements-panel-label-depth smaller">{{beed.glass_width}}</span>
          :
          <span
            v-if="!beeds_edit[beed.id]"
          >{{ beed.list_id }} {{ getListById(beed.list_id).name }}</span>
          <select v-if="beeds_edit[beed.id]" v-model="beed.new_list_id">
            <option v-for="sht in listsOfGroup(5)" :value="sht.id" :key="sht.id">{{sht.name}}</option>
          </select>
          <div
            v-if="!beeds_edit[beed.id]"
            class="elements-panel-depth-edit smaller"
            @click="openBeedEdit(beed)"
          >&#x1F589;</div>
          <div
            v-if="beeds_edit[beed.id]"
            class="elements-panel-depth-save left-2"
            @click="saveBeed(beed)"
          >сохранить</div>
          <div
            v-if="beeds_edit[beed.id]"
            class="elements-panel-depth-save left-2 remove"
            @click="removeBeed(beed)"
          >удалить</div>
          <div
            v-if="beeds_edit[beed.id]"
            class="elements-panel-depth-edit close"
            @click="closeBeedEdit(beed)"
          >X</div>
        </div>
      </div>

      <!--
      <div class="elements-panel-line-item">
        <h5 class="elements-panel-folder-title">Фурнитура</h5>
      </div>-->
    </div>
  </div>
</template>
<script>
import Vue from "vue";
import ListContents from "./ListContents.vue";

export default {
  data() {
    return {
      showModal: false,

      profiles: this.$store.state.profiles,
      lists: this.$store.state.lists,
      lists_batch: this.$store.state.lists_batch,
      list_contents: this.$store.state.list_contents,
      list_types: this.$store.state.list_types,
      list_groups: this.$store.state.list_groups,
      elements: this.$store.state.elements_all,
      elements_profiles: this.$store.state.elements_profiles,
      element_groups: this.$store.state.element_groups,
      beed_profiles: this.$store.state.beed_profiles,

      active_profile_id: 0,
      active_profile: {},

      active_list_type: 0,
      active_list_group: 0,
      edit_item_on: 0,

      opened_item: {
        all_params: {
          src_link: "",
          profile_width: 0,
          class: "",
          glass_width: "",
          compose: "",
          resist: 0
        },
        cameras: 0,
        //heat_coeff: 0,
        //heat_coeff_value: 0,
        //noise_coeff: 0,
        link: "",
        description: "",
        name: "",
        short_name: ""
      },

      rama_contents_on: false,
      rama_still_contents_on: false,
      stvorka_contents_on: false,
      impost_contents_on: false,
      shtulp_contents_on: false,

      data_saved: false,
      edit_depth_frame: false,
      edit_depth_frame_still: false,
      edit_depth_sash: false,
      edit_depth_impost: false,
      edit_depth_shtulp: false,
      beeds_edit: {},
      unset_beeds_edit: {}
    };
  },
  components: {
    "list-contents": ListContents
  },
  props: {},

  created() {
    let that = this;

    this.$store.watch(
      function(state) {
        return state.profiles;
      },
      function(val) {
        that.profiles = val;
      },
      {
        deep: true
      }
    );
    this.$store.watch(
      function(state) {
        return state.list_groups;
      },
      function(val) {
        that.list_groups = val;
      },
      {
        deep: true
      }
    );

    this.$store.watch(
      function(state) {
        return state.lists;
      },
      function(val) {
        that.lists = val;
      },
      {
        deep: true
      }
    );

    this.$store.dispatch("GET_ALL_SETS_TYPES");
    this.$store.dispatch("GET_ALL_SETS_GROUPS");

    if (!this.$store.state.lists.length) {
      this.$store.dispatch("GET_ALL_SETS");
    }
    if (!this.$store.state.list_contents.length) {
      this.$store.dispatch("GET_ALL_SETS_CONTENTS");
    }
    if (!this.$store.state.elements_profiles.length) {
      this.$store.dispatch("GET_ELEMENTS_PROFILES");
    }
    if (!this.$store.state.beed_profiles.length) {
      this.$store.dispatch("GET_BEED_PROFILES");
    }
    if (!this.$store.state.elements_all.length) {
      this.$store.dispatch("GET_ALL_ELEMENTS");
    }
    if (!this.$store.state.element_groups.length) {
      this.$store.dispatch("GET_ELEMENTS_GROUPS");
    }
  },

  computed: {
    profilesAll: {
      get: function() {
        let res = [];
        if (!this.profiles || !this.profiles.profileSystemFolders) return res;
        this.profiles.profileSystemFolders.forEach(folder => {
          folder.profile_systems.forEach(p => {
            res.push(p);
          });
        });
        return res;
      }
    },
    contentsOf: {
      get: function(list_id) {
        let res = [];
        let first_children = this.$store.state.list_contents.filter(el => {
          return el.parent_list_id === list_id;
        });
      }
    },
    allBeeds: {
      get: function() {
        return this.beed_profiles
          .filter(el => {
            return el.profile_system_id === this.active_profile.id;
          })
          .sort((a, b) => {
            if (a.glass_width > b.glass_width) return -1;
            else if (a.glass_width < b.glass_width) return 1;
            else return 0;
          });
      }
    },
    unsetBeeds: {
      get: function() {
        let that = this;
        let widths = this.activeGlassWidths;

        let unset_widths = [];
        widths.forEach(w => {
          let has_beed = that.beed_profiles.filter(el => {
            return (
              el.glass_width === w &&
              el.profile_system_id === that.active_profile.id
            );
          })[0];
          if (!has_beed) unset_widths.push(w);
        });
        unset_widths.forEach(w => {
          if (that.unset_beeds_edit[w] === undefined) {
            Vue.set(that.unset_beeds_edit, w, null);
          }
        });
        return unset_widths;
      }
    },

    profileGlasses: {
      get: function() {
        let that = this;
        let all_glasses = this.elements.filter(el => {
          return el.element_group_id === 8;
        });

        return all_glasses.filter(g => {
          let link_el = that.elements_profiles.filter(el => {
            return (
              el.profile_system_id === that.active_profile_id && el.id === g.id
            );
          })[0];
          if (link_el) return true;
          return false;
        });
      }
    },

    activeGlassWidths: {
      get: function() {
        let that = this;
        let widths = [];
        let all_glasses = this.elements.filter(el => {
          return el.element_group_id === 8;
        });
        all_glasses.map(g => {
          let link_el = that.elements_profiles.filter(el => {
            return (
              el.profile_system_id === that.active_profile_id &&
              el.element_id === g.id
            );
          })[0];
          if (link_el && widths.indexOf(g.glass_width) === -1) {
            widths.push(g.glass_width);
          }
        });
        return widths;
      }
    },

    listsOfGroup: {
      get: function() {
        return function(group_id) {
          return this.lists.filter(el => {
            return el.list_group_id === group_id;
          });
        };
      }
    }
  },

  mounted: function() {},

  watch: {
    profiles: {
      handler: function() {
        // console.log("profiles changed");
      },
      deep: true
    }
  },

  methods: {
    openItem: function(item) {
      this.opened_item = JSON.parse(JSON.stringify(item));
      let parsed_desc = {};
      try {
        if (
          this.opened_item.elements_description &&
          this.opened_item.elements_description.link
        ) {
          parsed_desc = JSON.parse(this.opened_item.elements_description.link);
        }
      } catch (e) {
        console.log("parse link fail", this.opened_item.elements_description);
      }
      parsed_desc =
        parsed_desc && Object.keys(parsed_desc).length > 0
          ? parsed_desc
          : {
              src_link:
                this.opened_item.elements_description &&
                this.opened_item.elements_description.link
            };
      this.opened_item.all_params = parsed_desc;
      this.$forceUpdate();
      this.edit_item_on = 1;
    },

    openItemFolder: function(folder) {
      this.opened_item = JSON.parse(JSON.stringify(folder));
      let parsed_desc = {};
      try {
        parsed_desc = JSON.parse(this.opened_item.link);
      } catch (e) {
        console.log("parse link fail", this.opened_item.link);
      }
      parsed_desc =
        parsed_desc && Object.keys(parsed_desc).length > 0
          ? parsed_desc
          : {
              src_link: this.opened_item.link
            };
      this.opened_item.all_params = parsed_desc;
      this.$forceUpdate();
      this.edit_item_on = 0;
      this.edit_item_folder_on = 1;
    },

    closeItem: function() {
      this.edit_item = {};
      this.edit_item_on = 0;
    },

    closeItemFolder: function() {
      this.edit_item = {};
      this.edit_item_folder_on = 0;
    },

    saveOpenedItem: function() {
      let upd_profile = JSON.parse(JSON.stringify(this.opened_item));

      if (!upd_profile.elements_description)
        upd_profile.elements_description = {};
      upd_profile.elements_description.link = JSON.stringify(
        upd_profile.all_params
      );
      upd_profile.all_params = undefined;
      upd_profile.glass_id = upd_profile.id;
 
      if (!upd_profile.id || upd_profile.is_new) {
        this.$store.dispatch("CREATE_GLASS", upd_profile);
      }
      this.$store.dispatch("UPDATEGLASS", upd_profile);
    },

    cloneOpenedItem: function() {
      let new_item = JSON.parse(JSON.stringify(this.opened_item));
      new_item.name = new_item.name + " Копия";
      this.$store.dispatch("CREATE_GLASS", new_item);
    },

    removeOpenedItem: function() {
      if (!confirm("Подтвердите удаление стеклопакета!")) return;
      if (!this.edit_item_on || !this.opened_item.id) return;
      this.$store.dispatch("REMOVE_GLASS", { element_id: this.opened_item.id });
    },

    saveOpenedItemFolder: function() {
      let upd_profile = JSON.parse(JSON.stringify(this.opened_item));
      upd_profile.link = JSON.stringify(upd_profile.all_params);
      upd_profile.all_params = undefined;
      upd_profile.glass_folder_id = upd_profile.id;
      if (!upd_profile.id || upd_profile.is_new) {
        this.$store.dispatch("CREATE_GLASS_FOLDER", upd_profile);
      } else this.$store.dispatch("UPDATE_GLASS_FOLDER", upd_profile);
    },

    cloneOpenedItemFolder: function() {
      let new_item = JSON.parse(JSON.stringify(this.opened_item));
      new_item.name = new_item.name + " Копия";
      this.$store.dispatch("CREATE_GLASS_FOLDER", new_item);
    },

    removeOpenedFolder: function() {
      if (!confirm("Подтвердите удаление группы стеклопакетов!")) return;
      if (this.edit_item_folder_on) {
        let folder = this.glass_folders.filter(el => {
          return el.id === this.opened_item.id;
        })[0];
        // console.log("removeOpenedFolder", folder);
        if (!folder) return;
        if (this.folderIsEmpty(folder)) {
          this.$store.dispatch("REMOVE_GLASS_FOLDER", {
            glass_folder_id: this.opened_item.id
          });
        }
      }
    },

    createNewItem: function() {
      for (let key in this.opened_item) {
        this.opened_item[key] = undefined;
      }
      this.opened_item.is_new = 1;
      this.opened_item.all_params = {};
      this.opened_item.name = "Новое стекло";
      this.edit_item_on = 1;
      this.edit_item_folder_on = 0;
    },

    toggleRamaContents: function() {
      this.rama_contents_on = !this.rama_contents_on;
    },
    toggleRamaStillContents: function() {
      this.rama_still_contents_on = !this.rama_still_contents_on;
    },
    toggleStvorkaContents: function() {
      this.stvorka_contents_on = !this.stvorka_contents_on;
    },
    toggleImpostContents: function() {
      this.impost_contents_on = !this.impost_contents_on;
    },
    toggleShtulpContents: function() {
      this.shtulp_contents_on = !this.shtulp_contents_on;
    },

    //sets all
    setActiveProfile: function() {
      this.active_profile = this.profilesAll.filter(el => {
        return el.id === this.active_profile_id;
      })[0];
    },

    getListById: function(list_id) {
      return (
        this.$store.state.lists.filter(el => {
          return el.id === list_id;
        })[0] || {}
      );
    },

    getContentsOfList: function(list_id) {
      let self = this;
      let res = [];
      checkContents(list_id);

      function checkContents(list_id) {
        let c_list = self.$store.state.list_contents.filter(el => {
          return el.parent_list_id === list_id;
        });

        if (c_list.length) {
          c_list.forEach(item => {
            if (item.child_type === "element") {
              let elem_item = self.elements.filter(el => {
                return el.id === item.child_id;
              })[0];
              if (elem_item) {
                item.name = elem_item.name;
                item.sku = elem_item.sku;
              }
            } else {
              let elem_item = self.lists.filter(el => {
                return el.id === item.child_id;
              })[0];
              if (elem_item) {
                item.name = elem_item.name;
                item.sku = elem_item.sku;
              }
            }
            res.push(item);
            if (item.child_type === "list") {
              checkContents(item.child_id);
            }
          });
        }
      }
      return res;
    },

    profileHasDepths: function() {
      let valid = false;
      let check_keys = ["frame", "sash", "impost", "still", "shtulp"];
      if (!this.active_profile.depths) return false;
      check_keys.forEach(key => {
        if (!this.active_profile.depths[key]) {
          valid = false;
          return;
        }
        if (
          this.active_profile.depths[key].a > 0 ||
          this.active_profile.depths[key].b > 0 ||
          this.active_profile.depths[key].c > 0 ||
          this.active_profile.depths[key].d > 0
        ) {
          valid = true;
        }
      });
      return valid;
    },

    profileDepthsValid: function() {
      let valid = true;
      let check_keys = ["frame", "sash", "impost", "still", "shtulp"];
      if (!this.active_profile.depths) return false;
      check_keys.forEach(key => {
        if (!this.active_profile.depths[key]) {
          console.log("no depth key", key, this.active_profile.depths);
          valid = false;
          return;
        }
        if (
          !parseFloat(this.active_profile.depths[key].a) ||
          !parseFloat(this.active_profile.depths[key].b) ||
          !parseFloat(this.active_profile.depths[key].c) ||
          !parseFloat(this.active_profile.depths[key].d)
        ) {
          console.log(
            "invalid depths for : ",
            key,
            this.active_profile.depths[key]
          );
          valid = false;
        }
      });
      return valid;
    },

    openDepthFrame: function() {
      this.edit_depth_frame = true;
    },
    closeDepthFrame: function() {
      this.edit_depth_frame = false;
    },

    openDepthFrameStill: function() {
      this.edit_depth_frame_still = true;
    },
    closeDepthFrameStill: function() {
      this.edit_depth_frame_still = false;
    },

    openDepthSash: function() {
      this.edit_depth_sash = true;
    },
    closeDepthSash: function() {
      this.edit_depth_sash = false;
    },

    openDepthImpost: function() {
      this.edit_depth_impost = true;
    },
    closeDepthImpost: function() {
      this.edit_depth_impost = false;
    },

    openDepthShtulp: function() {
      this.edit_depth_shtulp = true;
    },
    closeDepthShtulp: function() {
      this.edit_depth_shtulp = false;
    },

    saveDepths: function() {
      console.log("save depths: ", this.active_profile.depths);
    },

    saveDepthsFrame: function() {
      this.$store.dispatch("UPDATE_SINGLE_LIST", {
        list_id: this.active_profile.rama_list_id,
        a: this.active_profile.depths.frame.a,
        b: this.active_profile.depths.frame.b,
        c: this.active_profile.depths.frame.c,
        d: this.active_profile.depths.frame.d
      });
      this.edit_depth_frame = false;
    },

    saveDepthsFrameStill: function() {
      this.$store.dispatch("UPDATE_SINGLE_LIST", {
        list_id: this.active_profile.rama_still_list_id,
        a: this.active_profile.depths.still.a,
        b: this.active_profile.depths.still.b,
        c: this.active_profile.depths.still.c,
        d: this.active_profile.depths.still.d
      });
      this.edit_depth_frame_still = false;
    },

    saveDepthsSash: function() {
      this.$store.dispatch("UPDATE_SINGLE_LIST", {
        list_id: this.active_profile.stvorka_list_id,
        a: this.active_profile.depths.sash.a,
        b: this.active_profile.depths.sash.b,
        c: this.active_profile.depths.sash.c,
        d: this.active_profile.depths.sash.d
      });
      this.edit_depth_sash = false;
    },

    saveDepthsImpost: function() {
      this.$store.dispatch("UPDATE_SINGLE_LIST", {
        list_id: this.active_profile.impost_list_id,
        a: this.active_profile.depths.impost.a,
        b: this.active_profile.depths.impost.b,
        c: this.active_profile.depths.impost.c,
        d: this.active_profile.depths.impost.d
      });
      this.edit_depth_impost = false;
    },

    saveDepthsShtulp: function() {
      this.$store.dispatch("UPDATE_SINGLE_LIST", {
        list_id: this.active_profile.shtulp_list_id,
        a: this.active_profile.depths.shtulp.a,
        b: this.active_profile.depths.shtulp.b,
        c: this.active_profile.depths.shtulp.c,
        d: this.active_profile.depths.shtulp.d
      });
      this.edit_depth_shtulp = false;
    },

    hasGlasses: function() {
      let has_glasses = false;
      let that = this;
      let profile_els = this.elements_profiles.filter(el => {
        return el.profile_system_id === this.active_profile.id;
      });
      profile_els.forEach(p_el => {
        let elem = that.elements.filter(el => {
          return el.id === p_el.element_id;
        })[0];
        if (elem && elem.element_group_id === 8) {
          has_glasses = true;
        }
      });
      return has_glasses;
    },

    allGlasses: function() {
      return this.elements.filter(el => {
        return el.element_group_id === 8;
      });
    },

    glassHasThisProfile: function(glass_id) {
      let all_profile_els = this.elements_profiles.filter(el => {
        return el.profile_system_id === this.active_profile.id;
      });
      let link_el = this.elements_profiles.filter(el => {
        return (
          el.profile_system_id === this.active_profile.id &&
          el.element_id === glass_id
        );
      })[0];
      return !!link_el;
    },

    glassHasBeeds: function(glass_el) {
      let that = this;
      let beed_el = this.beed_profiles.filter(el => {
        return (
          el.glass_width == glass_el.glass_width &&
          el.profile_system_id == that.active_profile.id
        );
      })[0];
      if (beed_el) {
        let list_el = this.lists.filter(el => {
          return el.id === beed_el.list_id;
        })[0];
        if (list_el) return true;
        return false;
      }
      return false;
    },

    activeGlassesHaveBeeds: function() {
      let that = this;
      let res = true;

      this.activeGlassWidths.forEach(w => {
        let beed_el = that.beed_profiles.filter(el => {
          return (
            el.glass_width == w &&
            el.profile_system_id == that.active_profile.id
          );
        })[0];
        if (!beed_el) {
          res = false;
        }
      });
      return res;
    },

    glassBeedsValid: function() {},

    toggleGlassProfile: function(glass_el) {
      let has_profile = this.elements_profiles.filter(el => {
        return (
          el.profile_system_id === this.active_profile.id &&
          el.element_id === glass_el.id
        );
      })[0];
      if (has_profile) {
        this.$store.dispatch("UPDATE_ELEMENT_PROFILES", {
          element_id: glass_el.id,
          on: [],
          off: [this.active_profile.id]
        });
      } else {
        this.$store.dispatch("UPDATE_ELEMENT_PROFILES", {
          element_id: glass_el.id,
          on: [this.active_profile.id],
          off: []
        });
      }
      this.$store.commit("TOGGLE_ELEMENT_PROFILE", {
        element_id: glass_el.id,
        profile_system_id: this.active_profile.id
      });
    },

    openBeedEdit: function(beed_el) {
      Vue.set(this.beeds_edit, beed_el.id, true);
      Vue.set(beed_el, "new_list_id", null);
    },

    closeBeedEdit: function(beed_el) {
      Vue.set(this.beeds_edit, beed_el.id, false);
    },

    saveBeed: function(beed_el) {
      if (beed_el.list_id === beed_el.new_list_id) return;
      else {
        beed_el.list_id = beed_el.new_list_id;
        this.$store.dispatch("SET_SINGLE_BEED_PROFILE", beed_el);
        Vue.set(this.beeds_edit, beed_el.id, false);
      }
    },

    saveNewBeed: function(w) {
      this.$store.dispatch("SET_SINGLE_BEED_PROFILE", {
        glass_width: w,
        profile_system_id: this.active_profile.id,
        list_id: this.unset_beeds_edit[w]
      });
    },

    removeBeed: function(beed_el) {
      if (confirm("Действительно удалить щтапик для толщины ?")) {
        this.$store.dispatch("REMOVE_SINGLE_BEED_PROFILE", beed_el);
        Vue.set(this.beeds_edit, beed_el.id, false);
      }
    }
  }
};
</script>

<style lang="scss"></style>
