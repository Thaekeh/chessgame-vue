<template>
  <div id="board">
    <div class="row" v-for="(row, index) in squares" :key="index">
      <div class="square" :class="squareColor(index, i)" v-for="(col, i) in squares[index]" :key="i">
        <!-- {{ col.image }} -->
        <img v-if="col.image" :src="col.image"  />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent } from 'vue'
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
          return 'even';
        }
      } else {
        if (col % 2 !== 0) {
          return 'even';
        }
      }
    },
    getImage(row: number, col: number) {
        let prefix = `../../public/`;
        let img;
        // Black pieces
        if (row === 0) {
          switch (col) {
            case 0:
            case 7:
              img = prefix + 'castle-black.png';
        console.log(`Image: ` + img)

              // return 'castle-black.png';
            case 1:
            case 6:
              return 'knight-black.png';
            case 2:
            case 5:
              return 'bishop-black.png'
          }
        } else if (row === 1) {
          return 'pawn-black.png';
        }

        return img;
    }
  },
  data() {
    return {
      squares: [{}]
    }
  },
  mounted() {
      for (let i = 0; i < this.rows; i++) {
        let  tempArray = [];
        for (let j = 0; j < this.cols; j ++) {
          // this.getImage(i, j)
          tempArray.push({title: `a${j}`, image: this.getImage(i, j)})
        }
        this.squares.push(tempArray)
      }
      console.log(this.squares)
  },
  // computed: {
  //   squares() {
  //     for (let i = 0; i < this.rows; i++) {
  //       this.squares.push
  //     }
  //   }
  // },
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
</style>
