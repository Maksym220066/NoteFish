<template>
  <div class="fishing-table-container">
    <h2>Таблиця риболовлі</h2>

    <div class="button-container">
      <Button label="Додати запис" icon="pi pi-plus" @click="openAddDialog" />
    </div>

    <DataTable
      :value="fishingData"
      tableStyle="min-width: 50rem"
      class="p-datatable-sm"
    >
      <Column header="Номер вудлища" style="width: 12%">
        <template #body="slotProps">
          <div class="cell-items">
            <div v-for="(num, idx) in slotProps.data.rodNumbers" :key="idx">{{ num }}</div>
          </div>
        </template>
      </Column>

      <Column header="Дистанція" style="width: 12%">
        <template #body="slotProps">
          <div class="cell-items">
            <div v-for="(dist, idx) in slotProps.data.distances" :key="idx">{{ dist }}</div>
          </div>
        </template>
      </Column>

      <Column header="Насадка" style="width: 15%">
        <template #body="slotProps">
          <div class="cell-items">
            <div v-for="(b, idx) in slotProps.data.baits" :key="idx">{{ b }}</div>
          </div>
        </template>
      </Column>

      <Column header="Година закиду" style="width: 15%">
        <template #body="slotProps">
          <div class="cell-items">
            <div v-for="(time, idx) in slotProps.data.castTimes" :key="idx">{{ time }}</div>
          </div>
        </template>
      </Column>

      <Column header="Година поклівки" style="width: 15%">
        <template #body="slotProps">
          <div class="cell-items">
            <div v-for="(time, idx) in slotProps.data.biteTimes" :key="idx">{{ time }}</div>
          </div>
        </template>
      </Column>

      <Column header="Примітки" style="width: 20%">
        <template #body="slotProps">
          <div class="cell-items">
            <div v-for="(note, idx) in slotProps.data.notes" :key="idx">{{ note }}</div>
          </div>
        </template>
      </Column>

      <Column header="Дії" style="width: 11%">
        <template #body="slotProps">
          <Button
            icon="pi pi-pencil"
            severity="info"
            text
            rounded
            @click="openEditDialog(slotProps.data, slotProps.index)"
          />
          <Button
            icon="pi pi-trash"
            severity="danger"
            text
            rounded
            @click="deleteRow(slotProps.index)"
          />
        </template>
      </Column>
    </DataTable>

    <Dialog
      v-model:visible="dialogVisible"
      :header="dialogMode === 'add' ? 'Додати запис' : 'Редагувати запис'"
      :modal="true"
      :style="{ width: '800px' }"
    >
      <div class="dialog-content">
        <div class="field-group">
          <label>Номер вудлища</label>
          <div class="input-row">
            <InputText v-model="editedRow.rodNumbers[0]" placeholder="Вудлище 1" />
            <InputText v-model="editedRow.rodNumbers[1]" placeholder="Вудлище 2" />
            <InputText v-model="editedRow.rodNumbers[2]" placeholder="Вудлище 3" />
            <InputText v-model="editedRow.rodNumbers[3]" placeholder="Вудлище 4" />
          </div>
        </div>

        <div class="field-group">
          <label>Дистанція</label>
          <div class="input-row">
            <InputText v-model="editedRow.distances[0]" placeholder="Дистанція 1" />
            <InputText v-model="editedRow.distances[1]" placeholder="Дистанція 2" />
            <InputText v-model="editedRow.distances[2]" placeholder="Дистанція 3" />
            <InputText v-model="editedRow.distances[3]" placeholder="Дистанція 4" />
          </div>
        </div>

        <div class="field-group">
          <label>Насадка</label>
          <div class="input-row">
            <InputText v-model="editedRow.baits[0]" placeholder="Насадка 1" />
            <InputText v-model="editedRow.baits[1]" placeholder="Насадка 2" />
            <InputText v-model="editedRow.baits[2]" placeholder="Насадка 3" />
            <InputText v-model="editedRow.baits[3]" placeholder="Насадка 4" />
          </div>
        </div>

        <div class="field-group">
          <label>Година закиду</label>
          <div class="input-row">
            <InputText v-model="editedRow.castTimes[0]" placeholder="ГГ:ХХ" />
            <InputText v-model="editedRow.castTimes[1]" placeholder="ГГ:ХХ" />
            <InputText v-model="editedRow.castTimes[2]" placeholder="ГГ:ХХ" />
            <InputText v-model="editedRow.castTimes[3]" placeholder="ГГ:ХХ" />
          </div>
        </div>

        <div class="field-group">
          <label>Година поклівки</label>
          <div class="input-row">
            <InputText v-model="editedRow.biteTimes[0]" placeholder="ГГ:ХХ" />
            <InputText v-model="editedRow.biteTimes[1]" placeholder="ГГ:ХХ" />
            <InputText v-model="editedRow.biteTimes[2]" placeholder="ГГ:ХХ" />
            <InputText v-model="editedRow.biteTimes[3]" placeholder="ГГ:ХХ" />
          </div>
        </div>

        <div class="field-group">
          <label>Примітки</label>
          <div class="input-row">
            <InputText v-model="editedRow.notes[0]" placeholder="Примітки 1" />
            <InputText v-model="editedRow.notes[1]" placeholder="Примітки 2" />
            <InputText v-model="editedRow.notes[2]" placeholder="Примітки 3" />
            <InputText v-model="editedRow.notes[3]" placeholder="Примітки 4" />
          </div>
        </div>
      </div>

      <template #footer>
        <Button label="Скасувати" icon="pi pi-times" @click="closeDialog" severity="secondary" />
        <Button label="Зберегти" icon="pi pi-check" @click="saveRow" />
      </template>
    </Dialog>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import InputText from 'primevue/inputtext';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';

