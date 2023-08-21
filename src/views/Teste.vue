<template>
	<div>
		<button class="btn btn-primary" @click="notificacao">Teste Toast</button>
	</div>
</template>

<script>
  import { toast } from 'vue3-toastify'

  export default {
    setup(){
      const notificacao = () => {
        toast.success("Notificação",{
          autoClose: 2000,
          position: 'top-center',
          closeButton: false,
          hideProgressBar: false,
          multiple: false,
          icon: ''
        })        
      }
      return {notificacao}
    }
  }
</script>

<style>
</style>