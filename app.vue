<template>
  <div class="page">
    <header>
      <h1>Subs Tracker</h1>
      <div @click="toggleDarkMode" class="btn">
        <IconsMoon v-if="isDarkMode" />
        <IconsSun v-else />
      </div>
    </header>
    <div class="wrap-total">
      <p class="label">Monthly cost</p>
      <p class="total">{{ "R$" + totalCost.toFixed(2) }}</p>
    </div>
    <SubsList :services="data" />
  </div>
</template>

<script setup lang="ts">
const isDarkMode = ref(true);
const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value;
  if (isDarkMode.value) {
    document.body.classList.remove("light-mode");
  } else {
    document.body.classList.add("light-mode");
  }
};

onMounted(() => {
  if (!isDarkMode.value) {
    document.body.classList.add("light-mode");
  }
});

const { data } = useSupabase();
const totalCost = computed(() => {
  return data.value.reduce((total, item) => {
    if (item.service_charge_frequency === "monthly") {
      return total + item.service_cost;
    } else if (item.service_charge_frequency === "yearly") {
      return total + item.service_cost / 12;
    } else {
      return total;
    }
  }, 0);
});
</script>

<style lang="scss">
header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 3.2rem;

  h1 {
    font-size: 1.2rem;
    font-weight: 800;
    color: var(--secondary-text);
  }

  .btn {
    display: flex;
    padding: 0.4rem 0.4rem;
    border: none;
    border-radius: 56px;
    background-color: var(--surface);
    color: var(--base-text);
    cursor: pointer;
    transform: translateY(0);
    transition: transform opacity 0.15s ease-in-out;
    opacity: 0.7;

    &:hover {
      transform: translateY(4px);
      opacity: 1;
    }

    svg {
      width: 1.6rem;
      height: 1.6rem;
      stroke: var(--base-text);
    }
  }
}

.wrap-total {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  gap: 0.4rem;
  margin-bottom: 2.4rem;

  .label {
    font-size: 1.2rem;
    font-weight: 400;
    color: var(--secondary-text);
  }
  .total {
    font-size: 2.4rem;
    font-weight: 800;
    line-height: 100%;
    color: var(--base-text);
  }
}
</style>
