<template>
  <draggable
    :list="listArray"
    item-key="_id"
    handle=".handle"
    class="row custom-scroll"
    ghost-class="ghost"
    drag-class="drag"
    :force-fallback="true"
    @end="$emit('syncData')"
  >
    <template #item="{ element }">
      <div class="board">
        <div class="flex title-bar">
          <div class="handle title-holder" >
            <h4 v-show="!(editMode === element._id)">{{ element.title }}</h4>
            <textarea
              :ref="setItemRef"
              :id="element._id"
              v-show="editMode === element._id"
              v-model="element.title"
              @blur="validateCardDetails(element._id, element.title)"
            ></textarea>
          </div>
          <dropdown-menu :ref="element._id" @delete="deleteCard(element._id)" @edit="toggleEditMode(element._id)"></dropdown-menu>
        </div>
        <div class="card">
          <p v-if="element.value.length === 0" style="color:gray;text-align:center;font-size:14px;margin:20px;margin-bottom:0px;">Drop your tasks here 🔥</p>
          <div class="custom-scroll" style="max-height:calc(100vh - 395px);overflow:auto;padding: 20px;padding-bottom:0px;">
            <draggable
              v-model="element.value"
              item-key="id"
              group="card"
              ghost-class="ghost"
              drag-class="drag"
              @end="$emit('syncData')"
              >
              <template #item="{ element }">
                <div class="list-item" @click="viewTask(element._id)">
                  <p>{{ resizeNameLength(element.name,25) }}</p>
                  <div class="desc-text">
                    <p>{{ resizeNameLength(element.description,100)}}</p>
                  </div>
                </div>
              </template>
            </draggable>
          </div>
          <div @click="newTask(element._id)" class="add-card-button">
            <p><span>+</span> Add Task</p>
          </div>
        </div>
      </div>
    </template>
  </draggable>
</template>

<script>
import draggable from "vuedraggable";
export default {
  inject: ["listArray", "fetchData", "syncData"],
  components: {
    draggable,
  },
  data() {
    return {
      drag: false,
      editMode: "",
      menuMode: "",
      textFieldRefs: {},
    };
  },
  methods: {
    setItemRef(el) {
      if (el) {
        this.textFieldRefs[el.id] = el;
      }
    },
    toggleEditMode(id = "") {
      this.editMode = id;
      if (id !== "") {
        setInterval(() => {
          this.textFieldRefs[id].focus();
        }, 5);
      }
    },
    newTask(id) {
      this.$emit("newTask", id);
    },
    viewTask(id) {
      this.$emit("viewTask", id);
    },
    deleteCard(id) {
      this.$emit("deleteCard", id);
    },
    resizeNameLength(name,size) {
      if (name.length > size) {
        return name.substring(0, size) + "...";
      }
      return name;
    },
    async validateCardDetails(id, title) {
      if (title.trim() === "") {
        this.fetchData();
      } else {
        this.syncData();
      }
      this.toggleEditMode();
    },
  },
  beforeUpdate() {
    this.textFieldRefs = {};
  },
  updated() {
    console.log(this.textFieldRefs);
  },
};
</script>

<style scoped>

/* width */
.custom-scroll::-webkit-scrollbar {
  width: 8px;
  height: 10px;
}

/* Track */
.custom-scroll::-webkit-scrollbar-track {
  background: #dbdbdb9d; 
  border-radius: 25px;
}
 
/* Handle */
.custom-scroll::-webkit-scrollbar-thumb {
  background: rgb(182, 182, 182); 
  border-radius: 25px;
}

/* Handle on hover */
.custom-scroll::-webkit-scrollbar-thumb:hover {
  background: rgb(128, 128, 128); 
}

.row {
  width: auto;
  min-height: calc(100vh - 230px);
  padding: 25px 35px;
  display: flex;
  flex-direction: row;
  overflow-x: scroll;
}

.handle {
  cursor: pointer;
}

.title-bar {
  position: relative;
}

.drop-down {
  position: absolute;
  bottom: -85px;
  right: 25px;
  background: white;
  z-index: 1;
  box-shadow: 0px 3px 10px #00000028;
  border-radius: 5px;
  overflow: hidden;
}

.drop-down-menu {
  display: flex;
  padding: 15px 15px 10px 15px;
  justify-content: baseline;
  transition: 0.15s;
}

