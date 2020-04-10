<template>
	<div id="app" class="container">
		<b-alert show dismissible v-for="mensagem in mensagens"
				:key="mensagem.texto"
				:variant="mensagem.tipo"> {{mensagem.texto}}

		</b-alert>
		<b-card>
			<b-form-group label="Nome">
				<b-form-input type="text"
								size="lg"
								v-model="usuario.nome" 
								placeholder="Informe o nome">
				</b-form-input>
			</b-form-group>
			
			<b-form-group label="E-mail">	
				<b-form-input type="text"
								size="lg"
								v-model="usuario.email" 
								placeholder="Informe o E-mail">
				</b-form-input>
			</b-form-group>
			<hr>
			<b-button @click="salvar" 
					size="lg"
					variant="primary">salvar
			</b-button>

			<b-button @click="buscarUsuarios" 
					size="lg"
					class="ml-2"
					variant="success">Buscar Usuários
			</b-button>

			<b-list-group>
				<b-list-group-item v-for="(usuario, id) in usuarios" :key="id">
					<strong>Nome: </strong> {{usuario.nome}} <br>
					<strong>E-mail: </strong> {{usuario.email}}<br>
					<strong>ID: </strong> {{usuario.id}}<br>
					<b-button @click="carregar(usuario.id)" 
						size="lg"
						variant="warning">Carregar
					</b-button>

					<b-button @click="excluir(usuario.id)" 
						size="lg"
						class="ml-2"
						variant="danger">Excluir
					</b-button>
				</b-list-group-item>
			</b-list-group>
		</b-card>
		
	</div>
</template>

<script>
export default {

	data() {
		return {
			mensagens: [],
			id: null,
			usuarios: [],
			usuario: {
				nome: '',
				email: ''
			}
		}
	},
	/* created() {
		this.$http.post('usuarios.json', {
			nome: 'Daniel',
			email:'daniel.lemes.borges@gmail.com'
		})
	}*/

	methods: {
		limpar() {
			this.usuario.nome = ''
			this.usuario.email = ''
			this.id = null,
			this.mensagens = []
		},
		salvar() {
			const metodo = this.id ? 'patch' : 'post'
			const finalUrl = this.id ? `/${this.id}.json`: '.json'
			this.$http[metodo](`/usuarios${finalUrl}`, this.usuario)
				.then( _ => {
					this.limpar()
					this.mensagens.push({
						texto: 'Operação concluida com sucesso',
						tipo: 'success'
					})	
				})
		},
		buscarUsuarios() {
			this.$http.get('usuarios.json')
				.then(resp => {
					this.usuarios = resp.data
				})
		},
		carregar(id) {
			this.id = id
			this.usuario = this.usuarios.filter(u => u.id == id)[0]
		},

		excluir(id) {
			this.$http.delete(`/usuarios/${id}.json`)
				.then(_ => {
					this.limpar()
					this.mensagens.push({
						texto:'Registro excluído com sucesso',
						tipo:'danger'
					})
				})
				.catch(err => {
					this.mensagens.push({
						texto: 'Erro ao excluir',
						tipo: 'danger'
					})
				})
		}
	}
}
</script>

<style>
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #2c3e50;
	font-size: 1.5rem;
}

#app h1 {
	text-align: center;
	margin: 50px;
}
</style>
