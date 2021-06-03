<template>
  <div id="app">
    <div class="box-lists">
      <ul class="lists" ref="lists">
        <li class="list-and-items" 
          v-for="(list, key) in lists"
          :key="key"
          :id="key"
          >
          <div class="list">
            <span @click="changeDropDownList(key)">
              <p v-if="list.expandList">&#x25BC;</p>
              <p v-else>&#x25B6;</p>
            </span>
            <input type="checkbox" :checked="list.checked ? 'checked' : ''" :id="`idCheckboxList${key}`" @click="selectAllCheckbox(key)">
            <p>{{ list.name }}</p>

          </div>
          <ul class="items" v-if="list.expandList">
            <li 
              class="item"
              v-for="(i, id) in list.items"
              :key="id"
              >
              <div class="item-checkbox-box">
                <input type="checkbox" :checked="i.checked ? 'checked' : ''" @click="selectItem(key, id)">
                <p >Item</p>
              </div>
              <div class="number-and-color-item">
                <!-- list - key ; item - id -->
                <input type="number" v-model="i.quantity" @keyup="updateNumberOfCubes(key, id, i.quantity)">
                <input type="color"  :value="i.color" @input="i.color = $event.target.value" @change="cubStyle()">
              </div>
            </li>

            
          </ul>
        </li>
      </ul>
      
    </div>
    <div class="box-display">
      <ul class="display-list">
        <li class="display-item"
          v-for="(list, key) in lists"
          :key="key"
          >
          <div v-if="list.expandList">
            <div>
              <button>Сортировка</button>
            </div>
            <div  class="display-list-box">
              <div 
                :class="list.className" 
                v-for="(item, id) in list.items" 
                :key="id"
                >
                <!-- eslint-disable vue/no-use-v-if-with-v-for,vue/no-confusing-v-for-v-if -->
                <div 
                  class="display-item-box cub"
                  v-for="i in Number(item.quantity)" 
                  v-if="item.checked"
                  :key="i"
                  :ref="key+id+i"
                  :class="list.className+id"  
                  :style="{background: item.color}"
                  >
                </div>
              </div>
            </div>
          </div>          
        </li>
      </ul>
    </div>

    
    <router-view/>
  </div>
</template>

<script>
  export default {
    data: () => ({
      classNameCub: '_cub',
      lists: [
        {
          name: 'List 1',
          className: 'list_one_',
          expandList: false,
          checked: false,
          itemsQuantity: null,
          items: {} 
        },
        {
          name: 'List 2',
          className: 'list_two_',
          expandList: false,
          checked: false,
          itemsQuantity: null,
          items: {} 
        },
        {
          name: 'List 3',
          className: 'list_three_',
          expandList: false,
          checked: false,
          itemsQuantity: null,
          items: {} 
          
        },
        {
          name: 'List 4',
          className: 'list_Four_',
          expandList: false,
          checked: false,
          itemsQuantity: null,
          items: {} 
        },
        {
          name: 'List 5',
          className: 'list_Five_',
          expandList: false,
          checked: false,
          itemsQuantity: null,
          items: {} 
        }
      ],
      item: {
        quantity: 2,
        color: null,
        checked: false
      },
    }),

    

    mounted() {
      for (let list in this.lists) {
        this.lists[list].itemsQuantity = this.getRandomInt()
        for (let i = 0; i <= this.lists[list].itemsQuantity; i++ ) {
          this.lists[list].items[i] = {...this.item}
          this.lists[list].items[i].quantity = this.getRandomInt()
          this.lists[list].items[i].color = this.randomColor()
        }
      }
    },
    
    methods: {
      getRandomInt() {
        let min = Math.ceil(4)
        let max = Math.floor(10)
        return Math.floor(Math.random() * (max - min) + min)
      },


      changeDropDownList(key) {
        return this.lists[key].expandList ? this.lists[key].expandList = false : this.lists[key].expandList = true 
      },


      selectAllCheckbox(key) {
        if (!this.lists[key].checked) {
          this.lists[key].checked = true
          for (let item in this.lists[key].items) {
            this.$set(this.lists[key].items[item], `checked`, true)
          }        
        } else if (this.lists[key].checked) {
          this.lists[key].checked = false
          for (let item in this.lists[key].items) {
            this.$set(this.lists[key].items[item], `checked`,  false)
         }
        }
        
      },


      selectItem(listId, itemId) {
        if (this.lists[listId].items[itemId].checked) {
          this.lists[listId].items[itemId].checked = false
        } else {
          this.lists[listId].items[itemId].checked = true
        }
        this.checkingItemForTrue(listId)
        return this.$forceUpdate()
      },


      checkingItemForTrue(listId) {
        let countChecked = null
        let focusList = document.querySelector(`#idCheckboxList${listId}`)
        for (let item in this.lists[listId].items) {
          if (this.lists[listId].items[item].checked) {
            countChecked +=1
          }
        }
        if ((countChecked > 0) && (countChecked < Object.keys(this.lists[listId].items).length)) {
          focusList.indeterminate = true  
        } else if (Object.keys(this.lists[listId].items).length == countChecked) {
          focusList.indeterminate = false
          this.lists[listId].checked = true
          this.$forceUpdate()
        } else {
          focusList.indeterminate = false
          this.lists[listId].checked = false
        }
      },


      cubStyle() {
        return this.$forceUpdate()
      },


      updateNumberOfCubes(listId, itemId, value) {        
        if (value.length > 2 ) {
          alert(`value не может привысить двух значное число, будет установлено 0`) 
          value = 0
        }
        if (value >= 0) {
          this.$set(this.lists[listId].items[itemId], `quantity`, value)
        } else {
          alert('отрицательное значение не может быть установленно') 
          value = 0
        }
        return this.$forceUpdate()
      },

      randomColor () {
        let letters = '0123456789ABCDEF';
        let color = '#';
        for (let i = 0; i < 6; i++) {
          color += letters[Math.floor(Math.random() * 16)];
        }
        return color;
      },

    }
    
    
  }

</script>


<style lang="scss">
  
#app {
  display: flex;
  justify-content: space-around;
}

input[type="number"] {
	-moz-appearance: textfield;
	-webkit-appearance: textfield;
	appearance: textfield;
  text-align: center;
  width: 20px;
  height: 23px;
  margin: 5px;
  border: none;
  &:focus {
    outline: none;
  }
}
 
input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button {
	display: none;
}

input[type="color"] {
  border: none;
  background: none;
  width: 20px;
  margin: 5px;
  align-self: center;
}

.box-lists, .box-display {
 width: 43%;
 height: 100vh; 
 border: solid 1px;
 overflow: auto;
}

p {
  margin: 0px;
}

li {
  list-style-type: none;
} 

.list, .item-checkbox-box, .number-and-color-item, .item {
  display: flex;
  align-items: baseline;
}

.list>span {
  cursor: pointer;
}

.number-and-color-item, .item {
  justify-content: space-around;
}

.display-list {
  
  padding: 0px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.display-item {
  margin: 10px;
  border: 1px solid;
  width: 90%;
  min-height: 20px;
}

.cub {
  margin: 2px;
  width: 20px;
  height: 20px;
  background: black;
}

.display-list-box {
  
  &>div {
    display: inline-flex;
    flex-wrap: wrap;
    flex-direction: row;
    
  }
}
  


</style>