.drop-down-menu:last-of-type {
  padding: 10px 15px 15px 15px;
}

.drop-down-menu:hover {
  background: #eef3f9;
}

.drop-down-menu .icon {
  margin: 0px;
}

.drop-down-menu p {
  margin-left: 10px;
  vertical-align: text-bottom;
  line-height: 20px;
  font-family:font-bold;
}

/* // Dropdown Menu Animation */
.dropdown-enter-active{
  animation: modal 0.15s ease-in;
}

.dropdown-leave-active {
  animation: modal 0.15s ease-in reverse;
}

@keyframes modal {
  from {
    opacity: 0;
    transform: translateY(-10px) ;
  }

  to {
    opacity: 1;
    transform: translateY(0px) ;
  }
}

.title-holder {
  width: 275px;
  margin-right: 5px;
  /* max-height: 24px; */
}

.title-holder h4{
  width: 90%;
  text-overflow: ellipsis;
  overflow: hidden !important; 
  white-space: nowrap;
  display: inline-block;
  border: 2px solid transparent;
}

.title-holder textarea {
  line-height: 24px;
  font-size: 18px;
  font-family: font-bold;
  padding: 2px 0px;
  margin: 0px;
  width: 100%;
  max-height: 24px;
  resize: none;
  /* border: none; */
}

.icon {
  height: 20px;
  width: 20px;
  max-height: 20px;
  max-width: 20px;
  background: black;
  margin-top: 4px;
  margin-bottom: 15px;
  cursor: pointer;
  transition: 0.3s;
}

.menu-icon {
  background: url("../assets/icons/menu.png") no-repeat;
  background-size: 100%;
  opacity: 0.35;
  margin-top: 14px;
  margin-bottom: 5px;
}

.menu-icon:hover {
  background: url("../assets/icons/menu_hover.png") no-repeat;
  background-size: 100%;
  opacity: 1;
}

.edit-icon {
  background: url("../assets/icons/write.png") no-repeat;
  background-size: cover;
  opacity: 0.9;
}

.edit-icon:hover,
.drop-down-menu:hover .edit-icon {
  background: url("../assets/icons/write_hover.png") no-repeat;
  background-size: cover;
  opacity: 1;
}

.delete-icon {
  background: url("../assets/icons/delete.png") no-repeat;
  background-size: cover;
  opacity: 0.9;
}

.delete-icon:hover,
.drop-down-menu:hover .delete-icon {
  background: url("../assets/icons/delete_hover.png") no-repeat;
  background-size: cover;
  opacity: 1;
}

.close-icon {
  background: url("../assets/icons/close.png") no-repeat;
  background-size: cover;
}

.close-icon:hover {
  background: url("../assets/icons/close_hover.png") no-repeat;
  background-size: cover;
}

button {
  margin-bottom: 15px;
}

p {
  margin: 0px;
}

h4 {
  cursor: pointer;
  margin: 0px;
  line-height: 28px;
  font-size: 18px;
  font-family: font-bold;
}

.card {
  /* padding: 20px; */
  background: #f4f7fd;
  margin-right: 25px;
  /* margin-bottom: 25px; */
  min-width: 310px;
  max-width: 310px;
  float: left;
  transition: 0.5s;
  border-radius: 10px;
}

.list-item {
  padding: 15px;
  margin: 0px 0px 15px 0px;
  box-shadow: 0px 0px 10px #b8b8b82a;
  cursor: pointer;
  background: white;
  color: black;
  word-wrap: break-word;
  border-radius: 5px;
}

.list-item p{
  font-weight: 700;
}

.desc-text{
  margin-top: 10px;
  color: gray;
}

.desc-text p {
  font-size: 14px;
  font-weight: 400;
}

.add-card-button {
  padding: 8px;
  background: #e5ebf7;
  color: #e5ebf7;
  border-radius: 5px;
  font-family:font-bold;
  cursor: pointer;
  text-align: center;
  margin: 20px;
  margin-top:0px;
}

.add-card-button p {
  margin: 0px;
  text-align: center;
  color: #2674fa;
  font-family:font-bold;
}

.drag,
.sortable-drag {
  opacity: 1;
}

.ghost,
.sortable-ghost {
  opacity: 0;
}
@media screen and (max-width: 500px) {
  .row {
    min-height: calc(100vh - 285px);
  }
}
</style>