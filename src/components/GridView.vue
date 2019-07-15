<!-- https://codepen.io/gusy/pen/jQVrGz -->
<!-- https://github.com/jbaysolutions/vue-grid-layout/issues/73 -->
<template>
  <!-- <div>
    <div style="text-align: center;">
      <div class="site-title"> Bugs of the City</div>
      <span v-if="!preview">
        <button class="btn btn-outline-primary" @click="addTitleGridItem"> H1 </button>
        <button class="btn btn-outline-primary" @click="addContentGridItem"> Content </button>
        <button class="btn btn-outline-primary" @click="addImageGridItem"> Image </button>
      </span>
      <button class="btn btn-outline-primary" @click="disableGrid">
        <span v-if="preview"> Edit </span>
        <span v-else> Preview </span>
      </button>
    </div>
    <hr>
    <grid-layout :layout="getResources"
      :col-num="12" :row-height="30" :is-draggable="isDraggable" :is-resizable="isResizable"
      :is-mirrored="false" :vertical-compact="true" :margin="[5, 5]"
      :use-css-transforms="true">
      <grid-item v-for="(item, index) in getResources" :key="index" :class="{ 'editMode' : !preview }"
      :autoSize="true" :x="item.x" :y="item.y" :w="item.w" :h="item.h" :i="item.i">

        <div v-if="!preview" @click="removeItem({key: index})" style="position: absolute; bottom: 0px; left: 4px;">
          <i class="fa fa-trash" aria-hidden="true"></i>
        </div>
        <component v-bind:is="item.type" v-bind="item.options" ></component>

      </grid-item>
    </grid-layout>
  </div> -->
  <grid-layout
  :layout="layout"
  :col-num="12"
  :row-height="30"
  :is-draggable="true"
  :is-resizable="true"
  :is-mirrored="false"
  :vertical-compact="true"
  :margin="[10, 10]"
  :use-css-transforms="true"
  >

  <grid-item v-for="item in layout"
  :key="item.i"
  :x="item.x"
  :y="item.y"
  :w="item.w"
  :h="item.h"
  :i="item.i">
    <div class="item">

      <resizable>
        <component v-if="item.type" v-bind:is="item.type" v-bind="item.options" ></component>

      </resizable>

    </div>

  </grid-item>
  </grid-layout>
</template>
<script>
import { mapGetters, mapActions } from 'vuex'
// import TextWidget from './TextWidget'
// import TextAreaWidget from './TextAreaWidget'
// import ImageWidget from './ImageWidget'
import { GridLayout, GridItem } from 'vue-grid-layout'
import ResizeSensor from 'resize-sensor'

const resizable = {
  mounted () {
    const vm = this
    this.gridItem = this.$parent

    this.resizeSensor = new ResizeSensor(this.$el, () => {
      const widgetHeight = this.$el.clientHeight
      console.log('resizeSensor' + widgetHeight)

      if (widgetHeight !== vm.widgetHeight) {
        const h = Math.ceil((widgetHeight + vm.gridItem.margin[1]) / (vm.gridItem.rowHeight + vm.gridItem.margin[1]))
        const newH = h

        vm.widgetHeight = widgetHeight

        vm.gridItem.eventBus.$emit(
          'resizeEvent',
          'resizeend',
          vm.gridItem.i,
          vm.gridItem.x,
          vm.gridItem.y,
          newH,
          vm.gridItem.w
        )
      }
    })

    this.widgetHeight = this.$el.clientHeight
  },
  data () {
    return {
      resizeSensor: null,
      clientHeight: null
    }
  },
  render (h) {
    return h('div', [h('div', { attrs: { contentEditable: true, class: 'editable' } }, this.$slots.default)])
  }
}

const MyQCard = {
  // functional: true,
  name: 'my-q-card',
  props: ['text'],
  // render (h) {
  // return h('q-card', [h('q-card-section', {
  //   props: {
  //     text: 'foobar'
  //   }
  // })])
  // }
  render: function (createElement, context) {
    var self = this
    // Transparently pass any attributes, event listeners, children, etc.
    return createElement('q-card', [createElement('q-card-section', {
      domProps: {
        innerHTML: self.text
      }
      // props: {
      //   text: 'foobar'
      // }
    })])
  }
  // render: function (h) {
  //   return (
  //     <q-card>
  //       <q-card-section>Hello</q-card-section>
  //     </q-card>
  //   )
  // }
  // template: '<q-card class="my-card"><q-card-section>{{text}}</q-card-section></q-card>'
}

