<template>
  <q-card class="page-card q-mt-xl">
    <div class="container">
      <div class="Tasks">
        <q-list
          bordered
          style="border-radius: 30px"
          class="shadow-1"
          v-for="process in openProcesses"
          :key="process.id"
        >
          <q-item class="pop-list">
            <q-item-section
              class="pop-item bordered q-pa-sm text-weight-bold text-white"
              :class="`bg-${process.color}`"
              >#{{ process.id }}</q-item-section
            >
          </q-item>
        </q-list>
        <div
          class="flex full-height self-center justify-center text-center q-ml-md"
        >
          <q-icon name="mdi-arrow-right-thick" size="30px" color="green" />
        </div>
      </div>
      <div class="Cpu">
        <div class="full-width text-center q-mb-md text-weight-bold">
          {{ openProcesses.length }} processo(s) na fila
        </div>

        <div class="cpu-div text-center">
          <div
            style="height: 190px; flex: auto"
            class="flex justify-center items-center"
          >
            <div class="text-center">
              <q-icon name="mdi-cpu-64-bit" size="4rem" />
            </div>
            <div class="full-width">
              <template
                v-for="processing in state.actualProcess"
                :key="processing"
              >
                <q-btn :color="processing.color" :label="`#${processing.id}`" />
              </template>
            </div>
          </div>
        </div>
      </div>
      <div class="Form">
        <q-card class="q-pa-md" style="width: 80%; border-radius: 30px">
          <q-card-section title class="text-h6 text-weight-bold text-center">
            Ações
          </q-card-section>
          <q-form>
            <div class="row justify-around">
              <q-select
                dense
                class="col-12 q-my-sm"
                :options="processes.map((p) => p.id)"
                label="Id do processo"
                outlined
                v-model="state.process.id"
              />

              <q-input
                dense
                class="col-12 q-my-sm"
                label="Nome do processo"
                outlined
                v-model="state.process.name"
              />

              <q-select
                dense
                class="col-12 q-my-sm text-capitalize"
                :options="status"
                label="Ação"
                v-model="state.process.status"
                outlined
              />

              <q-select
                dense
                class="col-12 q-my-sm"
                :options="priorities"
                label="Prioridade"
                v-model="state.process.priority"
                outlined
              />

              <div class="row justify-around">
                <q-btn
                  label="Criar"
                  color="green"
                  @click="addProcess"
                  class="col-4 q-ma-sm"
                  dense
                />

                <q-btn
                  label="Salvar"
                  color="blue"
                  @click="saveProcess"
                  class="col-4 q-ma-sm"
                  dense
                />

                <q-btn
                  label="Resetar"
                  type="reset"
                  @click="clearState"
                  color="negative"
                  flat
                  class="col-4 q-ma-sm"
                  dense
                />
              </div>
            </div>
          </q-form>
        </q-card>
      </div>
      <div class="Blocked">
        <q-card class="q-pa-md" style="width: 80%; border-radius: 30px">
          <q-card-section title class="text-h6 text-weight-bold text-center">
            Suspensos: {{ blockedProcesses.length }}
          </q-card-section>
          <q-list
            bordered
            v-for="process in blockedProcesses"
            :key="process.id"
          >
            <q-item clickable v-ripple>
              <q-item-section avatar>
                <q-icon color="red" name="mdi-close-thick" />
              </q-item-section>
              <q-item-section
                >{{ process.name }} #{{ process.id }}
              </q-item-section>
            </q-item>
          </q-list>
        </q-card>
      </div>
      <div class="Notify">
        <h5 style="font-weight: 800">Processos</h5>
      </div>
    </div>
  </q-card>
</template>
<script lang="ts">
import { useNotifier } from 'src/utils/notifier';
import { defineComponent, reactive, watch } from 'vue';

