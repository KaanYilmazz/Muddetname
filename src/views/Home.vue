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
                                        <v-text-field v-model="hesapYil" label="Yıl" type="number"/>
                                    </v-col>
                                    <v-col md="4">
                                        <v-text-field v-model="hesapAy" label="Ay" type="number"/>
                                    </v-col>
                                    <v-col md="4">
                                        <v-text-field v-model="hesapGun" label="Gün" type="number"/>
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
                                <v-col md="6">Suç Tarihi :<datepicker v-model="sucTarihi" placeholder="Suç Tarihi" :language="tr"></datepicker></v-col>
                                <v-col md="6">Doğum Tarihi: <datepicker  v-model="dogumTarihi" placeholder="Doğum Tarihi" :language="tr"></datepicker></v-col>
                                </v-row>

                            </v-col>
                            <v-col md="6">
                                <v-radio-group label="Mahsup" row v-model="mahsupVarMi">
                                    <v-spacer></v-spacer>
                                    <v-radio label="Var" :value="true"></v-radio>
                                    <v-radio label="Yok" :value="false"></v-radio>
                                </v-radio-group>
                                <v-row>
                                    <v-col md="6">
                                        <v-text-field v-if="mahsupVarMi===true" label="Mahsup Günü" type="number"/>
                                    </v-col>
                                    <v-col md="6">
                                        <v-spacer></v-spacer>
                                        <v-btn v-if="mahsupVarMi===true">Gün Hesapla</v-btn>
                                    </v-col>
                                </v-row>
                                <v-select
                                        :items="cezaTurleri"
                                        v-model="secilenCeza"
                                        item-value="id"
                                        item-text="tur"
                                        label="Ceza Türü Seçiniz"
                                        required
                                ></v-select>
                                <v-radio-group label="Tekerrüre Esas Sabıka Kaydı:" row v-model="secilenOzelDurum">
                                    <v-spacer></v-spacer>
                                    <v-radio label="Var" :value=7></v-radio>
                                    <v-radio label="Yok" :value=1></v-radio>
                                </v-radio-group>
                                <v-select v-if="secilenOzelDurum!==7"
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
                    <v-card-text class="font-weight-bold">Şartlı Tahliye: {{sartliTahliye}}</v-card-text>
                    <v-card-text class="font-weight-bold">Denetimli Serbestlik: {{denetimliSerbestlik}}</v-card-text>
                    <v-card-text class="font-weight-bold">Sonuç: {{sonuc}}</v-card-text>
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
                    {id: 1, tur: "Adi Suçlar"},
                    {id: 2, tur: "Kasten Öldürme (TCK 81, 82)"},
                    {id: 3, tur: "Kasten Öldürme (TCK 83)"},
                ],
                cezaTurleri: [
                    {id: 1, tur: "Süreli Hapis Cezası"},
                    {id: 2, tur: "Ağırlaştırılmış Müebbet Hapis Cezası"},
                    {id: 3, tur: "Müebbet Hapis Cezası"},
                ],
                ozelDurumlar: [
                    {id: 1, tur: "Yok"},
                    {id: 2, tur: "0-6 Yaş Aralığında Çocuğu Bulunan Kadın Hükümlü"},
                    // {id: 3, tur: "12-15 Yaş  Hükümlüler"},
                    // {id: 4, tur: "15-18 Yaş  Hükümlüler"},
                    //{id: 5, tur: "70 Yaş Üzeri Hükümlüler"},
                    {id: 6, tur: "65 Yaş Üzeri ve Ağır Hasta Hükümlüler"},
                    //      {id: 7, tur: "Tekerrür"},
// işlem sırası 3 = 4 > 7 > 1 > 2 > 5 > 6
                ],
                secilenSuc: 1,
                secilenCeza: 1,
                secilenOzelDurum: 1,
                secilenDurum: 1,
                sucTarihi: new Date("2020-02-03"),
                dogumTarihi: new Date("1995-12-04"),
                mahsupVarMi: false,
                denetimliSerbestlik: "",
                sartliTahliye: "",
                sonuc: "",
                hesapYil: 0,
                hesapAy: 0,
                hesapGun: 0,
                sonucGun: 0,
                gun: 0,
                en: en,
                tr: tr
            };
        },
        methods: {
            Hesapla() {
                this.secilenOzelDurum = this.secilenDurum;
                this.gun = this.hesapYil * 365 + this.hesapAy * 30 + this.hesapGun;
                this.denetimliSerbestlik = "x";
                this.sartliTahliye = "x";
                this.sonuc = "x";
                this.OzelDurumBelirle();
                console.log("Seçilen Durum:", this.secilenOzelDurum);
                console.log("Tarih:", this.sucTarihi);
                console.log("Seçilen Suç:", this.secilenSuc);
                console.log("Gün:", this.gun);

                switch (this.secilenSuc) {
                    // Adi Suçlar
                    case 1:
                        if (this.secilenOzelDurum === 1) {
                            if (this.sucTarihi < new Date("2020-03-30")) {

                                this.sonucGun = this.gun / 2 - (3 * 365);
                            } else {
                                this.sonucGun = this.gun / 2 - 365;
                            }
                        }
                        else if (this.secilenOzelDurum === 2) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun / 2 - (4 * 365);
                            } else {
                                this.sonucGun = this.gun / 2 - (2 * 365);
                            }
                        }
                       else if (this.secilenOzelDurum === 3) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun / 2 - 3 * 365) / 3;
                            } else {
                                this.sonucGun = (this.gun / 2 - 365) / 2;
                            }
                        }
                       else if (this.secilenOzelDurum === 4) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun / 2 - 3 * 365) / 2;
                            } else {
                                this.sonucGun = (this.gun / 2 - 365);
                            }
                        }
                        else if (this.secilenOzelDurum === 5) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = this.gun / 2 - (4 * 365);
                            } else {
                                this.sonucGun = this.gun / 2 - (2 * 365);
                            }
                        }
                        else if (this.secilenOzelDurum === 6) {
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = 0
                            } else {
                                this.sonucGun = this.gun / 2 - (3 * 365);
                            }
                        }
                        else if (this.secilenOzelDurum === 7) {
                            this.sonucGun = this.gun / 2 - (3 * 365);
                        }
                        console.log("hesaplanacak gun:", this.sonucGun);
                        this.Yazdir();
                        break;
                    // Kasten Öldürme TCK 81,82
                    case 2:
                        if (this.secilenOzelDurum === 1 || this.secilenOzelDurum === 5 || this.secilenOzelDurum === 7) { // yok ve ya 70 yas ve ya tekerrür
                                this.sonucGun = this.gun /3 * 2 - 365;
                        }
                        else if (this.secilenOzelDurum === 2) { // anne
                            this.sonucGun = this.gun /3 * 2 - (365*2);
                        }
                        else if (this.secilenOzelDurum === 3) { // 12-15 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 -  365) / 3;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 -  365) / 2 ;
                            }
                        }
                        else if (this.secilenOzelDurum === 4) { // 15-18 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 -  365) / 2 ;
                            } else {
                                this.sonucGun = this.gun * 2 / 3 -  365 ;
                            }
                        }
                        else if (this.secilenOzelDurum === 6) { // 65 yas ustu
                            this.sonucGun = this.gun * 2 /3  - (365*3);
                        }

                        console.log("hesaplanacak gun:", this.sonucGun);
                        this.Yazdir();
                        break;
                    case 3:
                        if (this.secilenOzelDurum === 1 || this.secilenOzelDurum === 5 || this.secilenOzelDurum === 7) { // yok ve ya 70 yas ve ya tekerrür
                            this.sonucGun = this.gun /3 * 2 - 365;
                        }
                        else if (this.secilenOzelDurum === 2) { // anne
                            this.sonucGun = this.gun /3 * 2 - (365*2);
                        }
                        else if (this.secilenOzelDurum === 3) { // 12-15 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 -  365) / 3;
                            } else {
                                this.sonucGun = (this.gun * 2 / 3 -  365) / 2 ;
                            }
                        }
                        else if (this.secilenOzelDurum === 4) { // 15-18 yas
                            if (this.sucTarihi < new Date("2020-03-30")) {
                                this.sonucGun = (this.gun * 2 / 3 -  365) / 2 ;
                            } else {
                                this.sonucGun = this.gun * 2 / 3 -  365 ;
                            }
                        }
                        else if (this.secilenOzelDurum === 6) { // 65 yas ustu
                            this.sonucGun = this.gun * 2 /3  - (365*3);
                        }

                        console.log("hesaplanacak gun:", this.sonucGun);
                        this.Yazdir();
                        break;
                }


            },
            Yazdir() {
                this.sonuc = this.humanise(this.sonucGun);
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
            OzelDurumBelirle()
            {
                var yas =  this.YasGetir();
                console.log("Yaş:",yas);
                if(yas >= 12 && yas <= 18)
                {
                    this.secilenOzelDurum = 4;
                    if(yas <= 15)
                    {
                        this.secilenOzelDurum = 3;
                    }
                }
                else if (![6,7].includes(this.secilenOzelDurum))
                {
                    if(yas >= 70)
                    {
                        this.secilenOzelDurum = 5
                    }
                }

            },
            YasGetir()
            {
                var diff_ms = this.sucTarihi - this.dogumTarihi;
                var age_dt = new Date(diff_ms);

                return Math.abs(age_dt.getUTCFullYear() - 1970);
            }


        }
    };
</script>
