<template>
  <div class="Cont">
    <table class="table-striped  ">
      <thead>
        <tr>
          <th
            v-for="headerItem in arrayForHeader"
            :key="headerItem.fieldName"
            scope="col"
            class="field"
          >
            {{
              headerItem.fieldName.charAt(0).toUpperCase() +
                headerItem.fieldName.slice(1)
            }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in items" :key="item.name">
          <td>{{ item.name }}</td>
          <td>{{ item.capital }}</td>
          <td>{{ item.region }}</td>
          <td class="aa">
            <img :src="item.flag" />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: {
    items: Array,
  },
  data() {
    return {};
  },
  computed: {
    // tablo başlıkların oluşturmak için gereken arrayi döndürür
    arrayForHeader: function() {
      let headerArr = [];
      if (this.items[0]) {
        Object.entries(this.items[0]).forEach((item) => {
          headerArr.push({ fieldName: item[0] });
        });
      }
      return headerArr;
    },
  },
  mounted() {},
};
</script>

<style>
td {
  padding-right: 10px;
}
img {
  height: 100px;
  width: 100px;
}
.Cont {
  width: 70%;
  margin: 0px auto;
  display: flex;
  flex-direction: column;
  border: 1px solid lightgray;
  border-radius: 12px;
}
.field {
  font-weight: bold;
  font-size: 18px;
}
</style>
