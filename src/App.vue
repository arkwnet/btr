<template>
  <div>
    <div class="header">
      <div class="wrapper">
        <h1>ぼざろ写真メーカー</h1>
      </div>
    </div>
    <div class="main">
      <div class="wrapper">
        <div class="photo"><img :src="imageUrl" /></div>
        <canvas class="canvas" ref="canvas" width="1920" height="1080"></canvas>
        <div class="control">
          <p>画像ファイルを選択：<input class="file" type="file" ref="file" /></p>
          <p>
            <input
              type="text"
              class="text font-36px"
              v-model="title"
              placeholder="しばらくお待ちください"
            />
          </p>
          <p>
            <input type="text" class="text font-24px" v-model="prefecture" placeholder="東京都" />
          </p>
          <p><input type="text" class="text font-24px" v-model="place" placeholder="野獣邸" /></p>
          <div class="button" @click="load">生成</div>
        </div>
        <p class="center">
          右クリックメニュー「名前を付けて画像を保存」またはロングタップで保存できます。
        </p>
      </div>
    </div>
    <br />
    <div class="wrapper">
      <Adsense :data-ad-client="adClient" :data-ad-slot="adSlot"> </Adsense>
    </div>
    <div class="footer">
      <div class="wrapper">
        <p>
          <a href="https://find47.jp/ja/i/Vz4MJ" target="blank">富山県 黒部ダム</a> (©
          <a href="https://app.find47.jp/ja/u/qI1hg" target="blank">Yuto</a>,
          <a href="https://creativecommons.org/licenses/by/4.0/deed.ja" target="blank"
            >クリエイティブ・コモンズ・ライセンス 表示 4.0 国際</a
          >) を改変して作成
        </p>
        <br />
        <p>Version {{ version }}</p>
        <p>
          Copyright (c) 2023 <a href="https://arkw.net/">Sora Arakawa</a><br />Licensed under the
          MIT License
        </p>
        <p>This application is non-official product of "Bocchi the Rock!".</p>
        <p><a href="https://github.com/arkwnet/btr">GitHub Repository</a></p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      status: false,
      image: new Image(),
      imageUrl: '',
      imageWidth: 1920,
      imageHeight: 1080,
      template: new Image(),
      canvas: null,
      context: null,
      adClient: import.meta.env.VITE_AD_CLIENT,
      adSlot: import.meta.env.VITE_AD_SLOT,
      version: import.meta.env.PACKAGE_VERSION,
      title: 'しばらくお待ちください',
      prefecture: '富山県',
      place: '黒部ダム'
    }
  },
  mounted() {
    this.$gtag.pageview('/')
    this.imageUrl = import.meta.env.BASE_URL + 'img/loading.png'
    this.canvas = this.$refs.canvas
    this.context = this.canvas.getContext('2d')
    this.template.src = import.meta.env.BASE_URL + 'img/template.png'
    const vm = this
    document.fonts.ready.then(function () {
      setTimeout(function () {
        vm.draw(import.meta.env.BASE_URL + 'img/noimage.jpg')
        vm.status = true
      }, 1000)
    })
  },
  methods: {
    load() {
      if (this.status == true) {
        this.imageUrl = import.meta.env.BASE_URL + 'img/loading.png'
        const vm = this
        let reader = new FileReader()
        reader.onload = function (e) {
          vm.draw(e.target.result)
        }
        reader.readAsDataURL(this.$refs.file.files[0])
      }
    },
    draw(src) {
      let vm = this
      vm.image.src = src
      vm.image.onload = function () {
        vm.context.drawImage(vm.image, 0, 0, vm.imageWidth, vm.imageHeight)
        vm.context.drawImage(vm.template, 0, 0, vm.imageWidth, vm.imageHeight)
        vm.context.strokeStyle = 'black'
        vm.context.lineJoin = 'round'
        vm.context.miterLimit = '5'
        vm.context.font = '120px "ＭＳ Ｐゴシック", JPAPGothic, sans-serif'
        vm.context.lineWidth = '10'
        vm.context.strokeText(
          vm.title,
          (vm.imageWidth - vm.context.measureText(vm.title).width) / 2,
          494
        )
        vm.context.fillStyle = 'white'
        vm.context.fillText(
          vm.title,
          (vm.imageWidth - vm.context.measureText(vm.title).width) / 2,
          494
        )
        vm.context.font = '42px "ＭＳ Ｐゴシック", JPAPGothic, sans-serif'
        vm.context.lineWidth = '5'
        vm.context.strokeText(
          vm.prefecture,
          (410 - vm.context.measureText(vm.prefecture).width) / 2,
          953
        )
        vm.context.strokeText(vm.place, (410 - vm.context.measureText(vm.place).width) / 2, 1003)
        vm.context.fillText(
          vm.prefecture,
          (410 - vm.context.measureText(vm.prefecture).width) / 2,
          953
        )
        vm.context.fillText(vm.place, (410 - vm.context.measureText(vm.place).width) / 2, 1003)
        vm.imageUrl = vm.canvas.toDataURL()
      }
    }
  }
}
</script>