const STORAGE_KEY = 'fishing_table_data';

const fishingData = ref([]);

const dialogVisible = ref(false);
const dialogMode = ref('add');
const editedRow = ref({});
const editedIndex = ref(-1);

const saveToLocalStorage = () => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(fishingData.value));
};

const loadFromLocalStorage = () => {
  const stored = localStorage.getItem(STORAGE_KEY);
  if (stored) {
    try {
      fishingData.value = JSON.parse(stored);
    } catch (e) {
      console.error('Помилка завантаження даних з localStorage:', e);
      fishingData.value = [];
    }
  }
};

watch(fishingData, () => {
  saveToLocalStorage();
}, { deep: true });

onMounted(() => {
  loadFromLocalStorage();
});

const createEmptyRow = () => {
  return {
    rodNumbers: ['1', '2', '3', '4'],
    distances: ['', '', '', ''],
    baits: ['', '', '', ''],
    castTimes: ['', '', '', ''],
    biteTimes: ['', '', '', ''],
    notes: ['', '', '', '']
  };
};

const openAddDialog = () => {
  dialogMode.value = 'add';
  editedRow.value = createEmptyRow();
  dialogVisible.value = true;
};

const openEditDialog = (data, index) => {
  dialogMode.value = 'edit';
  editedIndex.value = index;
  editedRow.value = {
    rodNumbers: [...data.rodNumbers],
    distances: [...data.distances],
    baits: [...data.baits],
    castTimes: [...data.castTimes],
    biteTimes: [...data.biteTimes],
    notes: [...data.notes]
  };
  dialogVisible.value = true;
};

const saveRow = () => {
  if (dialogMode.value === 'add') {
    fishingData.value = [
      {
        rodNumbers: [...editedRow.value.rodNumbers],
        distances: [...editedRow.value.distances],
        baits: [...editedRow.value.baits],
        castTimes: [...editedRow.value.castTimes],
        biteTimes: [...editedRow.value.biteTimes],
        notes: [...editedRow.value.notes]
      },
      ...fishingData.value]
  } else {
    fishingData.value[editedIndex.value] = {
      rodNumbers: [...editedRow.value.rodNumbers],
      distances: [...editedRow.value.distances],
      baits: [...editedRow.value.baits],
      castTimes: [...editedRow.value.castTimes],
      biteTimes: [...editedRow.value.biteTimes],
      notes: [...editedRow.value.notes]
    };
  }
  closeDialog();
};

const closeDialog = () => {
  dialogVisible.value = false;
  editedRow.value = {};
  editedIndex.value = -1;
};

const deleteRow = (index) => {
  fishingData.value.splice(index, 1);
};
</script>

<style scoped>
.fishing-table-container {
  padding: 2rem;
  max-width: 1400px;
  margin: 0 auto;
}

h2 {
  color: #2c3e50;
  margin-bottom: 1.5rem;
}

.button-container {
  margin-bottom: 1rem;
}

:deep(.p-datatable) {
  font-size: 0.9rem;
}

:deep(.p-datatable .p-datatable-tbody > tr > td) {
  padding: 0.75rem;
}

:deep(.p-datatable .p-datatable-thead > tr > th) {
  background-color: #f8f9fa;
  color: #495057;
  font-weight: 600;
  padding: 1rem 0.75rem;
}

.dialog-content {
  padding: 1rem 0;
}

.field-group {
  margin-bottom: 1.5rem;
}

.field-group label {
  display: block;
  margin-bottom: 0.75rem;
  color: #495057;
  font-weight: 600;
  font-size: 1rem;
}

.input-row {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.75rem;
}

.cell-items {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.cell-items > div {
  padding: 0.25rem 0;
}

.w-full {
  width: 100%;
}

:deep(.p-inputtext) {
  width: 100%;
}
</style>
