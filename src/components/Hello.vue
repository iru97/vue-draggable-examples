<template>
  <div>
    <div class="col-md-12">
      <div class="col-md-2">
        <div class="options">
          <h3>Options:</h3>
          <div>

          </div>
        </div>        
        <draggable class="list-group" group="one" v-model="list" v-bind="dragOptions" :move="onMove" @start="isDragging=true" @end="isDragging=false">
            <li class="list-group-item" v-for="element in list" :key="element.order">
              <i :class="element.fixed? 'fa fa-anchor' : 'glyphicon glyphicon-pushpin'" @click=" element.fixed=! element.fixed" aria-hidden="true"></i>
              {{element.name}}
              <span class="badge">{{element.order}}</span>
            </li>
        </draggable>
      </div>

      <div class="col-md-2">
        <div class="options">
          <h3>Options:</h3>
          <div>
            <input type="checkbox" v-model="list2Sort">
            <label>Sort</label>
          </div>
        </div>
        <draggable class="list-group"  group="one" v-model="list2" :sort="list2Sort" v-bind="dragOptions" :move="onMove">
            <li class="list-group-item" v-for="element in list2" :key="element.order">
              <i :class="element.fixed? 'fa fa-anchor' : 'glyphicon glyphicon-pushpin'" @click=" element.fixed=! element.fixed" aria-hidden="true"></i>
              {{element.name}}
              <span class="badge">{{element.order}}</span>
            </li>
        </draggable>
      </div>
      <div class="col-md-2">
        <div class="options">
          <h3>Options:</h3>
          <div>
            <label>Accept items from other lists</label>
            <input type="checkbox" v-model="acceptData">
          </div>
          <div>
            <label>Send items to other lists</label>
            <input type="checkbox" v-model="sendData">
          </div>
        </div>
        <draggable class="list-group" :group="{name: 'one', put: acceptData, pull: sendData}" v-model="list3" :sort="false" @start="isDragging=true" @end="isDragging=false">
            <li class="list-group-item" v-for="element in list3" :key="element.order">
              <i :class="element.fixed? 'fa fa-anchor' : 'glyphicon glyphicon-pushpin'" @click=" element.fixed=! element.fixed" aria-hidden="true"></i>
              {{element.name}}
              <span class="badge">{{element.order}}</span>
            </li>
        </draggable>
        <div id="hoverHere">
          HACER HOVER AQUÍ
        </div>
        <div :class="{'draggClass': isDragging && isHover}">
          HOLA
        </div>
      </div>
      <div class="col-md-2">
        <div class="options">
          <h3>Options:</h3>

        </div>
        <draggable class="list-group" :group="{name: 'one', put: false, pull: false}" v-model="listWithChilds" :sort="false" v-bind="dragOptions" :move="onMove" >
            <li class="list-group-item drag-item" v-for="element in listWithChilds" :key="element.order">
                <div>
                  <i :class="element.fixed? 'fa fa-anchor' : 'glyphicon glyphicon-pushpin'" @click=" element.fixed=! element.fixed" aria-hidden="true"></i>
                  {{element.parent.name}}
                  <span class="badge">{{element.order}}</span>
                </div>
              <div>
                <li class="list-group-item " v-for="child in element.childs" :key="child.order">
                  {{ child.name }}
                </li>
              </div>

            </li>
        </draggable>
      </div>

    </div>
    <div class="col-md-12">
      <div class="list-group col-md-2">
        <pre>{{listString}}</pre>
      </div>
      <div class="list-group col-md-2">
        <pre>{{list2String}}</pre>
      </div>
      <div class="list-group col-md-2">
        <pre>{{list3String}}</pre>
      </div>
      <div class="list-group col-md-2">
        <pre>{{listWithChilds}}</pre>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";
const message = [
  "vue.draggable",
  "draggable",
  "component",
  "for",
  "vue.js 2.0",
  "based",
  "on",
  "Sortablejs"
];
const items = ["bag", "pencil", "bottle"];
const keywords = ["example", "drag", "ksr"];
const itemsWithChilds = [
  {
    name: "js",
    keywords: ["let", "var"]
  },
  {
    name: "csharp",
    keywords: ["linqu"]
  }
];

export default {
  name: "hello",
  components: {
    draggable
  },
  data() {
    return {
      list: message.map((name, index) => {
        return { name, order: index + 1, fixed: false };
      }),
      list2: items.map((name, index) => {
        return { name, order: index + 1, fixed: false };
      }),
      list3: keywords.map((name, index) => {
        return { name, order: index + 1, fixed: false };
      }),
      listWithChilds: itemsWithChilds.map((parent, index) => {
        return {
          parent,
          childs: parent.keywords.map((name, index) => {
            return { name, order: index + 1, fixed: false };
          }),
          order: index + 1,
          fixed: false
        };
      }),
      editable: true,
      isDragging: false,
      isHover: false,
      delayedDragging: false,
      list2Sort: false,
      acceptData: true,
      sendData: true
    };
  },
  methods: {
    orderList() {
      this.list = this.list.sort((one, two) => {
        return one.order - two.order;
      });
    },
    onMove({ relatedContext, draggedContext }) {
      const relatedElement = relatedContext.element;
      const draggedElement = draggedContext.element;
      return (
        (!relatedElement || !relatedElement.fixed) && !draggedElement.fixed
      );
    },
    handleMouseEnter() {
      // eslint-disable-next-line no-console
      console.log("HEY");
    }
  },
  computed: {
    dragOptions() {
      return {
        animation: 0,
        disabled: !this.editable,
        ghostClass: "ghost"
      };
    },
    listString() {
      return JSON.stringify(this.list, null, 2);
    },
    list2String() {
      return JSON.stringify(this.list2, null, 2);
    },
    list3String() {
      return JSON.stringify(this.list3, null, 2);
    }
  },
  watch: {
    isDragging(newValue) {
      if (newValue) {
        this.delayedDragging = true;
        return;
      }
      this.$nextTick(() => {
        this.delayedDragging = false;
      });
    }
  }
};
</script>

<style>
.options {
  height: 10rem;
}

.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}

.list-group {
  min-height: 20px;
}

.list-group-item {
  cursor: move;
}

.list-group-item i {
  cursor: pointer;
}

.draggClass:hover {
  display: none;
}
</style>
