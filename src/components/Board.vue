<template>
  <div id="board">
    <div class="row" v-for="(row, index) in squares" :key="index">
      <div class="square" :class="{'even': squareColor(index, i)}" v-for="(col, i) in squares[index]" :key="i" @click="handleClick($event, index, i)">
        <div v-if="col.image" :class="{'selected': col.selected}" class="background-square" >
          <img class="image" v-if="col.image" :src="col.image"  />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent } from 'vue'

interface Square {
  title: String,
  image: String,
  selected: Boolean
}

export default defineComponent({
  name: 'Board',
  props: {
    rows: {
      type: Number,
      required: true
    },
    cols: {
      type: Number,
      required: true
    }
  },
  methods: {
    squareColor(row: number, col: number) {
      if (row % 2 === 0) {
        if (col % 2 === 0) {
          return true;
        }
      } else {
        if (col % 2 !== 0) {
          return true;
        }
      }
    },
    selected(row: number, col: number) {
      return true;
    },
    handleClick(event: CustomEvent, row: number, col: number) {
      console.log(event.target);
      // let el = event.target;
      let el = this.squares[row][col]
      this.selectedItem.selected = false;
      this.selectedItem = el;
      // console.log(`El before: ${JSON.stringify(el)}`);
      el.selected = true;
      // console.log(`El after: ${JSON.stringify(el)}`);
      
      this.availableMoves(el)
      
      
      console.log(this.squares[row][col])
      console.log(`row: ${row} - col: ${col}`)
    },
    availableMoves(el: {}) {
      let piece = el.piece;
      if (piece === 'pawn') {
        
      }
      
    },
    getStats(row: number, col: number, stat: string) {
      let piece;
      let img;
      let color;
      let white;
      if (row === 0 || row === 1) {
        color = 'black';
        white = false;
      } 
      if (row === 6 || row === 7) {
        color = 'white';
        white = true;
      } 

      if (stat !== 'white') {
        if (row === 0 || row === 7) {
          switch (col) {
            case 0:
            case 7:
              piece = 'castle';
              img = `/castle-${color}.png`;
              break;
            case 1:
            case 6:
              piece = 'knight';
              img = `/knight-${color}.png`;

              break;
            case 2:
            case 5:
              piece = 'bishop';
              img = `/bishop-${color}.png`;
              break;
            case 3:
              piece = 'queen';
              img = `/queen-${color}.png`;
              break;
            case 4:
              piece = 'king';
              img = `/king-${color}.png`;

              break;
          }
        } else if (row === 1 || row === 6) {
          piece = 'pawn';
          img = `/pawn-${color}.png`;
        }
      }
        if (stat === 'piece') return piece;
        if (stat === 'white') return white;
        if (stat === 'image') return img;

    }
  },
  data() {
    return {
      squares: [[{title: String, image: String, selected: Boolean, piece: String, white: Boolean }]],
      selectedItem: {
        title: String,
        image: String,
        selected: Boolean
      }
    }
  },
  mounted() {
    this.squares = []
      for (let i = 0; i < this.rows; i++) {
        let  tempArray = [];
        for (let j = 0; j < this.cols; j ++) {
          // this.getImage(i, j)
          tempArray.push({title: `a${j}`, image: this.getStats(i, j, 'image'), selected: false, white: this.getStats(i, j, 'white') ,piece: this.getStats(i, j, 'piece')})
        }
        this.squares.push(tempArray)
      }
      console.log(document.querySelectorAll('.image'))
      console.log(this.squares)
  },
  setup: () => {
    const count = ref(0)
    return { count }
  }
})
</script>

<style scoped>
#board {
  width: 100%;
}
.square {
  width: 100px;
  height: 100px;
  background-color: rgba(108, 108, 108, 0.975);
}

.row {
  display: flex;
  
}

.even {
  background-color: rgb(235, 194, 153);
}

.selected {
  background-color: rgba(82, 157, 90, 0.673)
}

.background-square {
  height: 100%;
}
</style>
