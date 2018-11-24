<template>
  <div>
    <div class="content">
      <section class="arg-wrapper">
        <div
          v-for="item in items"
          class="arg-item"
          :style="'height: ' + (item.num + 5) + 'px'"
          :key="item.id"
          :active="item.active"
        >
        </div>
      </section>
      <table>
        <tr>
          <td><button @click="shuffle()">シャッフル</button></td>
          <td><button @click="quickSort(0, numbers.length - 1)">クイックソート</button></td>
          <td><button @click="bubbleSort()">バブルソート</button></td>
          <td><button @click="selectSort()">選択ソート</button></td>
          <td><button @click="shellSort()">シェルソート</button></td>
        </tr>
      </table>
      <div>
        <label>遅延(ミリ秒)</label>
        <input v-model="delay">
      </div>
      source : <a href="https://github.com/yamamoto7/-algorithm--viewer">hhttps://github.com/yamamoto7/-algorithm--viewer</a>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      items: [],
      numbers: [],
      delay: 1
    }
  },
  mounted () {
    for (var i = 0; i < 500; i++) {
      this.numbers[i] = i
      this.items.push({id: i, num: this.numbers[i], active: false})
    }
  },
  methods: {
    async shuffle () {
      for (var i = this.numbers.length - 1; i >= 0; i--) {
        var r = Math.floor(Math.random() * (i + 1))
        var tmp = this.numbers[i]
        this.numbers[i] = this.numbers[r]
        this.numbers[r] = tmp
        this.items[i].num = this.numbers[i]
        await this.timeout(this.delay)
      }
    },
    async quickSort (left, right) {
      // 範囲の中からランダムにピボット選定し、ピボットを可視化。
      const PIVOT = this.numbers[Math.floor((left + right) / 2)]
      this.items[PIVOT].active = true

      var l = left
      var r = right

      while (1) {
        while (this.numbers[l] < PIVOT) l++
        while (PIVOT < this.numbers[r]) r--
        if (r <= l) break

        // 値を交換している間だけ可視化されるようにする。
        const tmp = this.numbers[l]
        this.numbers[l] = this.numbers[r]
        this.numbers[r] = tmp

        this.items[l].num = this.numbers[l]
        this.items[r].num = this.numbers[r]

        this.items[l].active = true
        this.items[r].active = true
        await this.timeout(this.delay)
        this.items[l].active = false
        this.items[r].active = false

        l++
        r--
      }

      this.items[PIVOT].active = false
      if (left < l - 1) await this.quickSort(left, l - 1)
      if (r + 1 < right) await this.quickSort(r + 1, right)
    },
    async shellSort () {
      for (var step = parseInt(this.numbers.length / 2); step > 0; step = parseInt(step / 2)) {
        console.log(step)
        // 間隔をあけて挿入ソートを行う
        // 挿入する値を１つずつ取り出す繰り返し
        for (var i = step; i < this.numbers.length; i++) {
          // 挿入する値を一時的に保存する
          var tmp = this.numbers[i]
          this.items[i].active = true
          // 取り出した位置から前に向かって比較するfor文
          for (var j = i; j >= step; j -= step) {
            if (this.numbers[j - step] > tmp) {
              // 挿入値が小さい場合ステップ幅分後ろにずらすが、その間可視化する
              this.items[j - step].active = true
              this.numbers[j] = this.numbers[j - step]
              this.items[j].num = this.numbers[j]
              this.items[j].active = true
              await this.timeout(this.delay)
              this.items[j - step].active = false
              this.items[j].active = false
            } else {
              // 挿入する値が小さくなければ、処理を止める
              break
            }
          }
          // ずらす処理が終わったところに「挿入する値」をいれる
          this.items[j].active = true
          this.numbers[j] = tmp
          await this.timeout(this.delay)
          this.items[j].active = false
          this.items[i].active = false
          this.items[j].num = this.numbers[j]
        }
      }
    },
    async selectSort () {
      // 「最小値を入れる位置」を先頭から順番に選択していくfor文
      for (var i = 0; i < this.numbers.length - 1; i++) {
        // 最小値を探すアルゴリズム
        // 先頭の値を暫定の最小値として初期設定する
        var min = this.numbers[i]
        // 先頭の位置を保存する
        var k = i
        // 隣の位置から最後まで、最小値との比較を繰り返す
        for (var j = i + 1; j < this.numbers.length; j++) {
          // 比較して最小値を更新するif文
          if (min > this.numbers[j]) {
            min = this.numbers[j]
            k = j
          }
          this.items[j].active = true
          await this.timeout(this.delay)
          this.items[j].active = false
        }
        // 交換するアルゴリズム
        // 「先頭の値」と「最小値の値」を交換する
        var tmp = this.numbers[i]
        this.numbers[i] = this.numbers[k]
        this.numbers[k] = tmp
        this.items[i].num = this.numbers[i]
        this.items[k].num = this.numbers[k]
      }
    },
    async bubbleSort () {
      for (var i = 0; i < this.numbers.length; i++) {
        // 後ろから前に向かって小さい値を浮かび上がらせるfor文
        for (var j = this.numbers.length - 1; j > i; j--) {
          // 隣りあう２つの値を比べて、後ろが小さければ交換する
          if (this.numbers[j] < this.numbers[j - 1]) {
            var tmp = this.numbers[j]
            this.numbers[j] = this.numbers[j - 1]
            this.numbers[j - 1] = tmp
            this.items[i].num = this.numbers[i]
            this.items[j].num = this.numbers[j]
            this.items[i].active = true
            this.items[j].active = true
            await this.timeout(this.delay)
            this.items[i].active = false
            this.items[j].active = false
          }
        }
      }
    },
    timeout (ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }
  }
}
</script>

<style scoped>
.content{
  width: 1000px;
}
.arg-wrapper{
  display: flex;
  align-items: flex-end;
}
.arg-item{
  width: 2px;
  border-top: 2px solid rgb(120,150,240);
  border-bottom: 2px solid rgb(120,150,240);
}
.arg-item[active]{
  background-color: rgb(240, 120, 100);
}
</style>
