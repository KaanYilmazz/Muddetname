<template>
    <div class="container">
        <v-row>
            <v-col cols="12" md="6">
                <v-card>
                    <v-card-title class="justify-center">
                        İnfaz (Yatar) ve Denetimli Serbestlik Hesaplama
                    </v-card-title>
                    <v-card-text>
                        <v-row>
                            <v-col cols="12" md="6">
                                Toplam Ceza
                                <v-row>
                                    <v-col md="4">
                                        <v-text-field v-model="hesapYil" label="Yil" type="number"/>
                                    </v-col>
                                    <v-col md="4">
                                        <v-text-field  v-model="hesapAy" label="Ay" type="number"/>
                                    </v-col>
                                    <v-col md="4">
                                        <v-text-field  v-model="hesapGun" label="Gün" type="number"/>
                                    </v-col>
                                </v-row>
                                <v-select
                                        :items="sucTurleri"
                                        item-text="tur"
                                        item-value="id"
                                        label="Suç Türü Seçiniz"
                                        v-model="secilenSuc"
                                        required
                                ></v-select>
                                <v-row>
                                    <v-col md="6">Suç Tarihi :
                                        <datepicker v-model="sucTarihi" placeholder="Suç Tarihi"
                                                    :language="tr"></datepicker>
                                    </v-col>
                                    <v-col md="6">Dogum Tarihi:
                                        <datepicker v-model="dogumTarihi" placeholder="Dogum Tarihi"
                                                    :language="tr"></datepicker>
                                    </v-col>
                                </v-row>
                                <p style="color: red" v-if="yasHataMesaji">Tarihleri doğru girdiğinizden emin olun!</p>
                            </v-col>
                            <v-col md="6">
                                <v-radio-group label="Mahsup" row v-model="mahsupVarMi">
                                    <v-spacer></v-spacer>
                                    <v-radio label="Var" :value="true"></v-radio>
                                    <v-radio label="Yok" :value="false"></v-radio>
                                </v-radio-group>
                                        <v-text-field v-if="mahsupVarMi===true" v-model="mahsupGun" label="Mahsup Günü" type="number"/>
                                <v-select
                                        :items="cezaTurleri"
                                        v-model="secilenCeza"
                                        item-value="id"
                                        item-text="tur"
                                        label="Ceza Türü Seçiniz"
                                        required
                                ></v-select>
                                <v-radio-group label="Tekerrüre Esas Sabika Kaydi:" row v-model="secilenDurum">
                                    <v-spacer></v-spacer>
                                    <v-radio label="Var" :value=7></v-radio>
                                    <v-radio label="Yok" :value=1></v-radio>
                                </v-radio-group>
                                <v-select v-if="secilenDurum!==7"
                                          :items="ozelDurumlar"
                                          item-value="id"
                                          v-model="secilenDurum"
                                          item-text="tur"
                                          label="Varsa Özel Durum Seçiniz"
                                          required
                                ></v-select>

                            </v-col>
                        </v-row>
                    </v-card-text>
                    <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn color="secondary" @click="Hesapla">Hesapla</v-btn>
                    </v-card-actions>
                </v-card>
            </v-col>
            <v-col cols="12" md="1"></v-col>
            <v-col cols="12" md="5">
                <v-card>
                    <v-card-title class="justify-center">Hesap Dökümü</v-card-title>
                    <v-card-text class="font-weight-bold">Şartli Tahliye: {{sartliTahliye}}</v-card-text>
                    <v-card-text class="font-weight-bold">Denetimli Serbestlik: {{denetimliSerbestlik}}</v-card-text>
                    <v-card-text class="font-weight-bold">Son Güncelleme Tarihi: 20.01.2020</v-card-text>
                </v-card>
            </v-col>
        </v-row>
    </div>
</template>

