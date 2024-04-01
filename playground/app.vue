<template>
  <div class="flex justify-start gap-20 m-10">
    <div
      class="bg-emerald-300 rounded-lg overflow-hidden shadow-lg"
    >
      <div class="p-8">
        <h2 class="text-xl text-red-500 font-bold mb-4">ENTER DETAILS:</h2>
        <form @submit.prevent="submitForm">
          <div class="mb-4">
            <label for="id" class="block font-bold mb-1">Id:</label>
            <input
              type="number"
              v-model="formData.id"
              id="id"
              name="id"
              class="w-full px-3 py-2 border border-cyan-600 rounded-md"
              placeholder="Enter Id"
              required
            />
          </div>
          <div class="mb-4">
            <label for="name" class="block font-bold mb-1">Name:</label>
            <input
              id="name"
              v-model="formData.name"
              type="text"
              name="name"
              class="w-full px-3 py-2 border border-cyan-600 rounded-md"
              placeholder="Enter Name"
              required
            />
          </div>
          <div class="mb-4">
            <label for="age" class="block font-bold mb-1">Age:</label>
            <input
              id="age"
              v-model.number="formData.age"
              type="number"
              name="age"
              class="w-full px-3 py-2 border border-cyan-600 rounded-md"
              placeholder="Enter Age"
              required
            />
          </div>
          <div class="mb-4">
            <label for="phonenumber" class="block font-bold mb-1"
              >Phone Number:</label
            >
            <input
              id="phonenumber"
              v-model="formData.phone"
              type="text"
              name="phonenumber"
              class="w-full px-3 py-2 border border-cyan-600 rounded-md"
              placeholder="Enter Phonenumber"
              required
            />
          </div>
          <button
            type="submit"
            :disabled="disableButton"
            class="text-white px-4 py-2 rounded-md w-full"
            :class="disableButton ? 'bg-blue-400' : 'bg-blue-600'"
          >
            Submit
          </button>
        </form>
      </div>
    </div>
    <div class="overflow-x-auto flex justify-center mt-10">
      <table
        v-if="employeeDetails.length"
        class="table-auto border-collapse border border-gray-200"
      >
        <thead>
          <tr>
            <th
              v-for="head in tableHeaders"
              :key="head"
              class="px-3 py-1 bg-cyan-700 text-white"
            >
              {{ head }}
            </th>
            <th class="px-3 py-1 bg-cyan-700 text-white">CRUD</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(person, index) in employeeDetails"
            :key="index"
            class="bg-gray-50"
          >
            <td class="border px-4 py-2">
              {{ person.id }}
            </td>
            <td class="border px-4 py-2">
              {{ person.name }}
            </td>
            <td class="border px-4 py-2">
              {{ person.age }}
            </td>
            <td class="border px-4 py-2">
              {{ person.phone }}
            </td>
            <td class="border px-4 py-2">
              <button
                v-for="button in buttons"
                :key="button"
                class="border rounded-lg px-2 py-1 text-white mx-3"
                :class="
                  button === 'Read'
                    ? 'bg-yellow-500'
                    : button === 'Update'
                    ? 'bg-orange-500'
                    : 'bg-red-500'
                "
                @click="crudOperations(button, person)"
              >
                {{ button }}
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
const tableHeaders = ref([]);
const formData = ref({
  id: "",
  name: "",
  age: null,
  phone: "",
});
const disableButton = ref(false)
const employeeDetails = ref([]);
const buttons = ref(["Read", "Update", "Delete"]);
onMounted(async () => {
  readFormData();
});
const readFormData = async () => {
  const { data } = await useFetch("https://sheetdb.io/api/v1/7e1qtstut00nn?sort_by=id&sort_order=asc");
  if (data) {
    employeeDetails.value = data.value;
    // Extract keys from the first object in the array
    tableHeaders.value = Object.keys(employeeDetails.value[0]);
  }
};
const submitForm = async () => {
  const response = await $fetch("https://sheetdb.io/api/v1/7e1qtstut00nn", {
    method: "POST",
    body: {
      id: formData.value.id,
      name: formData.value.name,
      age: formData.value.age,
      phone: formData.value.phone,
    },
  });
  if (response) {
    readFormData();
    formData.value = {};
  }
};
const crudOperations = async (button: any, person: any) => {
  if (button === "Read") {
    const data = await useFetch(
      `https://sheetdb.io/api/v1/7e1qtstut00nn/search?name=${person.name}`
    );
    if (data) {
      let readingDetails = [];
      readingDetails = data.data.value;
      console.log(":::readingDetails", readingDetails);
      formData.value = readingDetails[0];
      disableButton.value = true
    }
  } else if (button === "Update") {
    const response = await $fetch(
      `https://sheetdb.io/api/v1/7e1qtstut00nn/name/${person.name}`,
      {
        method: "PATCH",
        headers: {
          Accept: "application/json",
        },
        body: {
          id: formData.value.id,
          name: formData.value.name,
          age: formData.value.age,
          phone: formData.value.phone,
        },
      }
    );
    if (response) {
      readFormData();
      formData.value = {};
      disableButton.value = false
    }
  } else {
    const response = await $fetch(
      `https://sheetdb.io/api/v1/7e1qtstut00nn/name/${person.name}`,
      {
        method: "DELETE",
        headers: {
          Accept: "application/json",
        },
      }
    );
    if (response) readFormData();
  }
};
</script>
