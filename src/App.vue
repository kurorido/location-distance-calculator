<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-md-12">
          <p>工具使用方法</p>
          <ul>
            <li>先將地點加入清單</li>
            <li>按下開始查詢計算結果</li>
          </ul>
        </div>
        <div class="col-md-6">
          <div class="card">
            <div class="card-header">
              <h5 class="pb-0 mb-0">距離比較工具</h5>
            </div>
            <div class="card-body">

              <div class="form-group">
                <label for="current_address">目前位置地址或地點名稱</label>
                <input class="form-control" type="text" v-model="current_address">
              </div>

              <div class="form-group">
                <label for="destination">想去的地址或地點</label>
                <div class="input-group mb-3">
                    <input class="form-control" type="text" v-model="destination">
                    <div class="input-group-append">
                      <button @click="addItem" class="btn btn-success" type="button">加入清單</button>
                    </div>
                </div>
              </div>
            </div>
          </div>
        </div> <!-- .col-md-6 -->
        <div class="col-md-6">
          <div class="card">
            <div class="card-header">
              <h5 class="pb-0 mb-0">計算結果</h5>
            </div>
            <div class="card-body">
              <table class="table">
                <thead>
                  <tr>
                    <th>地址或地點</th>
                    <th>距離</th>
                    <th>預估時間</th>
                    <th></th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="destination in destinations">
                    <td v-text="destination.address"></td>
                    <td v-text="destination.distance_text"></td>
                    <td v-text="destination.duration_text"></td>
                    <td>
                      <button @click="removeItem(destination)" class="btn btn-sm btn-danger">移除</button>
                    </td>
                  </tr>
                </tbody>
              </table>

              <div class="form-group text-right">
                <button class="btn btn-primary" @click="calculate">開始查詢</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      current_address: '亞東醫院',
      destination: '',
      destinations: [
        {
          address: '黑露咖啡',
          distance_text: '-',
          duration_text: '-'
        },
        {
          address: '角公園',
          distance_text: '-',
          duration_text: '-'
        }
      ]
    }
  },
  methods: {
    addItem () {
      this.destinations.push({
        address: this.destination,
        distance_text: '-',
        duration_text: '-'
      })
    },
    removeItem (item) {
      this.destinations.splice(this.destinations.indexOf(item), 1)
    },
    calculate () {
      const service = new window.google.maps.DistanceMatrixService()
      const origins = [ this.current_address ]
      const locations = this.destinations.map(({ address }) => address)
      service.getDistanceMatrix({
        origins: origins,
        destinations: locations,
        travelMode: 'WALKING'
      }, (response, status) => {
        if (status !== 'OK') {
          alert('Error was: ' + status)
        } else {
          var originList = response.originAddresses
          for (var i = 0; i < originList.length; i++) {
            var results = response.rows[i].elements
            for (var j = 0; j < results.length; j++) {
              this.destinations[j].distance_text = results[j].distance.text
              this.destinations[j].duration_text = results[j].duration.text
            }
          }
        }
      })
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
