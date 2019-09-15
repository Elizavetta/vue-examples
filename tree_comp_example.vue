<template>
  <div>
    <div class="elements-panel-line-item">
      <div class="elements-panel-contents-item">{{selfList.name}} #{{selfList.id}}</div>
      <div class="elements-panel-line-item-edit right" @click="toggleEditList()">&#x1F589;</div>
      <div class="elements-panel-contents-edit" v-if="edit_list_on">
        <div class="elements-panel-line-item-btn" @click="toggleNewElement()">+элемент</div>
        <div class="elements-panel-line-item-btn" @click="toggleNewList()">+набор</div>
        <div class="elements-panel-line-item-btn" @click="toggleNewListCreate()">+новый набор</div>
      </div>
      <div v-if="new_element_on">
        <h6>Добавить элемент</h6>
        {{elements_groups}}
        <div>
          <label>группа</label>
          <select>
            <option v-for="g in element_groups" :key="g.id">{{g}}</option>
          </select>
        </div>
        <div>
          <label>фильтр</label>
          <input type="text" />
        </div>
        <div>
          <label>элемент</label>
          <select>
            <option v-for="el in filteredElements" :key="el.id">{{el.name}} [{{el.sku}}]</option>
          </select>
        </div>
      </div>
      <div class="elements-panel-contents-subitem" v-for="c in contentsOf" :key="c.id">
        <!--<span v-if="c.child_type==='list'">[]</span>-->
        <span v-if="c.child_type==='element'">
          <span>{{c.name}} ->{{c.parent_list_id}}</span>

          <span class="elements-panel-contents-rule">
            <span v-if="c.rules_type_id==1">меньше родителя на {{c.value}} м</span>
            <span v-if="c.rules_type_id==2">{{parseFloat(c.value, 1)}} штук на одного родителя</span>
            <span v-if="c.rules_type_id==3">{{parseFloat(c.value, 1)}} штук на метр родителя</span>
          </span>

          <span class="elements-panel-line-item-edit">&#x1F589;</span>
        </span>

        <list-contents v-if="c.child_type==='list'" :list_id="c.child_id"></list-contents>
      </div>
    </div>
  </div>
</template>
<script>
// import Vue from "vue";

export default {
  name: "list-contents",
  data() {
    return {
      lists: this.$store.state.lists,
      list_contents: this.$store.state.list_contents,
      list_types: this.$store.state.list_types,
      list_groups: this.$store.state.list_groups,
      elements: this.$store.state.elements_all,
      element_groups: this.$store.state.element_groups,

      new_element_filter: "",
      new_element_group: 0,

      edit_list_on: false,
      new_element_on: false,
      new_list_on: false,
      new_list_create_on: false
    };
  },
  components: {},
  props: ["list_id"],

  created() {
    let that = this;

    console.log(
      "elements groups: ",
      this.element_groups,
      this.$store.state.element_groups
    );
    //console.log("list_contents id", this.list_id);
  },

  computed: {
    contentsOf: {
      get: function() {
        let self = this;
        let first_children = this.$store.state.list_contents.filter(el => {
          return el.parent_list_id === self.list_id;
        });
        first_children.map(item => {
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
        });
        return first_children;
      }
    },
    selfList: {
      get: function() {
        return this.lists.filter(el => {
          return el.id === this.list_id;
        })[0];
      }
    },
    selfElement: {
      get: function() {
        let list = this.lists.filter(el => {
          return el.id === this.list_id;
        })[0];
        if (!list) return {};
        return this.elements.filter(el => {
          return el.id === list.parent_element_id;
        })[0];
      }
    },
    filteredElements: {
      get: function() {
        let that = this;
        return this.elements.filter(el => {
          return (
            el.element_group_id === that.new_element_group &&
            (el.name.indexOf(that.new_element_filter) !== -1 ||
              el.sku.indexOf(that.new_element_filter) !== -1)
          );
        });
      }
    }
  },

  mounted: function() {},

  watch: {},

  methods: {
    addNewChild: function() {},
    removeChild: function(child_id) {},

    toggleEditList: function() {
      this.edit_list_on = !this.edit_list_on;
    },

    toggleNewElement: function() {
      this.new_element_on = !this.new_element_on;
    },

    toggleNewList: function() {
      this.new_list_on = !this.new_list_on;
    },

    toggleNewListCreate: function() {
      this.new_list_create_on = !this.new_list_create_on;
    }
  }
};
</script>

<style lang="scss"></style>