export default defineComponent({
  setup() {
    const notify = useNotifier();

    const priorities = ['high', 'medium', 'low'];

    const status = [
      'executando',
      'suspenso',
      'finalizado',
      'bloqueado',
      'criado',
      'pronto',
    ];

    const defaultProcess = {
      id: 0,
      name: '',
      priority: 'medium',
      color: 'teal',
      status: 'criado',
      timer: 30,
    };

    const state = reactive({
      process: defaultProcess,
      actualProcess: [
        {
          id: 0,
          name: '',
          priority: 'medium',
          color: 'teal',
          status: 'executando',
          timer: 30,
        },
      ],
    });

    const processes = reactive([
      {
        id: 1,
        name: 'Processo AA',
        priority: 'high',
        color: 'teal',
        status: 'pronto',
        timer: 29,
      },
      {
        id: 2,
        name: 'Processo BB',
        priority: 'medium',
        color: 'yellow',
        status: 'suspenso',
        timer: 30,
      },
    ]);

    const openProcesses = reactive([
      {
        id: 1,
        name: 'Processo AA',
        priority: 'high',
        color: 'teal',
        status: 'pronto',
        timer: 29,
      },
    ]);

    const blockedProcesses = reactive([
      {
        id: 2,
        name: 'Processo BB',
        priority: 'medium',
        color: 'yellow',
        status: 'suspenso',
        timer: 30,
      },
    ]);

    const clearState = () => {
      state.process = {
        id: 0,
        name: '',
        priority: 'medium',
        color: 'teal',
        status: 'criado',
        timer: 30,
      };
    };

    const defineTime = (status: string, priority: string) => {
      const originalTime = 20;
      let time = originalTime;

      switch (status) {
        case 'criado':
          time = originalTime;
          break;

        case 'pronto':
          time = 15;
          break;
      }

      const modifiedTime = time;

      switch (priority) {
        case 'low':
          time = modifiedTime;
          break;

        case 'medium':
          time = time - Math.ceil(modifiedTime / 3);
          break;

        case 'high':
          time = time - Math.ceil(modifiedTime / 2);
          break;
      }

      return time;
    };

    const addProcess = () => {
      try {
        if (processes.some((process) => process.id === state.process.id)) {
          state.process.id = processes.length + 1;
        }

        if (state.process.priority === 'low') {
          state.process.color = 'teal';
        } else if (state.process.priority === 'medium') {
          state.process.color = 'yellow';
        } else if (state.process.priority === 'high') {
          state.process.color = 'red';
        }

        state.process.timer = defineTime(
          state.process.status,
          state.process.priority
        );

        processes.push(state.process);
        if (
          state.process.status === 'pronto' ||
          state.process.status === 'criado' ||
          state.process.status === 'executando'
        ) {
          openProcesses.push(state.process);
        } else if (
          state.process.status === 'bloqueado' ||
          state.process.status === 'finalizado' ||
          state.process.status === 'suspenso'
        ) {
          blockedProcesses.push(state.process);
        }

        if (state.process.status === 'executando') {
          state.actualProcess.push(state.process);
        }

        void clearState();

        notify.success('Processo criado com sucesso');
      } catch (error) {
        void clearState();
        notify.error('Falha ao criar, cheque o log');
        console.log(error);
      }
    };

    const findProcess = () => {
      const process = processes.find(
        (process) => process.id === state.process.id
      );
      if (process) {
        state.process = { ...process };
      }
    };

    const saveProcess = () => {
      try {
        const process = processes.find(
          (process) => process.id === state.process.id
        );

        switch (state.process.priority) {
          case 'low':
            state.process.color = 'teal';
            break;

          case 'medium':
            state.process.color = 'yellow';
            break;

          case 'high':
            state.process.color = 'red';
            break;
        }

        state.process.timer = defineTime(
          state.process.status,
          state.process.priority
        );

        let processIndex = 0;

        if (
          process?.status === 'pronto' ||
          process?.status === 'criado' ||
          process?.status === 'executando'
        ) {
          processIndex = openProcesses.findIndex(
            (process) => process.id === state.process.id
          );
        } else {
          processIndex = blockedProcesses.findIndex(
            (process) => process.id === state.process.id
          );
        }

        if (
          process?.status === 'pronto' ||
          process?.status === 'criado' ||
          process?.status === 'executando'
        ) {
          openProcesses.splice(processIndex, 1);
        } else {
          blockedProcesses.splice(processIndex, 1);
        }

        processIndex = processes.findIndex(
          (process) => process.id === state.process.id
        );

        processes.splice(processIndex, 1);

        if (
          state.process?.status === 'pronto' ||
          state.process?.status === 'criado' ||
          state.process?.status === 'executando'
        ) {
          openProcesses.push(state.process);
        } else {
          blockedProcesses.push(state.process);
        }

        processes.push(state.process);
        void clearState();

        notify.success('Processo alterado com sucesso');
      } catch (error) {
        void clearState();
        notify.error('Falha ao criar, cheque o log');
        console.log(error);
      }
    };

    const stateChanger = (status: string) => {
      switch (status) {
        case 'criado':
          return 'pronto';
        case 'pronto':
          return 'executando';

        case 'executando':
          return 'finalizado';
        case 'bloqueado':
          return 'bloqueado';
        case 'finalizado':
          return 'finalizado';
        default:
          return 'suspenso';
      }
    };

    const updateTime = () => {
      processes.forEach((process) => {
        if (process.timer > 0) {
          process.timer -= 1;
          if (
            process?.status === 'pronto' ||
            process?.status === 'criado' ||
            process?.status === 'executando'
          ) {
            const openProcess = openProcesses.find(
              (openProcess) => openProcess.id === process.id
            );
            if (openProcess) {
              openProcess.timer = process.timer;
            }
          } else {
            const blockedProcess = openProcesses.find(
              (blockedProcess) => blockedProcess.id === process.id
            );
            if (blockedProcess) {
              blockedProcess.timer = process.timer;
            }
          }
        } else {
          const status = process.status;
          process.timer = defineTime(process.status, process.priority);
          process.status = stateChanger(process.status);

          if (
            status === 'pronto' ||
            status === 'criado' ||
            status === 'executando'
          ) {
            notify.success(
              `Processo #${process.id}: ${status} => ${process.status}`
            );
          }

          if (
            process.status === 'finalizado' ||
            process.status === 'bloqueado' ||
            process.status === 'suspenso'
          ) {
            if (status === 'executando') {
              let index = state.actualProcess.findIndex(
                (aProcess) => aProcess.id === process.id
              );

              state.actualProcess.splice(index);

              index = openProcesses.findIndex(
                (aProcess) => aProcess.id === process.id
              );

              openProcesses.splice(index);
            }

            if (!blockedProcesses.some((bprocess) => bprocess === process)) {
              process.timer = defineTime(process.status, process.priority);

              blockedProcesses.push(process);
            }
          }

          if (process.status === 'executando') {
            process.timer = defineTime(process.status, process.priority);

            state.actualProcess.push(process);

            const index = openProcesses.findIndex(
              (oProcess) => oProcess.id === process.id
            );

            openProcesses.splice(index);
          }
        }
      });
    };

    const sortProcesses = () => {
      openProcesses.sort((a, b) => b.timer - a.timer);
    };

    setInterval(() => void updateTime(), 1000);
    setInterval(() => void sortProcesses(), 100);

    processes.shift();
    openProcesses.shift();
    blockedProcesses.pop();
    blockedProcesses.shift();
    state.actualProcess.shift();

    watch(
      () => state.process.id,
      () => {
        void findProcess();
      }
    );

    return {
      state,
      processes,
      priorities,
      status,
      openProcesses,
      addProcess,
      saveProcess,
      blockedProcesses,
      clearState,
    };
  },
});
</script>

<style scoped>
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 0.1fr 2.5fr 0.5fr;
  gap: 0px 0px;
  grid-auto-flow: row;
  grid-template-areas: 'Notify Notify Notify' 'Form Cpu Blocked' 'Tasks Tasks Tasks';
}
.Tasks {
  grid-area: Tasks;
  display: flex;
  align-self: center;
  justify-content: center;
}
.Cpu {
  grid-area: Cpu;
  display: block;
  align-self: center;
  justify-content: center;
  text-align: -webkit-center;
}
.Form {
  grid-area: Form;
  display: flex;
  align-self: center;
  justify-content: center;
}
.Blocked {
  grid-area: Blocked;
  display: flex;
  align-self: center;
  justify-content: center;
}
.Notify {
  grid-area: Notify;
  display: flex;
  align-self: center;
  justify-content: center;
}
.cpu-div {
  border: 1px solid grey;
  border-radius: 100px;
  width: 70%;
  display: flex;
}

.bordered {
  border-radius: 5px;
}

.pop-list {
  height: 0.05rem;
  position: relative;
}
.pop-item:hover,
.pop-item:focus {
  transform: translateY(-0.25em);
  transition-duration: 500ms;
}
</style>
