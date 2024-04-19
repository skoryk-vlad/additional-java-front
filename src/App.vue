<script setup>
import axios from "axios";
import { ref } from "vue";

const packages = ref(null);
const packageName = ref("");
const titles = [
  "Id",
  "Name",
  "Weekly Downloads",
  "Version",
  "Size",
  "Last Publish",
];

const addPackage = async () => {
  try {
    await axios.post("http://localhost:8081/packages", {
      name: packageName.value,
    });
    packageName.value = "";
    await fetchData();
  } catch (e) {
    console.log(e);
  }
};

const exportExcel = async () => {
  try {
    const { data } = await axios.get("http://localhost:8081/packages/excel", {
      responseType: "blob",
    });

    const href = window.URL.createObjectURL(data);
    const link = document.createElement('a');
    link.href = href;
    link.setAttribute('download', 'packages.xlsx');
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    window.URL.revokeObjectURL(href);
  } catch (e) {
    console.log(e);
  }
};

const fetchData = async () => {
  try {
    packages.value = (await axios.get("http://localhost:8081/packages")).data;
  } catch (e) {
    console.log(e);
  }
};
fetchData();
</script>

<template>
  <header>
    <div>NPM packages</div>
  </header>

  <main>
    <div class="form">
      <input type="text" placeholder="Package name..." v-model="packageName" />
      <button @click="addPackage">Add package</button>
      <button @click="exportExcel">Export Excel</button>
    </div>
    <table>
      <tr class="header-row">
        <th class="item" v-for="title of titles" :key="title">{{ title }}</th>
      </tr>
      <tr v-for="npmPackage of packages" :key="npmPackage">
        <td>{{ npmPackage.id }}</td>
        <td>{{ npmPackage.name }}</td>
        <td>{{ npmPackage.weeklyDownloads }}</td>
        <td>{{ npmPackage.version }}</td>
        <td>{{ npmPackage.size }}</td>
        <td>{{ npmPackage.lastPublish }}</td>
      </tr>
    </table>
  </main>
</template>

<style scoped>
header {
  width: 100%;
  height: 80px;
  background-color: lightblue;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 35px;
}
main {
  width: 80%;
  margin: 15px auto;
  display: flex;
  align-items: center;
  flex-direction: column;
}
.form {
  align-self: flex-end;
  margin-bottom: 15px;
}
table {
  width: 100%;
  border: 1px solid lightblue;
  border-radius: 15px;
  padding: 5px;
}
.header-row {
  border-bottom: 1px solid lightblue;
}
th {
  border-bottom: 1px solid lightblue;
  font-weight: bold;
}
</style>
