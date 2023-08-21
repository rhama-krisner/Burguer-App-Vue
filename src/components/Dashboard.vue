<template>
	<div id="burger-table">
			<div>
				<div id="burger-table-heading">
					<div class="order-id">#</div>
					<div>Cliente:</div>
					<div>Pão:</div>
					<div>Carne:</div>
					<div>Opcionais:</div>
					<div>Ações:</div>
				</div>
			</div>
			<div id="burger-table-rows">
				<div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
					<div class="order-number">{{ burger.id }}</div>
					<div>{{ burger.name }}</div>
					<div>{{ burger.pao }}</div>
					<div>{{ burger.carne }}</div>
					<div>
						<ul>
							<li v-for="(opcional, index) in burger.opcionais" :key="index">{{ opcional }}</li>
						</ul>
					</div>
					<div class="actions-container">
						<select name="status" class="status form-select" @change="updateBurger($event, burger.id)">
							<option v-for="s in status"
							:key="s.id" 
							:value="s.tipo" 
							:selected="burger.status == s.tipo">{{ s.tipo }}</option>
						</select>
						<button class="delete-btn btn btn-danger" @click="deleteBurgerAndNotify(burger)">Excluir</button>
					</div>
				</div>
			</div>
		</div>
</template>

<script>
import { toast } from 'vue3-toastify'
export default {
    name: "Dashboard",
	data() {
		return {
			burgers: null,
			burger_id: null,
			status: []
		}
	},
	methods: {
		async notify(message) {
			toast.error(message,{
				autoClose: 2000,
				position: 'top-center',
				closeButton: false,
			})
		},
		async getPedidos() {
			const req = await fetch("http://localhost:3000/burgers")
			const data = await req.json()
			this.burgers = data

			//resgatar os status
			this.getStatus()
		},
		async getStatus(){
			const req = await fetch("http://localhost:3000/status")
			const data = await req.json()
			this.status = data

		},
		async deleteBurger(id){
			const req = await fetch(`http://localhost:3000/burgers/${id}`,{
				method: 'DELETE',

			})

			const res = await req.json()

			//msg
			this.getPedidos()

		},
		async deleteBurgerAndNotify(burger) {
      		await this.deleteBurger(burger.id);
      		await this.notify(`Pedido n°${burger.id} foi deletado com sucesso!`);
			console.log(burger.id)
    	},
		async updateBurger(event, id){
			const option = event.target.value

			const dataJson = JSON.stringify({status: option})

			const req = await fetch(`http://localhost:3000/burgers/${id}`,{
                method: "PATCH",
                headers: {"Content-Type": "application/json" },
                body: dataJson
            });

			const res = await req.json()
			console.log(res)
		}
		
	},
	mounted() {
		this.getPedidos()
	}
}
</script>

<style scoped>
	#burger-table {
		max-width: 1200px;
		margin: 0 auto;
	}

	#burger-table-heading,
	#burger-table-rows,
	
    .burger-table-row {
		display: flex;
		flex-wrap: wrap;
	}

	#burger-table-heading {
		font-weight: bold;
		padding: 12px;
		border-bottom: 3px solid #333;
	}

    #burger-table-heading div,
    .burger-table-row div{
        width: 19%;
    }

	.burger-table-row {
		width: 100%;
		padding: 12px;
		border-bottom: 1px solid #ccc;
	}

    #burger-table-heading .order-id,
    .burger-table-row .order-number{
        width: 5%;
    }

    .actions-container {
		display: flex;
		align-items: center; /* Centraliza verticalmente */
		gap: 10px; /* Espaçamento entre o select e o botão */
    }

    select{
        width: 150px;
    }

    .delete-btn{
        font-weight: bold;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
    }


</style>