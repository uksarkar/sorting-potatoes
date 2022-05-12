<script setup lang="ts">
import { computed, Ref, ref, watch } from "vue";

const emit = defineEmits<{ (e: "start", peoples: number): void }>();

const onSubmit = () => {
  if (isValidForm.value) {
    emit("start", peoples.value!);
    showModal.value = false;
    peoples.value = null;
  }
};

const peoples: Ref<number | null> = ref(null);
const error: Ref<string | null> = ref(null);
const showModal: Ref<boolean> = ref(false);

const isValidForm = computed({
  get() {
    return (
      error.value == null &&
      peoples.value &&
      peoples.value >= 20 &&
      peoples.value <= 100
    );
  },
  set(val) {},
});

const toggleModal = () => {
  showModal.value = !showModal.value;
};

watch(peoples, (newValue) => {
  error.value =
    newValue && newValue < 20
      ? "Please enter at least 20."
      : newValue && newValue > 100
      ? "Enter maximum 100."
      : null;
});
</script>

<template>
  <button
    v-show="!showModal"
    type="button"
    @click="toggleModal"
    class="primary"
  >
    Start sorting
  </button>
  <form v-if="showModal" @submit.prevent="onSubmit" action="">
    <div @click.self="toggleModal" class="overly">
      <div class="modal">
        <div class="head">
          <h2>How many people?</h2>
          <button type="button" @click="toggleModal">&times;</button>
        </div>
        <div class="body">
          <label for="peoples">
            Enter the number of how many people you want to add to the list.
          </label>
          <input
            type="number"
            v-model="peoples"
            name="peoples"
            id="peoples"
            placeholder="20-100"
          />
          <div class="feedback" style="{opacity: error ? 1:0;}">
            <strong>{{ error }}</strong>
          </div>
        </div>
        <div class="footer">
          <button type="button" @click="toggleModal" class="cancel">
            Cancel
          </button>
          <button :disabled="!isValidForm" class="primary">Start</button>
        </div>
      </div>
    </div>
  </form>
</template>

<style scoped>
.overly {
  position: absolute;
  background: #0008;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  background: #fff;
  width: 30vw;
  border-radius: 10px;
}

.modal .head {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 2px solid #eee;
  padding: 1rem;
}

.modal .head h2 {
  margin: 0;
}

.body,
.footer {
  padding: 1rem;
}
.body {
  margin: 2rem 1rem;
}
.modal label {
  color: gray;
}
.modal input {
  width: 100%;
  display: inline-block;
  border-radius: 5px;
  line-height: 2rem;
  border: 1px solid #eee;
  padding: 0.1rem 0.3rem;
}

.modal .head button {
  padding: 0.5rem;
  font-size: 2rem;
}

.modal .head button:hover {
  color: red;
}

.footer {
  border-top: 2px solid #eee;
  display: flex;
  justify-content: end;
}
.footer button.primary {
  margin-left: 5px;
}

.feedback {
  color: red;
  margin-top: 0.5rem;
  transition: all 0.2s ease-in;
  height: 1rem;
}
</style>