// const HelloCtor = Vue.extend(Hello);

export default {
  name: 'gridview',
  // components: { GridLayout, GridItem, TextWidget, TextAreaWidget, ImageWidget },
  components: { GridLayout, GridItem, resizable, MyQCard },
  data () {
    return {
      layout: [
        { 'x': 0,
          'y': 0,
          'w': 2,
          'h': 2,
          'i': '0',
          'type': 'q-btn',
          options: { color: 'white', 'text-color': 'black', label: 'Standard' }
        },
        { 'x': 2,
          'y': 0,
          'w': 2,
          'h': 4,
          'i': '1',
          'type': 'my-q-card',
          options: {
            'flat': true,
            'bordered': true,
            // class: 'bg-grey-9',
            text: 'test' +
            '\nsome more'
          }
          // options: { color: 'white', 'text-color': 'black', label: 'Standard' }
        },
        {
          'x': 4,
          'y': 0,
          'w': 2,
          'h': 5,
          'i': '2',
          type: 'q-chip',
          options: {
            icon: 'alarm',
            label: 'Set alarm'
          }
        },
        { 'x': 6, 'y': 0, 'w': 2, 'h': 3, 'i': '3' },
        { 'x': 8, 'y': 0, 'w': 2, 'h': 3, 'i': '4' },
        { 'x': 10, 'y': 0, 'w': 2, 'h': 3, 'i': '5' },
        { 'x': 0, 'y': 5, 'w': 2, 'h': 5, 'i': '6' },
        { 'x': 2, 'y': 5, 'w': 2, 'h': 5, 'i': '7' },
        { 'x': 4, 'y': 5, 'w': 2, 'h': 5, 'i': '8' },
        { 'x': 6, 'y': 4, 'w': 2, 'h': 4, 'i': '9' },
        { 'x': 8, 'y': 4, 'w': 2, 'h': 4, 'i': '10' },
        { 'x': 10, 'y': 4, 'w': 2, 'h': 4, 'i': '11' },
        { 'x': 0, 'y': 10, 'w': 2, 'h': 5, 'i': '12' },
        { 'x': 2, 'y': 10, 'w': 2, 'h': 5, 'i': '13' },
        { 'x': 4, 'y': 8, 'w': 2, 'h': 4, 'i': '14' },
        { 'x': 6, 'y': 8, 'w': 2, 'h': 4, 'i': '15' },
        { 'x': 8, 'y': 10, 'w': 2, 'h': 5, 'i': '16' },
        { 'x': 10, 'y': 4, 'w': 2, 'h': 2, 'i': '17' },
        { 'x': 0, 'y': 9, 'w': 2, 'h': 3, 'i': '18' },
        { 'x': 2, 'y': 6, 'w': 2, 'h': 2, 'i': '19' }
      ]
      // isDraggable: false,
      // isResizable: false,
      // preview: true,
      // contenteditable: false
    }
  },
  computed: {
  //   ...mapGetters({
  //     getResources: 'page/getResources'
  //   })
    // getResources: function () {
    //   return [
    //     {
    //       'x': 0,
    //       'y': 0,
    //       'w': 3,
    //       'h': 2,
    //       'i': '0',
    //       'type': 'q-btn',
    //       options: { color: 'white', 'text-color': 'black', label: 'Standard' },
    //       // 'title': 'Actias Luna',
    //       // 'headings': {
    //       //   'h1': true,
    //       //   'h2': false,
    //       //   'h3': false
    //       // },
    //       'moved': false
    //     },
    //     {
    //       'x': 8,
    //       'y': 17,
    //       'w': 4,
    //       'h': 8,
    //       'i': '1',
    //       'type': 'q-btn',
    //       options: { color: 'black', 'text-color': 'white', label: 'Standard' },
    //       'moved': false
    //     }
    //
    //   ]
    // }
  },

  methods: {
  //   ...mapActions([
  //     'addTitleGridItem',
  //     'addContentGridItem',
  //     'addImageGridItem',
  //     'removeItem'
  //   ]),
    // disableGrid () {
    //   this.isDraggable = !this.isDraggable
    //   this.isResizable = !this.isResizable
    //   this.preview = !this.preview
    //   this.contenteditable = !this.contenteditable
    // }
  }
}
</script>
<style>
/* .editMode {
  background-color: #fafafa;
  border-radius: 5px;
}
.site-title {
  font-family: 'Lilita One', cursive;
  font-size: 50px;
  color: #F48FB1;
  text-align: center;
}
.heading1 {
  font-family: 'Crushed', cursive;
  font-size: 35px;
  border: none;
  color: #2196F3;
}
.heading2 {
  font-family: 'Patrick Hand', cursive;
  font-size: 20px;
  border: none;
  color: #3096f3;
  background-color: #FFF9C4;
  width: 100%;
  padding: 10px 5px;
}
.heading3 {
  font-family: 'Homemade Apple', cursive;
  font-size: 20px;
  border: none;
  color: #66d2b3;
  padding: 0 7px;
}
.content {
  font-family: 'Patrick Hand', cursive;
  font-size: 20px;
  color: #2196F3;
} */
/*** EXAMPLE ***/
* {
  box-sizing: border-box;
}
#content {
    width: 100%;
}