<script>


    import Datepicker from 'vuejs-datepicker';
    import {en, tr} from 'vuejs-datepicker/dist/locale'

    export default {
        name: "Home",
        components: {
            Datepicker
        },
        data() {
            return {
                sucTurleri: [
                    {id: 2, tur: "Kasten Öldürme (TCK 81, 82)"},
                    {id: 3, tur: "Kasten Öldürme (TCK 83)"},
                    {id: 4, tur: "Kasten Öldürme (TCK 83)"},
                    {id: 5, tur: "Iskence ve Eziyet (TCK 94,95,96)"},
                    {id: 6, tur: "Özel Hayata Karsi Suçlar (TCK 132 - 138 arasi)"},
                    {
                        id: 7,
                        tur: "Terörle mücadele Kanunu kapsamina girmeyen suçlar bakimindan Örgüt kurmak veya yönetmek ya da örgütün faaliyeti çerçevesinde islenen suçlar"
                    },
                    {id: 8, tur: "Devletin Güvenligine Karsi Suçlar (TCK 302 - 339 arasi)"},
                    {id: 9, tur: "MIT Kanunu Kapsamindaki Suçlar (2937 sayili kanun kapsaminda islenen)"},
                    {id: 10, tur: "Basit Cinsel Saldiri (TCK 102/1)"},
                    {id: 11, tur: "Resit Olmayanla Cinsel Iliski Suçu (104/1)"},
                    {id: 12, tur: "Cinsel Taciz (TCK 105)"},
                    {id: 13, tur: "Nitelikli Cinsel Saldiri (TCK 102/2)"},
                    {id: 14, tur: "Çocugun Cinsel Istismari (TCK 103)"},
                    {id: 15, tur: "Resit Olmayanla Cinsel Iliski (TCK 104/2-3)"},
                    {id: 16, tur: "Resit Olmayanla Cinsel Iliski Suçunun Nitelikli Hali (TCK 104/2)"},
                    {id: 17, tur: "Uyusturucu Ticareti (TCK 188)"},
                    {id: 18, tur: "Terör Suçu(3713 sy. yasa kapsamindaki suçlar)"},
                    {id: 1, tur: "Diğer"},
                ],
                cezaTurleri: [
                    {id: 1, tur: "Süreli Hapis Cezasi"},
                    {id: 2, tur: "Agirlastirilmis Müebbet Hapis Cezasi"},
                    {id: 3, tur: "Müebbet Hapis Cezasi"},
                ],
                ozelDurumlar: [
                    {id: 1, tur: "Yok"},
                    {id: 2, tur: "0-6 Yas Araliginda Çocugu Bulunan Kadin Hükümlü"},
                    // {id: 3, tur: "12-15 Yas  Hükümlüler"},
                    // {id: 4, tur: "15-18 Yas  Hükümlüler"},
                    //{id: 5, tur: "70 Yas Üzeri Hükümlüler"},
                    {id: 6, tur: "65 Yas Üzeri ve Agir Hasta Hükümlüler"},
                    //      {id: 7, tur: "Tekerrür"},

                ],
                secilenSuc: 1,
                secilenCeza: 1,
                secilenOzelDurum: 1,
                secilenDurum: 1,
                sucTarihi: new Date("2020-02-03"),
                dogumTarihi: new Date("1995-12-04"),
                mahsupVarMi: false,
                mahsupGun:0,
                denetimliSerbestlik: "",
                sartliTahliye: "",
                hesapYil: 0,
                hesapAy: 0,
                hesapGun: 0,
                sonucGun: 0,
                gun: 0,
                en: en,
                tr: tr,
                yasHataMesaji: false
            };
        },
        watch:{
            'sucTarihi': function() {
                this.TarihKontrolEt();
            },
            'dogumTarihi': function() {
                this.TarihKontrolEt();
            }
        },
        methods: {
            Hesapla() {
                this.secilenOzelDurum = this.secilenDurum;
                this.gun = this.hesapYil * 365 + this.hesapAy * 30 + this.hesapGun;
                this.denetimliSerbestlik = "x";
                this.sartliTahliye = "x";
                this.OzelDurumBelirle();
                console.log("Seçilen Durum:", this.secilenOzelDurum);
                console.log("Tarih:", this.sucTarihi);
                console.log("Seçilen Suç:", this.secilenSuc);
                console.log("Gün:", this.gun);

                switch (this.secilenSuc) {
                    // Adi Suçlar
                    case 1 :
                        if (this.secilenOzelDurum === 1) {
                            if (this.sucTarihi < new Date("2020-03-30")) {

                                this.sonucGun = this.gun / 2 - (3 * 365);
                            } else {
                                this.sonucGun = this.gun / 2 - 365;
                            }
                        } else if (this.secilenOzelDurum === 2) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun / 2 - (4 * 365);
                            } else {
                                this.sonucGun = this.gun / 2 - (2 * 365);
                            }
                        } else if (this.secilenOzelDurum === 3) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun / 2 - 3 * 365) / 3;
                            } else {
                                this.sonucGun = (this.gun / 2 - 365) / 2;
                            }
                        } else if (this.secilenOzelDurum === 4) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun / 2 - 3 * 365) / 2;
                            } else {
                                this.sonucGun = (this.gun / 2 - 365);
                            }
                        } else if (this.secilenOzelDurum === 5) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun / 2 - (4 * 365);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }
                        } else if (this.secilenOzelDurum === 6) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = 0
                            } else {
                                this.sonucGun = this.gun / 2 - (3 * 365);
                            }
                        } else if (this.secilenOzelDurum === 7) {
                            this.sonucGun = this.gun / 2 - (3 * 365);
                        }
                        break;
                    // Kasten Öldürme TCK 81,82 ve ya  Özel Hayata Karsi Suçlar ve ya  Devletin Güvenligine Karsi Suçlar ve ya Cinsel suçlar
                    case 2 :
                    case 6 :
                    case 8 :
                    case 10 :
                    case 11 :
                    case 12 :
                        if (this.secilenOzelDurum === 1 || this.secilenOzelDurum === 5 || this.secilenOzelDurum === 7) { // yok ve ya 70 yas ve ya tekerrür
                            this.sonucGun = this.gun / 3 * 2 - 365;
                        } else if (this.secilenOzelDurum === 2) { // anne
                            this.sonucGun = this.gun / 3 * 2 - (365 * 2);
                        } else if (this.secilenOzelDurum === 3) { // 12-15 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 3;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            }
                        } else if (this.secilenOzelDurum === 4) { // 15-18 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }
                        } else if (this.secilenOzelDurum === 6) { // 65 yas ustu
                            this.sonucGun = this.gun * 2 / 3 - (365 * 3);
                        }
                        break;
                    //Kasten adam öldürme 83
                    case 3:
                        if (this.secilenOzelDurum === 1 || this.secilenOzelDurum === 3 || this.secilenOzelDurum === 4 || this.secilenOzelDurum === 7) { // yok ve ya 12-15 ve ya 15-18
                            if (this.sucTarihi < new Date("2016-07-01")) {
                                this.sonucGun = this.gun / 2 - (365 * 2);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }

                        } else if (this.secilenOzelDurum === 2) { // anne
                            this.sonucGun = this.gun * 2 / 3 - (365 * 2);
                        } else if (this.secilenOzelDurum === 5) { // 70 yas ustu
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun * 2 / 3 - (2 * 365);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }
                        } else if (this.secilenOzelDurum === 6) { // 65 yas ustu
                            this.sonucGun = this.gun * 2 / 3 - (365 * 3);
                        }
                        break;
                    // Iskence ve Eziyet
                    case 5:
                        if (this.secilenOzelDurum === 1 || this.secilenOzelDurum === 7) { // yok ve ya 12-15 ve ya 15-18
                            if (this.sucTarihi < new Date("2016-07-01")) {
                                this.sonucGun = this.gun / 2 - (365 * 2);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }
                        } else if (this.secilenOzelDurum === 2) { // anne
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun * 2 / 3 - (4 * 365);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - (2 * 365);
                            }
                        } else if (this.secilenOzelDurum === 3) { // 12-15 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 3;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            }
                        } else if (this.secilenOzelDurum === 4) { // 15-18 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365);
                            }
                        } else if (this.secilenOzelDurum === 5) { // 70 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - (365 * 4));
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }
                        } else if (this.secilenOzelDurum === 6) { // 65 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = 0;
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - (365 * 2);
                            }
                        }
                        break;
                    // Terör sayilmayan örgüt suçlari ve ya MİT Kanunu Kapsamındaki Suçlar
                    case 7 :
                    case 9 :
                        if (this.secilenOzelDurum === 1) {
                            if (this.sucTarihi < new Date("2020-03-30")) {

                                this.sonucGun = this.gun * 2 / 3 - (3 * 365);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }
                        } else if (this.secilenOzelDurum === 2) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun * 2 / 3 - (4 * 365);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - (2 * 365);
                            }
                        } else if (this.secilenOzelDurum === 3) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 3 * 365) / 3;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            }
                        } else if (this.secilenOzelDurum === 4) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 3 * 365) / 2;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365);
                            }
                        } else if (this.secilenOzelDurum === 5) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun * 2 / 3 - (4 * 365);
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            }
                        } else if (this.secilenOzelDurum === 6) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = 0
                            } else {
                                this.sonucGun = this.gun * 2 / 3 - (3 * 365);
                            }
                        } else if (this.secilenOzelDurum === 7) {
                            this.sonucGun = this.gun * 2 / 3 - 365;
                        }
                        break;
                    case 13 :
                    case 14 :
                    case 15 :
                    case 16 :
                    case 18 :
                        if (  this.secilenOzelDurum === 1 && this.secilenSuc === 18 ){
                            this.sonucGun = this.gun * 3 / 4 - 365;

                        }
                      else if (this.secilenOzelDurum === 1 ) { // yok
                            if (this.sucTarihi < new Date("2014-06-28")) {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            } else {
                                this.sonucGun = this.gun * 3 / 4 - 365;
                            }
                        }
                        else if (this.secilenOzelDurum === 2) { // anne
                            this.sonucGun = this.gun * 3 / 4 - (365 * 2);
                        }
                        else if (this.secilenOzelDurum === 3) { // 12-15 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 3;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            }
                        } else if (this.secilenOzelDurum === 4) { // 15-18 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365);
                            }
                        } else if (this.secilenOzelDurum === 5 || this.secilenOzelDurum === 7) { // 70 yas
                            this.sonucGun = this.gun * 3 / 4 - 365;
                        } else if (this.secilenOzelDurum === 6) { // 65 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun * 3 / 4 - 365 * 3;
                            } else {
                                this.sonucGun = this.gun * 3 / 4 - 365 * 2;
                            }
                        }
                        break;
                    case 17:
                         if (this.secilenOzelDurum === 1 ) { // yok
                            if (this.sucTarihi < new Date("2014-06-28")) {
                                this.sonucGun = this.gun * 2 / 3 - 365;
                            } else {
                                this.sonucGun = this.gun * 3 / 4 - 365;
                            }
                        }
                        else if (this.secilenOzelDurum === 2) { // anne
                             if (this.sucTarihi < new Date("2020-03-30")) {
                                 this.sonucGun = this.gun * 3 / 4 - (365*4) ;
                             } else {
                                 this.sonucGun = this.gun * 3 / 4 - (365*2);
                             }
                        }
                        else if (this.secilenOzelDurum === 3) { // 12-15 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 3;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            }
                        } else if (this.secilenOzelDurum === 4) { // 15-18 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 - 365) / 2;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 - 365);
                            }
                        } else if (this.secilenOzelDurum === 5 ) { // 70 yas
                             if (this.sucTarihi < new Date("2020-03-30")) {
                                 this.sonucGun = this.gun * 3 / 4 - (365*4) ;
                             } else {
                                 this.sonucGun = this.gun * 3 / 4 - (365*1);
                             }
                        } else if (this.secilenOzelDurum === 6) { // 65 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = 0;
                            } else {
                                this.sonucGun = this.gun * 3 / 4 - 365 * 3;
                            }
                        }
                        else if(this.secilenOzelDurum === 7){ //tekerrür
                            this.sonucGun = this.gun * 3 / 4 - 365;
                         }
                        break;
                    default:
                        break;
                }
                console.log("hesaplanacak gun:", this.sonucGun);
                this.Yazdir();
            },
            Yazdir() {
                if(this.secilenCeza === 2)
                {
                    this.denetimliSerbestlik = "Ağırlaştırılmış müebbet hapis cezasına mahkûm edilmiş olanlar 30 yılını yüksek güvenlikli kapalı ceza infaz kurumunda çektikleri takdirde, koşullu salıverilmeden yararlanabilirler.";
                    this.sartliTahliye = "-";
                }
                else if (this.secilenCeza ===3)
                {
                    this.denetimliSerbestlik = "Müebbet hapis cezasına mahkûm edilmiş olanlar 24 yılını yüksek güvenlikli kapalı ceza infaz kurumunda çektikleri takdirde, koşullu salıverilmeden yararlanabilirler.";
                    this.sartliTahliye = "-";
                }
                else{
                if (this.mahsupVarMi===true)
                {
                    this.sonucGun -= this.mahsupGun;
                }

                    if (this.sonucGun <= 0)
                    {
                        this.denetimliSerbestlik = "Denetimli serbestlikten yararlanır, infaz süresi yoktur.";
                        this.sartliTahliye = "-";
                    }
                    else{
                        this.denetimliSerbestlik = this.humanise(this.sonucGun);
                        this.sartliTahliye = this.humanise(this.gun/2);
                    }

                }
            },
            humanise(diff) {
                // The string we're working with to create the representation
                var str = '';
                // Map lengths of `diff` to different time periods
                var values = [[' yıl', 365], [' ay', 30], [' gün', 1]];

                // Iterate over the values...
                for (var i = 0; i < values.length; i++) {
                    var amount = Math.floor(diff / values[i][1]);

                    // ... and find the largest time value that fits into the diff
                    if (amount >= 1) {
                        // If we match, add to the string ('s' is for pluralization)
                        str += amount + values[i][0] + ' ';

                        // and subtract from the diff
                        diff -= amount * values[i][1];
                    }
                }

                return str;
            },
            OzelDurumBelirle() {
                var yas = this.YasGetir();
                console.log("Yas:", yas);
                if (yas >= 12 && yas <= 18) {
                    this.secilenOzelDurum = 4;
                    if (yas <= 15) {
                        this.secilenOzelDurum = 3;
                    }
                } else if (![6, 7].includes(this.secilenOzelDurum)) {
                    if (yas >= 70) {
                        this.secilenOzelDurum = 5
                    }
                }

            },
            YasGetir() {
                var diff_ms = this.sucTarihi - this.dogumTarihi;
                var age_dt = new Date(diff_ms);

                return Math.abs(age_dt.getUTCFullYear() - 1970);
            },
            TarihKontrolEt(){
                if (this.sucTarihi<this.dogumTarihi){
                    this.yasHataMesaji = true;
                }
                else {
                    this.yasHataMesaji=false;
                }
            },


        }
    };
</script>
