<template>
  <header class="text-4xl font-bold pl-4 py-4">Deck Diff</header>

  <main class="grid grid-cols-1 xl:grid-cols-2">
    <div class="flex flex-col">
      <h2 class="text-center text-xl mb-2">The list you have:</h2>
      <textarea
      name="list1"
      id="list1"
      v-model="list1"
      rows="25"
      class="border-2 border-gray-300 p-4 w-3/4 mx-auto"
      ></textarea>
    </div>
    <div class="flex flex-col">
      <h2 class="text-center text-xl mb-2">The list you want:</h2>
      <textarea
      name="list2"
      id="list2"
      v-model="list2"
      rows="25"
      class="border-2 border-gray-300 p-4 w-3/4 mx-auto"
      ></textarea>
    </div>
    <button @click="compare()" class="px-4 py-2 bg-blue-300 border-2 border-blue-800  mx-auto rounded my-8 xl:col-span-2">Compare</button>
    <div class="px-8">
      <h2 class="text-2xl font-bold underline">Remove:</h2>
      <p class="text-red-800 font-bold text-lg pl-4">
        <ul class="list-disc list-inside">
          <li v-for="(card, index) in Object.keys(removeList)" :key="index">{{removeList[card]}} {{ card }}</li>
        </ul>
      </p>
    </div>
    <div class="px-8">
      <h2 class="text-2xl font-bold underline">Add:</h2>
      <p class="text-green-800 font-bold text-lg pl-4">
        <ul class="list-disc list-inside">
          <li v-for="(card, index) in Object.keys(addList)" :key="index">{{addList[card]}} {{ card }}</li>
        </ul>
      </p>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
const list1 = ref("");
const list2 = ref("");
const removeList = ref({});
const addList = ref({});

function compare() {
  let splitList = list1.value.split("\n");
  splitList = splitList.filter((el) => {
    return Number.isInteger(Number.parseInt(el[0]));
  });

  splitList = splitList.map((el) => {
    let res = el.split(" ");
    if (res.at(-1) === "PH") {
      res.pop();
    }
    res.pop();
    res.pop();
    return [res[0], res.slice(1).join(" ")];
  });

  let splitList2 = list2.value.split("\n");
  splitList2 = splitList2.filter((el) => {
    return Number.isInteger(Number.parseInt(el[0]));
  });

  splitList2 = splitList2.map((el) => {
    let res = el.split(" ");
    if (res.at(-1) === "PH") {
      res.pop();
    }
    res.pop();
    res.pop();
    return [res[0], res.slice(1).join(" ")];
  });

  let obj1 = {};
  let obj2 = {};

  splitList.forEach((el) => {
    obj1[el[1]] = Number.parseInt(el[0]);
  });

  splitList2.forEach((el) => {
    obj2[el[1]] = Number.parseInt(el[0]);
  });

  let keys = [...Object.keys(obj1), ...Object.keys(obj2)];
  keys = [...new Set(keys)];

  keys.forEach((key) => {
    let a = obj1?.[key] ? obj1[key] : 0;
    let b = obj2?.[key] ? obj2[key] : 0;
    let diff = a - b;

    if (diff > 0) {
      // more in list 1
      removeList.value[key] = diff;
    } else if (diff < 0) {
      addList.value[key] = Math.abs(diff);
    }
    // do nothing if same count in both lists
  });

  console.log(keys.sort());
}
</script>