.vue-grid-layout {
    background: #eee;
}

/* .editable {
  border: 1px solid lawngreen;
  height: 100%;
} */

.columns {
    -moz-columns: 120px;
    -webkit-columns: 120px;
    columns: 120px;
}

.vue-resizable-handle {
    z-index: 5000;
    position: absolute;
    width: 20px;
    height: 20px;
    bottom: 0;
    right: 0;
    background: url('data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/Pg08IS0tIEdlbmVyYXRvcjogQWRvYmUgRmlyZXdvcmtzIENTNiwgRXhwb3J0IFNWRyBFeHRlbnNpb24gYnkgQWFyb24gQmVhbGwgKGh0dHA6Ly9maXJld29ya3MuYWJlYWxsLmNvbSkgLiBWZXJzaW9uOiAwLjYuMSAgLS0+DTwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+DTxzdmcgaWQ9IlVudGl0bGVkLVBhZ2UlMjAxIiB2aWV3Qm94PSIwIDAgNiA2IiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmZmZmMDAiIHZlcnNpb249IjEuMSINCXhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiDQl4PSIwcHgiIHk9IjBweCIgd2lkdGg9IjZweCIgaGVpZ2h0PSI2cHgiDT4NCTxnIG9wYWNpdHk9IjAuMzAyIj4NCQk8cGF0aCBkPSJNIDYgNiBMIDAgNiBMIDAgNC4yIEwgNCA0LjIgTCA0LjIgNC4yIEwgNC4yIDAgTCA2IDAgTCA2IDYgTCA2IDYgWiIgZmlsbD0iIzAwMDAwMCIvPg0JPC9nPg08L3N2Zz4=');
    background-position: bottom right;
    padding: 0 3px 3px 0;
    background-repeat: no-repeat;
    background-origin: content-box;
    box-sizing: border-box;
    cursor: se-resize;
}

.vue-grid-item:not(.vue-grid-placeholder) {
    background: #ccc;
    border: 1px solid black;
    padding: 1rem;
}

.vue-grid-item.resizing {
    opacity: 0.9;
}

.vue-grid-item.static {
    background: #cce;
}

.vue-grid-item .text {
    font-size: 24px;
    text-align: center;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    height: 100%;
    width: 100%;
}

.vue-grid-item .no-drag {
    height: 100%;
    width: 100%;
}

.vue-grid-item .minMax {
    font-size: 12px;
}

.vue-grid-item .add {
    cursor: pointer;
}

.vue-draggable-handle {
    position: absolute;
    width: 20px;
    height: 20px;
    top: 0;
    left: 0;
    background: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='10'><circle cx='5' cy='5' r='5' fill='#999999'/></svg>") no-repeat;
    background-position: bottom right;
    padding: 0 8px 8px 0;
    background-repeat: no-repeat;
    background-origin: content-box;
    box-sizing: border-box;
    cursor: pointer;
}

.item {
  height: 100%;
}
</style>
