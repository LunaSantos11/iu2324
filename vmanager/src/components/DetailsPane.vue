<script setup>
import { resolve, VmState } from '../model.js'

defineEmits(['editVm', 'filterVm', 'rmVm', 'editGroup', 'filterGroup', 'rmGroup', 'setState'])

const props = defineProps({
  element: Object
})

function list(state) {
  return props.element.members
    .map(v=>resolve(v))
    .filter(vm=>state ? vm.state == state : true)
    .map(o=>o.name).join(' ');
}
</script>

<template>
  <div v-if="element == null || element.id == -1"  class="responsive-message"> <!--cambio ej5-->
    (selecciona una Vm o un grupo para ver sus detalles)
  </div>
  <div v-else-if="Array.isArray(element.groups)" class = "table-responsive"> <!--cambio ej5-->
    <h4>máquina virtual <span class="name">{{element.name}}</span></h4>

    <table class = "table table-bordered table-hover table-responsive">  <!--cambio ej5-->
      <tr>
        <th>Estado</th>
        <td>{{ element.state }} </td>
      </tr>
      <tr>
        <th>Memoria</th>
        <td>{{ element.ram }} Gb</td>
      </tr>
      <tr>
        <th>Disco</th>
        <td>{{ element.hd }} Gb</td>
      </tr>
      <tr>
        <th>Máximo uso de CPU</th>
        <td>{{ element.cpu }} %</td>
      </tr>
      <tr>
        <th>Núcleos de CPU</th>
        <td>{{ element.cores }}</td>
      </tr>
      <tr>
        <th>Dirección IPv4</th>
        <td>{{ element.ip }}</td>
      </tr>
      <tr>
        <th>Ancho de banda máximo</th>
        <td>{{ element.up }} Kbps de subida<br>{{ element.down }} Kbps de bajada</td>
      </tr>
      <tr>
        <th>Disco externo virtual</th>
        <td v-if="element.iso != -1">{{ resolve(element.iso).name }}</td>
        <td v-else> (ninguno) </td>
      </tr>
      <tr>
        <th>Grupos a los que pertenece</th>
        <td v-if="element.groups.length">
          {{ element.groups.map(g => resolve(g).name).join(' , ') }}
        </td>
        <td v-else> (ninguno) </td>
      </tr>
    </table>
  
    <h5>Acciones</h5> <!-- CAMBIOS EJERCICIO 4 -->
    <div class="btn-group">
      <button  title = "EDITAR" @click="$emit('editVm')" class="btn btn-outline-success">✏️</button>

      <button title = "FILTRAR" v-if="element.groups.length" class="btn btn-outline-warning"
       @click="$emit('filterVm')" >🔬</button>
      
      <button v-if="element.state != VmState.RUNNING" class="btn btn-outline-secondary"
      title = "CAMBIAR A ENCENDIDA" @click="$emit('setState', VmState.RUNNING)" >▶</button>
      <button v-if="element.state != VmState.SUSPENDED" class="btn btn-outline-secondary"
      title = "CAMBIAR A SUSPENDIDA" @click="$emit('setState', VmState.SUSPENDED)">💤</button>
      <button v-if="element.state != VmState.STOPPED" class="btn btn-outline-secondary"
      title = "CAMBIAR A APAGADA" @click="$emit('setState', VmState.STOPPED)">🛑</button>
      
      <button  title = "ELIMINAR" @click="$emit('rmVm')" class="btn btn-outline-danger">🗑️</button>
    </div>

    </div>

  <div v-else>
    <h4>grupo <span class="name">{{element.name}}</span></h4>

    <b>{{ element.members.length }} integrantes</b>
    <table>
      <tr>
        <th>{{ element.members.length }} integrantes</th>
        <td v-if="element.members.length">{{ list(false) }}
        </td>
        <td v-else> (no hay) </td>
      </tr>
      <tr>
        <th>Encendidas</th>
        <td v-if="list(VmState.RUNNING).length">{{ list(VmState.RUNNING) }}</td>
        <td v-else> (no hay) </td>
      </tr>
      <tr>
        <th>Suspendidas</th>
        <td v-if="list(VmState.SUSPENDED).length">{{ list(VmState.SUSPENDED) }}</td>
        <td v-else> (no hay) </td>
      </tr>
      <tr>
        <th>Apagadas</th>
        <td v-if="list(VmState.STOPPED).length">{{ list(VmState.STOPPED) }}</td>
        <td v-else> (no hay) </td>
      </tr>
    </table>

    
    <h5>Acciones</h5>
    <div class="btn-group"> <!-- CAMBIOS EJERCICIO 4 -->
      <button title = "EDITAR" @click="$emit('editGroup')" class="btn btn-outline-success">✏️</button>
      <button title = "FILTRAR" @click="$emit('filterGroup')" class="btn btn-outline-warning">🔬</button>
      <button title = "ELIMINAR" @click="$emit('rmGroup')" class="btn btn-outline-danger">🗑️</button>
    </div>
  </div>
</template>

<style scoped>
  tr>th {
    width: 10em;
    text-align: right;
  }
  .name {
    font-weight: 1000;
  }
  td, th {
    padding: 4px;
  }
  h5 {
    margin-top: 1em;
  }

  /*cambio ej 5*/
  @media (max-width: 768px) {

  tr>th {
    width: auto;
  }
}

</style>
