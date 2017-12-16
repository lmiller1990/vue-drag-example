<template>
  <div class="outer">
    <div class="board outer" @dragover="dragover" @dragend="dragend" :id="id" v-for="id in boardIds">
      <div class="title">
        Board Id {{ boards[id].id }} name: {{ boards[id].name }}
      </div>
      <div draggable  :id="piece.id" v-for="piece in boards[id].pieces" @dragstart="dragstart" :class="'piece board-' +  id.toString()">
        {{ piece.id }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',

  methods: {
    inBoard (boardId, pieceId) {
      return this.boards[boardId].pieces.filter(x => x.id == pieceId).length > 0
    },

    removePiece (boardId, pieceId) {
      let index = this.boards[boardId].pieces.findIndex(x => x.id == pieceId)
      this.boards[boardId].pieces.splice(index, 1)
    },

    dragover (e) {
      if (e.srcElement.className.includes('outer')) {
        this.currentBoard = e.srcElement.id
      }
    },

    dragend (e) {
        if (this.inBoard(this.currentBoard, this.currentPiece.id) === false) {
          // add new element
          this.boards[this.currentBoard].pieces.push({ id: this.currentPiece.id, boardId: parseInt(e.srcElement.id) })
          // remove old element
          this.removePiece(this.originalBoard, this.currentPiece.id)
        }
      this.pieceId = -1
    },

    dragstart (e) {
      this.originalBoard = e.srcElement.className[e.srcElement.className.length-1]
      this.currentPiece = this.boards[this.originalBoard].pieces.filter(x => x.id == e.srcElement.id)[0]
    }
  },
  
  data () {
    return {
      currentBoard: -1,
      originalBoard: -1,
      currentPiece: null,
      boardIds: [0, 1, 2],
      boards: {
        '2': {
          name: 'Other board',
          pieces: []
        },
        '0': {
          name: 'Top board',
          pieces: [
            { id: 10, boardId: 0 },
            { id: 49545, boardId: 0 },
            { id: 445, boardId: 0 },
          ]
        },
        '1': {
          name: 'Bottom board',
          pieces: [
            { id: 20, boardId: 1 },
            { id: 566, boardId: 1 },
            { id: 23, boardId: 1 },
            { id: 40, boardId: 1 }
          ] 
        }
      }
    }
  }

}
</script>

<style>
.board {
  border: 1px solid grey;
  min-height: 200px;
  margin: 10px;
}

.title {
  border-bottom: 2px solid black;
}

.piece {
  border: 1px solid pink;
  padding: 2px;
  margin: 2px;
}
</style>
