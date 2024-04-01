<template>
  <div>
    <div
      class="max-w-md mx-auto mt-10 bg-emerald-300 rounded-lg overflow-hidden shadow-lg"
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
              v-model="formData.phoneNumber"
              type="text"
              name="phonenumber"
              class="w-full px-3 py-2 border border-cyan-600 rounded-md"
              placeholder="Enter Phonenumber"
              required
            />
          </div>
          <button
            type="submit"
            class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600 w-full"
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
  phoneNumber: "",
});
const employeeDetails = ref([]);
const buttons = ref(["Read", "Update", "Delete"]);
onMounted(async () => {
  const { data } = await useFetch("https://sheetdb.io/api/v1/7e1qtstut00nn");
  if (data) {
    employeeDetails.value = data.value;
    // Extract keys from the first object in the array
    tableHeaders.value = Object.keys(employeeDetails.value[0]);
  }
});
const submitForm = async () => {
  const response = await $fetch("https://sheetdb.io/api/v1/7e1qtstut00nn", {
    method: "POST",
    body: {
      id: formData.value.id,
      name: formData.value.name,
      age: formData.value.age,
      phone: formData.value.phoneNumber,
    },
  });
  if (response) {
    formData.value = {};
  }
};
const crudOperations = async (button: any, person: any) => {
  if (button === "Read") {
    const data = await useLazyFetch("https://sheetdb.io/api/v1/7e1qtstut00nn", {
      query: {
        id: person.id,
      },
    });
    console.log(":::person", person.id);
    if (data) {
      const readingDetails = data;
      console.log(":::readingDetails", readingDetails);
    }
  }
};
</script>
