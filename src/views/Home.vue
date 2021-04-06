<template>
  <div class="home">
    <Header />

    <b-row class="inputFields mx-3 mb-5">
      <b-col>
        <b-form-input
          v-model="capitalText"
          placeholder="Search by Capital"
        ></b-form-input>
      </b-col>
      <b-col>
        <b-form-input
          v-model="anySeachText"
          placeholder="Search by Anything "
        ></b-form-input>
      </b-col>
    </b-row>

    <Table :items="desiredArr" />

    <h5 v-for="matchResult in matchResults" :key="matchResult.idForKey">
      {{ JSON.stringify(matchResult, null, "\t") }}
    </h5>
  </div>
</template>

<script>
// @ is an alias to /src
import Table from "@/components/Table.vue";
import Header from "@/components/Header.vue";
import axios from "axios";
export default {
  name: "Home",
  data: () => {
    return {
      items: [],
      capitalText: "",
      anySeachText: "",
      isEnterClicked: false,
      matchResults: [],
      idForKey: 0,
    };
  },
  components: {
    Table,
    Header,
  },
  methods: {
    //api den json verisini getirir
    async fetchDataFromApi() {
      const response = await axios.get("https://restcountries.eu/rest/v2/all");
      this.items = response.data;
    },
    //json daki capital attribute üne göre filtreleme yapar
    filteredArrByCapital() {
      if (this.capitalText) {
        return this.items.filter((item) => {
          return item.capital
            .toLowerCase()
            .includes(this.capitalText.toLowerCase());
        });
      } else return this.items;
    },
    // tüm jsonda yapılan arama için filtreleme yapar
    // checkIfAnthingMatched() method unu kullanır
    fiteredArrByAnything() {
      this.matchResults = [];
      this.idForKey = 0;
      return this.items.filter((item) => {
        return this.checkIfAnthingMatched(item);
      });
    },
    //Filtrelenmiş data yı tablo da kullanılacak formata çevirir
    // İstenen 4 özellik için yazıldı (name capital region flag)
    createArrayof4(filteredArr) {
      let itemsContainNameCapitalRegionFlag = [];
      filteredArr.forEach((item) => {
        const oneElementOfNewArr = {
          name: item.name,
          capital: item.capital,
          region: item.region,
          flag: item.flag,
        };
        itemsContainNameCapitalRegionFlag.push(oneElementOfNewArr);
      });
      return itemsContainNameCapitalRegionFlag;
    },
    // checkIfAnthingMatched() boolean döndürür, tüm json içinde arama yapar
    // eğer bir itemin herhangi bir attribute ünde matchleşme varsa true dönecek
    // filteleme yapmak için kullanılacak
    checkIfAnthingMatched(item) {
      if (this.anySeachText) {
        //bu for en kötü seneryonda 6000 bin defa dönüyor
        for (const attribute in item) {
          // aşağıdaki console.log() her attribute için karşılaştırmayı gösterir
          /*
        console.log(
          `${JSON.stringify(
            item[attribute]
          ).toLowerCase()}  ?  ${this.anySeachText.toLowerCase()}`
        );
        console.log(
          JSON.stringify(item[attribute])
            .toLowerCase()
            .includes(this.anySeachText.toLowerCase())
        );
        */
          if (
            JSON.stringify(item[attribute])
              .toLowerCase()
              .includes(this.anySeachText.toLowerCase())
          ) {
            this.matchResults.push({
              [attribute]: item[attribute],
              idForKey: this.idForKey++,
            });
            return true;
          }

          //Aşağıdaki iki forloop nested objelerin içerisinde arama yapmayı sağlıyor
          // Şu anlık gerek yok  daha hassas aramalar için kullanılabilir
          /*
        const firstNestedAttributes = item[attribute];
        for (const firstNestedAttribute in firstNestedAttributes) {

          if (
            JSON.stringify(firstNestedAttributes[firstNestedAttribute])
              .toLowerCase()
              .includes(this.anySeachText.toLowerCase())
          ) {
            console.log("2. loop");
            return true;
          }

          const secondNestedAttributes =
            firstNestedAttributes[firstNestedAttribute];
          for (const secondNestedAttribute in secondNestedAttributes) {
            // bu console.log aramanın ne zaman sonlandığını bulmak
            //ve nasıl bir arama yapıldığını görmek için faydalı
            console.log(
              ` JsonData --> ${attribute}: ${item[attribute]} --> ${firstNestedAttribute}: ${firstNestedAttributes[firstNestedAttribute]} -->${secondNestedAttribute}: ${secondNestedAttributes[secondNestedAttribute]} `
            );

            if (
              JSON.stringify(secondNestedAttributes[secondNestedAttribute])
                .toLowerCase()
                .includes(this.anySeachText.toLowerCase())
            ) {
              console.log("3. loop");
              return true;
            }
          }
        }
         */
        }
      } else {
        return true;
      }
      return false;
    },
    showFilteredData() {
      console.log(this.matchResults);
      return JSON.stringify(this.matchResults, null, "\t");
    },
  },
  computed: {
    // alt komponent olan tablo ya gönderilir
    desiredArr: function() {
      return !this.capitalText
        ? this.createArrayof4(this.fiteredArrByAnything())
        : this.createArrayof4(this.filteredArrByCapital());
    },
  },
  created() {
    this.fetchDataFromApi();
  },
};
</script>

<style>
h5 {
  font-size: 3px;
  font-weight: bold;
}
</style>
