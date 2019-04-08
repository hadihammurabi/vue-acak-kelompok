<template>
  <div class="container" id="wrapper">
    <div class="columns">
      <div class="column is-2 is-offset-1">
        <div class="card">
          <div class="card-header">
            <div class="card-header-title">Nomor Tim</div>
          </div>
          <div class="card-content has-text-centered">
            <h1 class="is-size-1">{{tim}}</h1>
          </div>
        </div>
      </div>
      <div class="column is-6">
        <div class="card">
          <div class="card-header">
            <div class="card-header-title">Nama</div>
          </div>
          <div class="card-content">
            <h1 class="is-size-1">{{!!hasil ? hasil : '-'}}</h1>
          </div>
        </div>
      </div>
      <div class="column is-2">
        <button
          class="button is-fullwidth is-info"
          :disabled="disabled"
          style="height: 100%"
          v-if="!nextactive && !resetactive"
          @click="acak()">
          <span class="is-size-3">ACAK</span>
        </button>
        <button
          class="button is-fullwidth is-success"
          :disabled="disabled"
          style="height: 100%"
          v-if="nextactive && !resetactive"
          @click="next()">
          <span class="is-size-3">NEXT</span>
        </button>
        <button
          class="button is-fullwidth is-danger"
          style="height: 100%"
          v-if="resetactive"
          @click="reset()">
          <span class="is-size-3">RESET</span>
        </button>
      </div>
    </div>
    <div class="columns" id="result-wrapper">
      <div class="column is-3">
        <div class="card">
          <div class="card-header">
            <h1 class="card-header-title">Tim 1</h1>
          </div>
          <div class="card-content">
            <ul class="panel">
              <li
                class="panel-block"
                v-for="(nama, i) in conclution[0].anggota"
                :key="i">
                {{i+1}}. {{nama}}
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div class="column is-3">
        <div class="card">
          <div class="card-header">
            <h1 class="card-header-title">Tim 2</h1>
          </div>
          <div class="card-content">
            <ul class="panel">
              <li
                class="panel-block"
                v-for="(nama, i) in conclution[1].anggota"
                :key="i">
                {{i+1}}. {{nama}}
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div class="column is-3">
        <div class="card">
          <div class="card-header">
            <h1 class="card-header-title">Tim 3</h1>
          </div>
          <div class="card-content">
            <ul>
              <li
                class="panel-block"
                v-for="(nama, i) in conclution[2].anggota"
                :key="i">
                {{i+1}}. {{nama}}
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div class="column is-3">
        <div class="card">
          <div class="card-header">
            <h1 class="card-header-title">Tim 4</h1>
          </div>
          <div class="card-content">
            <ul class="panel">
              <li
                class="panel-block"
                v-for="(nama, i) in conclution[3].anggota"
                :key="i">
                {{i+1}}. {{nama}}
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    peserta: [
      'hadi misnanto',
      'hidayat',
      'hammurabi',
      'joko',
      'priyo',
      'wanito',
      'sukoco',
      'karto',
      'mojo',
      'slamet',
      'thomas',
      'julia',
      'simons',
      'ilham',
      'mirsa',
      'musle',
    ],
    conclution: [
      {
        nomor: 1,
        anggota: [],
      },
      {
        nomor: 2,
        anggota: [],
      },
      {
        nomor: 3,
        anggota: [],
      },
      {
        nomor: 4,
        anggota: [],
      },
    ],
    maxanggota: 4,
    sampah: [],
    hasil: '',
    tim: 1,
    disabled: false,
    nextactive: false,
    resetactive: false,
  }),
  created() {
    this.peserta = this.peserta.map((nama) => {
      const tmp = nama.split(' ');
      let full = '';

      tmp.forEach((kata) => {
        full = `${full} ${kata.charAt(0).toUpperCase()}${kata.slice(1)}`;
      });

      return full;
    });

    const fromstorage = JSON.parse(window.localStorage.getItem('timundian'));
    if (!fromstorage) {
      return;
    }

    this.conclution = fromstorage.conclution;
    this.peserta = fromstorage.peserta;
    this.tim = fromstorage.tim;

    if (this.peserta.length <= 0) {
      this.resetactive = true;
    }
  },
  methods: {
    acak() {
      const index = this.getRandom();

      this.hasil = this.peserta[index];
      this.conclution[this.tim - 1].anggota.push(this.hasil);
      this.sampah.push(this.hasil);

      this.peserta = this.peserta.filter((nama, i) => i !== index);

      if (this.conclution[this.tim - 1].anggota.length >= 4) {
        this.nextactive = true;
      }

      if (this.tim === 4 && this.conclution[3].anggota.length >= 4) {
        this.tim = 4;
        this.resetactive = true;
      }
      window.localStorage.setItem('timundian', JSON.stringify({
        conclution: this.conclution,
        peserta: this.peserta,
        tim: this.tim,
      }));
    },
    getRandom() {
      return Math.floor(Math.random() * this.peserta.length) + 0;
    },
    next() {
      if (this.tim < 4) {
        this.tim += 1;
        this.hasil = '';
        this.nextactive = false;
      }
    },
    reset() {
      this.conclution = this.conclution.map((tim, i) => ({
        nomor: i + 1,
        anggota: [],
      }));
      this.peserta = this.sampah;
      this.hasil = '';
      this.tim = 1;
      this.disabled = false;
      this.nextactive = false;
      this.resetactive = false;
      this.sampah = [];
      window.localStorage.removeItem('timundian');
    },
  },
};
</script>


<style>
html {
  height: 100%;
}

body {
  height: 100%;
  background-color: #ccd;
}

#wrapper {
  padding-top: 20vh;
}

#result-wrapper {
  padding-top: 10vh;
}
</style>
