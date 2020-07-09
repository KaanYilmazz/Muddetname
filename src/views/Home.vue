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
                                <DatePicker v-model="sucTarihi" label="Suç Tarihi"></DatePicker>
                                <DatePicker v-model="dogumTarihi" label="Doğum Tarihi"></DatePicker>


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
                    <v-card-text class="font-weight-bold">Son Güncelleme Tarihi:20.01.2020</v-card-text>
                </v-card>
            </v-col>
        </v-row>
    </div>
</template>

<script>


  import DatePicker from "../components/DatePicker";

  export default {
        name: "Home",
        components: {
            DatePicker
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
                    {id: 3, tur: "12-15 Yaş  Hükümlüler"},
                    {id: 4, tur: "15-18 Yaş  Hükümlüler"},
                    {id: 5, tur: "70 Yaş Üzeri Hükümlüler"},
                    {id: 6, tur: "65 Yaş Üzeri ve Ağır Hasta Hükümlüler"},

                ],
                oranlar: [
                    [
                        [
                            "*2#/3#-1#/3"
                        ],
                        []
                    ],
                    []
                ],
                secilenSuc: 1,
                secilenCeza: 1,
                secilenOzelDurum: 1,
                sucTarihi: new Date("2015-02-03"),
                dogumTarihi: new Date().toISOString().substr(0, 10),
                mahsupVarMi: false,
                denetimliSerbestlik: "",
                sartliTahliye: "",
                sonuc: "",
                hesapYil: 0,
                hesapAy: 0,
                hesapGun: 0,
                sonucGun:0,
                gun:0,

            };
        },
        methods: {
            Hesapla() {
                this.gun = this.hesapYil *365 + this.hesapAy *30 + this.hesapGun;
                this.denetimliSerbestlik = "15 yıl 2 ay 3 gün";
                this.sartliTahliye = "10 yıl 7 ay 17 gün";
                this.sonuc = "17 yıl 1 ay 29 gün";
                //  const diffTime = Math.abs(new Date() - this.sucTarihi);
                //  const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
              console.log("secilen durum:",this.secilenOzelDurum);
              console.log("tarih:",this.sucTarihi);
              console.log("secilen suc:",this.secilenSuc);
              switch (this.secilenSuc) {
                case 1:
                  if (this.secilenOzelDurum===3)
                  {
                    if (this.sucTarihi< new Date("2020-03-30"))
                    {
                    this.sonucGun = (this.gun/2-3)/3;
                    }
                    else
                    {
                      this.sonucGun = (this.gun/2-1)/2;
                    }
                  }
                  else if(this.secilenOzelDurum ===4)
                  {
                    if (this.sucTarihi< new Date("2020-03-30"))
                    {
                      this.sonucGun = (this.gun/2-3)/2;
                    }
                    else
                    {
                      this.sonucGun = (this.gun/2-1);
                    }
                  }
                  else if(this.secilenOzelDurum ===7){
                      this.sonucGun = this.gun/2-3;
                  }
                  else if(this.secilenOzelDurum ===1)
                  {
                    if (this.sucTarihi< new Date("2020-03-30"))
                    {
                      this.sonucGun = this.gun/2-3;
                    }
                    else
                    {
                      console.log("sonuc gun:",this.sonucGun);
                      this.sonucGun = this.gun/2-1;
                    }
                  }
                  this.Yazdir();
              }



            },
          Yazdir(){
             this.sonuc = this.humanise(this.sonucGun);
          },
          humanise (diff) {
    // The string we're working with to create the representation
    var str = '';
    // Map lengths of `diff` to different time periods
    var values = [[' year', 365], [' month', 30], [' day', 1]];

    // Iterate over the values...
    for (var i=0;i<values.length;i++) {
      var amount = Math.floor(diff / values[i][1]);

      // ... and find the largest time value that fits into the diff
      if (amount >= 1) {
        // If we match, add to the string ('s' is for pluralization)
        str += amount + values[i][0] + (amount > 1 ? 's' : '') + ' ';

        // and subtract from the diff
        diff -= amount * values[i][1];
      }
    }

    return str;
  }


        }
    };
</script>
