<script setup lang="ts">
import { Ref, ref } from "vue";
import draggable from "vuedraggable";
import type ITableItem from "../interfaces/ITableItem";
import Modal from "./Modal.vue";
import iziToast from "izitoast";

const lists: Ref<ITableItem[]> = ref([]);

const firstNameConventions = ["Utpal", "Bhakto", "Sufal", "Bappy", "Toton"];
const lastNameConventions = ["Sarkar", "Gharami", "Mondol", "Saha"];

const getRandomElement = <T>(arr: T[]): T =>
  arr[Math.floor(Math.random() * arr.length)];

const randomIntFromInterval = (min: number, max: number): number =>
  Math.floor(Math.random() * (max - min + 1) + min);

const getUniqueNumber = (except: Set<number>): number => {
  const num = randomIntFromInterval(0, 80);
  return except.has(num) ? getUniqueNumber(except) : num;
};

const uuid = () => (Date.now() * Math.random()).toString(36);

const toHumanString = (num: number): string =>
  num < 10 ? `0${num}` : `${num}`;

const generatePeople = (except: Set<number>): ITableItem => {
  const first_name = getRandomElement(firstNameConventions),
    last_name = getRandomElement(lastNameConventions);
  return {
    name: `${first_name} ${last_name}`,
    email: `${first_name}.${getRandomElement([last_name, uuid()])}@gmail.com`,
    potatoes: getUniqueNumber(except),
    id: except.size,
  };
};

const onStart = (peoples: number) => {
  process.value = "PROCESSING";

  iziToast.warning({
    title: "Processing",
    message: "Generating random peoples.",
  });

  const except = new Set<number>();

  lists.value = Array(peoples)
    .fill("")
    .map(() => {
      const person = generatePeople(except);
      except.add(person.potatoes);
      return person;
    });

  timer.value = Date.now();
  isCompleted.value = false;

  process.value = "STABLE";

  iziToast.info({
    title: "Generated",
    message: `${lists.value.length} peoples added.`,
  });
};

const dragEnd = () => {
  const potatoes: number[] = lists.value.map((v) => v.potatoes);
  const isInAscending = potatoes.slice(1).every((e, i) => e > potatoes[i]);
  if (isInAscending) {
    const consumedTime = new Date(Date.now() - timer.value),
      m = consumedTime.getMinutes(),
      s = consumedTime.getSeconds();

    sortingTime.value = `${toHumanString(m)}:${toHumanString(s)}`;

    iziToast.success({
      message: `You have completed the sorting in ${sortingTime.value}`,
      title: "Congratulation",
    });
    isCompleted.value = true;
    lists.value = [];
  }
};

const process: Ref<"STABLE" | "PROCESSING"> = ref("STABLE");
const timer: Ref<number> = ref(0);
const isCompleted: Ref<boolean> = ref(false);
const sortingTime: Ref<string> = ref("");
</script>

<template>
  <div class="wrapper">
    <header>
      <h1>Sorting training system</h1>
      <div v-show="process === 'STABLE'"><Modal @start="onStart" /></div>
    </header>
    <div class="container">
      <div class="head">
        <strong>{{ lists.length }} people(s) in the list</strong>
      </div>
      <table v-if="!isCompleted">
        <thead>
          <tr>
            <th>Email</th>
            <th>Potatoes</th>
            <th>Full name</th>
          </tr>
        </thead>
        <draggable v-model="lists" item-key="id" tag="tbody" @end="dragEnd">
          <template #item="{ element }">
            <tr>
              <td>{{ element.email }}</td>
              <td>{{ element.potatoes }}</td>
              <td>{{ element.name }}</td>
            </tr>
          </template>
        </draggable>
      </table>
      <div v-else class="alert">
        <!-- image from Flaticon -->
        <img src="../assets/goal.png" alt="Congratulation!" />
        <div class="message">
          <strong class="title">Congratulation!</strong> <br />
          <strong class="text-success">
            You have completed the sorting in {{ sortingTime }}
          </strong>
          <br />
          <small>
            To start again click on the <kbd>Start sorting</kbd> button.
          </small>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.wrapper {
  width: 80%;
  margin: 20px auto;
}
.wrapper header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.container {
  user-select: none;
  border-radius: 10px;
  background-color: #fff;
  box-shadow: 0px 0px 5px 5px #ddd;
  padding-bottom: 4rem;
}
.head {
  padding: 2rem 1rem;
  text-align: right;
}
table {
  width: 100%;
}
td,
th {
  border-bottom: 1px solid #eee;
  padding: 1rem;
}
td:not(:last-child),
th:not(:last-child) {
  border-right: 1px solid #eee;
}
th {
  text-align: start;
  border-top: 1px solid #eee;
}
tr {
  transition: all 0.2s ease-in;
}
tbody tr:hover {
  background-color: #f1f1f1;
}

.alert {
  display: flex;
  justify-content: space-around;
  align-items: center;
  border-left: 1rem solid green;
  max-height: 30vh;
  border-radius: 10px;
  box-shadow: 0 0 5px 2px #eee;
  margin: auto;
  padding: 1rem 2rem;
  max-width: fit-content;
}
.alert img {
  max-width: 100px;
}
.alert .message {
  padding: 0 2rem;
}

.text-success {
  color: #2f592f;
}
.title {
  color: green;
  font-weight: bold;
  font-size: larger;
}

.alert small {
  color: #777;
}
</style>
