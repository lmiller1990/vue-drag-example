<template>
  <div class="outer">
    <div 
      class="board outer" 
      v-for="id in boardIds"
      :id="id" 
      @dragover="dragover" 
      @dragend="dragend" 
    >
      <div class="title">
        Board Id {{ id }} name: {{ boards[id].name }}
      </div>

      <div 
        v-for="piece in boards[id].pieces" 
        :id="piece.id" 
        :class="'piece piece-' + id + ' board-' +  id.toString()"
        @dragstart="dragstart" 
        @dragover="dragoverPiece"
        draggable 
      >
        <div class="piece-id">
          id: {{ piece.id }} 
        </div>

        <div class="piece-order">
          BoardId: {{ piece.boardId }} | Order: {{ piece.orderInBoard }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',

  methods: {
    dragoverPiece (e) {
      let targetId = e.target.id
      if (targetId && targetId != this.currentPiece.id) {
        let sourceBoardId = this.currentPiece.boardId
        let targetBoardId = e.srcElement.className.split(' ')[2].split('-')[1]
        // same board - switch positions
        if (sourceBoardId == targetBoardId) {
          let targetIndex = this.getIndexInBoard(sourceBoardId, targetId)
          let sourceIndex = this.getIndexInBoard(sourceBoardId, this.currentPiece.id)

          this.swapPiecesInBoard(sourceBoardId, targetIndex, sourceIndex)
        }
      }
    },

    swapPiecesInBoard (boardId, targetIndex, sourceIndex) {
      let temp = JSON.parse(JSON.stringify(this.boards[boardId].pieces[targetIndex]))

      this.boards[boardId].pieces.splice(targetIndex, 1, this.boards[boardId].pieces[sourceIndex])
      this.boards[boardId].pieces.splice(sourceIndex, 1, temp)

    },

    getIndexInBoard (boardId, pieceId) {
      return this.boards[boardId].pieces.findIndex(x => x.id == pieceId)
    },

    inBoard (boardId, pieceId) {
      return this.boards[boardId].pieces.filter(x => x.id == pieceId).length > 0
    },

    removePiece (boardId, pieceId) {
      let index = this.boards[boardId].pieces.findIndex(x => x.id == pieceId)
      this.boards[boardId].pieces.splice(index, 1)
    },

    addPieceToBoard (boardId, pieceId) {
      this.boards[boardId].pieces.push({ id: pieceId, boardId, orderInBoard: '?' })
    },

    dragover (e) {
      if (e.srcElement.className.includes('outer')) {
        this.currentBoard = e.srcElement.id
      }
    },

    dragend (e) {
      if (this.inBoard(this.currentBoard, this.currentPiece.id) === false) {
        // add new element
        this.addPieceToBoard(this.currentBoard, this.currentPiece.id)
        // remove old element
        this.removePiece(this.originalBoard, this.currentPiece.id)
      }

      this.pieceId = -1
    },

    dragstart (e) {
      // get the board from the class name
      this.originalBoard = e.srcElement.className.split('-')[2]
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
        '0': {
          name: '上ボード',
          pieces: [
            { id: 10, boardId: 0, orderInBoard: 0 },
            { id: 49545, boardId: 0, orderInBoard: 1 },
            { id: 445, boardId: 0, orderInBoard: 2 },
          ]
        },
        '1': {
          name: '中ボード',
          pieces: [
            { id: 20, boardId: 1, orderInBoard: 0 },
            { id: 566, boardId: 1, orderInBoard: 1 },
            { id: 23, boardId: 1, orderInBoard: 2 },
            { id: 40, boardId: 1, orderInBoard: 3 }
          ] 
        },
        '2': {
          name: '下ボード',
          pieces: []
        },
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
  display: flex;
  justify-content: space-between;
}
</style>
