<script setup>
import VModal from './VModal.vue'
import TextBox from './TextBox.vue'
import MemberBox from './MemberBox.vue'

import * as M from '../model.js'
import { ref } from 'vue'

const emit = defineEmits(['add', 'edit'])

const props = defineProps({
  group: Object,
  isAdd: Boolean, // otherwise, editing existing instead of adding
})

let modalRef = ref(null);

function setGroup() {
  const group = props.group;
  const form = document.getElementById("editOrAddGroup");
  
  // Verificar si alguna máquina virtual está encendida o suspendida
  const machinesRunningOrSuspended = group.members.some(memberId => {
    const vm = M.resolve(memberId);
    return vm.state === M.VmState.RUNNING || vm.state === M.VmState.SUSPENDED;
  });

  // Si hay máquinas virtuales encendidas o suspendidas, mostrar un mensaje de error
  if (machinesRunningOrSuspended) {
    alert('No se puede editar el grupo. Al menos una máquina virtual está encendida o suspendida.');
    return;
  }

  const valueFor = (name) => {
    const input = form.querySelector(`input[name=${name}]`)
    if (!input) console.log("ERROR: no input for name", name, "in", form)
    return input.value
  }

  console.log("saving group...", group, form)

  // Comprueba validez de todos los campos, y sobreescribe resultado
  if (!form.checkValidity()) {
    // Fuerza a que se muestren los errores simulando un envío
    // (pero como hay errores, no se va a enviar nada :-)
    form.querySelector("button[type=submit]").click()
    return;
  }

  // Todo válido: lanza evento al padre y cierra modal
  emit(props.isAdd ? 'add' : 'edit', new M.Group(group.id,
    valueFor("g-name"), JSON.parse(valueFor("g-members"))
  ))
  modalRef.value.hide()
}

// para que el padre pueda llamar a show (hide no debería hacer falta)
function show() {
  modalRef.value.show();
}
defineExpose({ show });
</script>

<template>
  <VModal ref="modalRef" id="groupAddOrEditModal"
    :title="isAdd ? 'Añadiendo nuevo grupo' : `Editando grupo: ${group.name}`" >
    <template #body>
      <form id="editOrAddGroup" 
        @submit.prevent="e => setGroup()">
        <div class="container">
          <TextBox :start="group.name" id="g-name" label="nombre" />
        </div>
        <div class="container">
          <MemberBox :start="group.members" :all="M.getVms()" id="g-members" label="miembros" />
        </div>
        <button type="submit" class="invisible">Submit</button>
      </form>
    </template>
    <template #footer>
      <button @click.prevent="() => setGroup()" class="btn btn-primary">
        {{ isAdd ? 'Añadir este grupo' : `Confirmar cambios a ${group.name}` }}
      </button>
    </template>
  </VModal>
</template>

<style scoped>
</style>
