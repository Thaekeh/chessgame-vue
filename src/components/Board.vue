<template>
  <div id="board">
    <div class="row" v-for="(row, index) in squares" :key="index">
      <div
        class="square"
        :class="{
          even: squareColor(index, i),
          selected: col.selected,
          isSelected: isSelectedSquare(row, col),
        }"
        v-for="(col, i) in squares[index]"
        :key="i"
        @click="handleClick($event, index, i)"
      >
        <div v-if="col.image" class="background-square">
          <img class="image" v-if="col.image" :src="col.image" />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent } from "vue";

interface Square {
  title: string;
  image: string;
  selected: boolean;
  piece: string;
  white: boolean;
  location: {
    x: number;
    y: number;
  };
}

interface SelectedItem {
  x: number | undefined;
  y: number | undefined;
}

interface Coordinate {
  x: number;
  y: number;
}

interface Pieces {
  r: string;
  n: string;
  k: string;
  q: string;
  b: string;
  p: string;
}

type PieceKey = "r" | "n" | "k" | "q" | "b" | "p";

export default defineComponent({
  name: "Board",
  props: {
    rows: {
      type: Number,
      required: true,
    },
    cols: {
      type: Number,
      required: true,
    },
  },
  methods: {
    aFieldIsSelected() {
      if (this.selectedItem.x && this.selectedItem.y) return true;
    },
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
    selectedSquare() {
      let y = this.selectedItem.y;
      let x = this.selectedItem.x;
      if (!x || !y) return;
      return this.squares[y][x];
    },
    isSelectedSquare(row: number, col: number) {
      if (this.selectedItem.x === row && this.selectedItem.y === col) {
        console.log(
          `Is selected: Row: ${JSON.stringify(row)}, Col: ${JSON.stringify(
            col
          )}`
        );
        return true;
      }
    },
    setSelected() {
      // this.selectedItem
    },
    getSquare(x: number, y: number) {
      return this.squares[x][y];
    },
    handleClick(event: any, row: number, col: number) {
      let target = this.squares[row][col];
      let selected;
      if (!this.aFieldIsSelected()) {
        this.selectedItem = { x: row, y: col };
        // this.squares[row][col].selected = true;
        return;
      }
      if (this.aFieldIsSelected()) {
        if (
          this.isMoveAvailable(this.selectedSquare(), this.getSquare(row, col))
        ) {
          // console.log("is available");
        }
        // this.squares[row][col].selected = true;
      }

      // if (this.selectedItem) {
      //   let selectedRow = this.selectedItem.y;
      //   let selectedCol = this.selectedItem.x;
      //   if (selectedRow !== -1 && selectedCol !== -1) {
      //     selected = this.squares[selectedRow][selectedCol];
      //   }
      // }
      // let prev = this.selectedSquare()

      // if (this.selectedItem && !target.piece) {
      //   console.log(`selected: ${JSON.stringify(this.selectedItem)}`);
      //   // let prev = this.selectedSquare();
      //   if (this.isMoveAvailable(selected, target)) {
      //     this.movePiece(selected, target);
      //     this.resetSelected();
      //     return;
      //   } else {
      //     return;
      //   }
      // }
      // if (this.selectedItem && target.piece && this.selectedItem.x !== -1) {
      //   if (!this.isMoveAvailable(selected, target)) return;
      //   if (selected?.white !== target.white) {
      //     this.movePiece(selected, target);
      //     // target.image = selected?.image;
      //     // selected.image = undefined;
      //   }
      //   console.log("Piece detected: " + target.white);
      //   console.log(`selected: ${JSON.stringify(selected)}`);
      // }
      // if (this.selectedItem.x >= 0 && target.piece) {

      // let prev = this.selectedSquare();

      // let prev = this.selectedSquare();
      // if (prev.white === target.white) return;
      // }

      // this.selectedItem = { x: col, y: row };

      // this.selectedItem.selected = false;
      // this.selectedItem = target;
      // target.selected = true;

      // console.log(this.squares[row][col])
      // console.log(`row: ${row} - col: ${col}`)
    },
    movePiece(el, target) {
      // console.log(`target: ${JSON.stringify(target)}`);
      // if (el.piece === "pawn") {
      //   if (el.white) {
      //     if (target.location === el.location) return;
      //   }
      // }
      target.image = el.image;
      target.piece = el.piece;
      target.white = el?.white;
      el.image = undefined;
      el.piece = undefined;
    },
    showAvailableMoves(el: any, row: any, col: any) {
      let piece = el.piece;
      if (piece === "pawn") {
        if (el.white) {
          this.squares[row - 1][col].selected = true;
        } else {
          this.squares[row + 1][col].selected = true;
        }
      }
    },
    isMoveAvailable(source: any, target: any) {
      // console.log(
      //   `Source: ${source.location.x} - Target: ${target.location.x}`
      // );
      console.log("Source: " + JSON.stringify(source));
      let diffX = source.location.x - target.location.x;
      let diffY = source.location.y - target.location.y;
      let absoluteDiffX = Math.abs(diffX);
      let absoluteDiffY = Math.abs(diffY);

      // Pawn logic
      if (source.piece === "pawn") {
        console.log(`Is pawn`);

        if (target.location.x !== source.location.x) {
          if (diffX === 1) {
            if (!target.piece) return;
            if (source.white) {
              if (diffY === 1 && target.piece) {
                return true;
              }
            } else if (source.white) {
              if (absoluteDiffX === 1 && target.piece) {
                console.log("Pawn moving");

                return true;
              }
            }
          }
        } else {
          if (source.white) {
            if (target.location.y < source.location.y) {
              if (source.location.y === 1 || source.location.y === 6) {
                if (absoluteDiffY === 1 || absoluteDiffY === 2) {
                  return true;
                }
              } else {
                if (absoluteDiffY === 1) {
                  if (target.piece) return;
                  return true;
                }
              }
            }
          }
        }
      }
      if (source.piece === "bishop") {
        if (absoluteDiffX === absoluteDiffY) {
          console.log(`Diagonal movement`);
          if (this.findObstacleDiagonal(source, target, diffY)) return;
          this.movePiece(source, target);
          this.resetSelected();
          // Find pawns inbetween selected piece and target location
        }
      }
      return false;
    },
    findObstacleDiagonal(source: any, target: any, pathLength: any) {
      console.log(`From: ${JSON.stringify(source.location)}`);
      console.log(`To: ${JSON.stringify(target.location)}`);
      console.log(`Path: ${JSON.stringify(pathLength)}`);
      for (let i = 0; i < pathLength; i++) {
        console.log(
          `Update: ${source.location.y + i} - ${source.location.x + i}`
        );
        if (
          this.squares[source.location.y + i][source.location.x + i].piece ===
          undefined
        ) {
          console.log("Obstacle found");
          return true;
        }
      }
    },
    availableMoves(el: any, row: number, col: number) {
      let piece = el.piece;
      let moves = [];
      if (piece === "pawn") {
        if (el.white) {
          console.log(`X: ${row} - Y: ${col}`);

          moves.push({ x: col, y: row });
          // moves.push(this.squares[row-1][col]);
        } else {
          moves.push(this.squares[row + 1][col]);
        }
      }
      return moves;
    },
    resetSelected() {
      for (let i = 0; i < this.squares.length; i++) {
        for (let j = 0; j < this.squares.length; j++) {
          this.squares[i][j].selected = false;
        }
      }
      this.selectedItem = undefined;
    },
    getPiece(char: string) {},
    setupPieces() {
      const gameData = this.initialPiecePositions;
      let rows = gameData.split(" ")[0].split("/");
      console.log(`positions: ${rows[0]}`);
      for (let i = 0; i < rows.length; i++) {
        let charArray = rows[i].split("");
        for (let j = 0; j < charArray.length; j++) {
          const char = charArray[j];
          if (/[a-zA-Z]/.test(char)) {
            const piece: PieceKey | number = this.pieces[char];
            if (char.toLowerCase() === char) {
            }

            break;
          }
          console.log(char);
          j += parseInt(char);
        }
        //   for (let j = 0; j < this.cols; j++) {

        //   }
      }
    },
    setupBoard() {
      this.squares = [];
      for (let i = 0; i < this.rows; i++) {
        let tempArray = [];
        for (let j = 0; j < this.cols; j++) {
          tempArray.push({
            title: `a${j}`,
            image: this.getStats(i, j, "image"),
            selected: false,
            white: this.getStats(i, j, "white"),
            piece: this.getStats(i, j, "piece"),
            location: { x: j, y: i },
          });
        }
        this.squares.push(tempArray);
      }
    },
    getStats(row: number, col: number, stat: string) {
      let piece;
      let img;
      let color;
      let white;
      if (row === 0 || row === 1) {
        color = "black";
        white = false;
      }
      if (row === 6 || row === 7) {
        color = "white";
        white = true;
      }

      if (stat !== "white") {
        if (row === 0 || row === 7) {
          switch (col) {
            case 0:
            case 7:
              piece = "castle";
              img = `/castle-${color}.png`;
              break;
            case 1:
            case 6:
              piece = "knight";
              img = `/knight-${color}.png`;
              break;
            case 2:
            case 5:
              piece = "bishop";
              img = `/bishop-${color}.png`;
              break;
            case 3:
              piece = "queen";
              img = `/queen-${color}.png`;
              break;
            case 4:
              piece = "king";
              img = `/king-${color}.png`;
              break;
          }
        } else if (row === 1 || row === 6) {
          piece = "pawn";
          img = `/pawn-${color}.png`;
        } else {
          piece = undefined;
        }
      }
      if (stat === "piece") return piece;
      if (stat === "white") return white;
      if (stat === "image") return img;
    },
  },
  data() {
    return {
      pieces: {
        r: "rook",
        n: "knight",
        k: "knight",
        q: "queen",
        b: "bishop",
        p: "pawn",
      } as Pieces,
      squares: [
        [
          {
            title: "",
            image: "",
            selected: false,
            piece: "",
            white: true,
            location: { x: 0, y: 0 },
          },
        ],
      ] as Square[][],
      turn: "white",
      selectedItem: { x: undefined, y: undefined } as SelectedItem,
      initialPiecePositions:
        "rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1" as string,
      currentPiecePositions:
        "rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1" as string,
    };
  },
  mounted() {
    this.setupBoard();
    this.setupPieces();
    // for (let i = 0; i < this.rows; i++) {
    //   let tempArray = [];
    //   for (let j = 0; j < this.cols; j++) {
    //     // this.getImage(i, j)
    //     tempArray.push({
    //       title: `a${j}`,
    //       image: this.getStats(i, j, "image"),
    //       selected: false,
    //       white: this.getStats(i, j, "white"),
    //       piece: this.getStats(i, j, "piece"),
    //       location: { x: j, y: i },
    //     });
    //   }
    //   this.squares.push(tempArray);
    // }
    // console.log(document.querySelectorAll(".image"));
    // console.log(this.squares);
  },
  setup: () => {
    const count = ref(0);
    return { count };
  },
});
</script>

<style scoped>
#board {
  /* width: 100%; */
  margin-left: 200px;
}
.square {
  width: 100px;
  height: 100px;
  background-color: rgba(108, 108, 108, 0.975);
}

.square:hover {
  transition: all 100ms;
  background-color: rgba(48, 84, 61, 0.605);
  cursor: pointer;
}

.row {
  display: flex;
  width: 800px;
}

.even {
  background-color: rgb(235, 194, 153);
}

.selected {
  background-color: rgba(82, 157, 90, 0.673);
}

.image {
  user-select: none;
  -moz-user-select: none;
  -webkit-user-drag: none;
  -webkit-user-select: none;
  -ms-user-select: none;
}

.background-square {
  height: 100%;
}

.isSelected {
  background-color: red;
}
</style>
