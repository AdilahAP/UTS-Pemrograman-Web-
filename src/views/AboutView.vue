
<template>
    <section class="cari">
        <input
            type="number"
            v-model="inputnomor"
            class="input"
            placeholder="Masukkan nomor surah"
        />
        <button @click="lihat" class="btn">Cari</button>
    </section>

    <section class="surah">
        <div class="judul2">
            <h1 v-if="namaSurah" class="judul">{{ namaSurah.name_simple }}</h1>
        </div>

        <div class="suara">
            <p v-if="audio">
                <audio controls class="suara2">
                    <source :src="audio.audio_url" type="audio/mpeg" />
                    
                </audio>
            </p>
        </div>

        <div class="bismillah">
            <p class="bismillah2">بِسْمِ اللهِ الرَّحْمٰنِ الرَّحِيْمِ</p>
        </div>
    </section>

    <section class="hasil">
        <div class="hasil2">
            <ul class="lista">
                <li class="ayat" v-for="ayat in ayah" :key="ayat.id">
                    {{ ayat.text_uthmani }} {{ ayat.text }}
                </li>
            </ul>
        </div>
    </section>
</template>
<script>


    import axios from "axios";
    export default {
        data() {
            return {
                ayat: [],
                audio: null,
                namaSurah: null,
                inputnomor: "",
            };
        },
        methods: {
            async lihat() {
                let nomor = this.inputnomor;
                let ayat = `https://api.quran.com/api/v4/quran/verses/uthmani?chapter_number=${nomor}`;
                let arti = "https://api.quran.com/api/v4/quran/translations/134?chapter_number=" + nomor;
                let judul = "https://api.quran.com/api/v4/chapters?language=en";
                let suara ="https://api.quran.com/api/v4/chapter_recitations/2?language=en";
                if (nomor <= 0 || nomor > 114) {
                    alert("masukkan nomor dengan benar!!");
                } else {
                    const reqAyat = axios.get(ayat);
                    const reqArti = axios.get(arti);
                    const reqJudul = axios.get(judul);
                    const reqSuara = axios.get(suara);
                    axios.all([reqAyat, reqArti, reqJudul, reqSuara]).then(
                        axios.spread((...response) => {
                            const responseAyat = response[0];
                            const responseArti = response[1];
                            const responseJudul = response[2];
                            const responseSuara = response[3];
                            const a = responseAyat.data.verses;
                            const b = responseArti.data.translations;
                            const gabung = (a, b) => {
                                const res = [];
                                for (let i = 0; i < a.length + b.length; i++) {
                                    if (i % 2 === 0) {
                                        res.push(a[i / 2]);
                                    } else {
                                        res.push(b[(i - 1) / 2]);
                                    }
                                }
                                return res;
                            };
                            this.ayah = gabung(a, b);
                            this.audio =
                                responseSuara.data.audio_files[nomor - 1];
                            this.namaSurah =
                                responseJudul.data.chapters[nomor - 1];
                        })
                    );
                }
            },
        },
    };
</script>
<style>
    .search {
        display: flexbox;
        margin: 45px 0 0 0;
        flex-direction: row;
        justify-content: center;
    }
    .input {
        height: 30px;
        border: 1px solid #323232;
        color: rgb(8, 8, 8);
    }
    .input:hover {
        border: 1px solid #6d9581;
    }
    .btn {
        background-color: #BBC8D0;
        border: 1px solid #323232;
        height: 32px;
        font-size: 12px;
        color: black;
        border-radius: 10%;
    }
    .btn:hover {
        color: rgb(19, 19, 18);
    }
    .suaraa {
        width: 70%;
        height: 30px;
    }
    .suara {
        margin: 50px 0;
        text-align: center;
    }
    .judul {
        text-align: center;
        font-size: 70px;
        color: #0b0b0b;
        margin: 60px 0 0 30px;
    }
    .bismillah {
        text-align: center;
        font-size: 50px;
        color: #463975;
    }
    .ayat {
        color: #100f0f;
        list-style: none;
        margin: 0 200px 50px 200px;
    }
    .ayat:nth-child(odd) {
        text-align: right;
        font-size: 40px;
    }
    .ayat:nth-child(even) {
        text-align: left;
        font-size: 15px;
        color: #0d0d0d;
    }
    @media screen and (max-width: 400px) {
        .ayat:nth-child(odd) {
            font-size: 20px;
            margin-bottom: 20px;
            margin-right: 10px;
        }
        .ayat:nth-child(even) {
            font-size: 10px;
            margin: 20px;
        }
        .ayat {
            margin: 0;
        }
    }
</style>