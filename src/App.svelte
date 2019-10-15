<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	const [send, receive] = crossfade({
		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});

	let todos = [
		{ id: 1, state: 2, description: 'Painel Administrativo' },
		{ id: 2, state: 0, description: 'Documentação' },
		{ id: 3, state: 0, description: 'Carrinho de compras' },
		{ id: 4, state: 1, description: 'Login do Usuário' },
		{ id: 5, state: 0, description: 'Perfil do Usuário' },
		{ id: 6, state: 2, description: 'Forum' },
	];

	let uid = todos.length + 1;

	function add(input) {
		const todo = {
			id: uid++,
			state: false,
			description: input.value
		};

		todos = [todo, ...todos];
		input.value = '';
	}


	function alterar(todo, tipo) {
		todos[todo-1].state = tipo;
	}

	function remove(todo) {
		todos = todos.filter(t => t !== todo);
	}
</script>

<style>
	*{
		margin: 0;
		padding: 0;
		outline: 0;
		box-sizing: border-box;
	}
	
	.main{
		padding:30px;
		background-color:#f2f2f2;
		width:100%;
		height:100%;
	}
	
	.new-todo {
		font-size: 1.4em;
		width: 100%;
		margin: 2em 0 1em 0;
	}

	.board {
		
		max-width: 600em;
		margin: 0 auto;
	}

	.panels {
		float: left;
		width: 33%;
		padding: 0 1em 0 0;
		box-sizing: border-box;
	}

	h2 {
		font-size: 2em;
		font-weight: 200;
		user-select: none;
	}

	label {
		top: 0;
		left: 0;
		display: block;
		font-size: 1em;
		line-height: 1;
		padding: 0.5em;
		margin: 0 auto 0.5em auto;
		border-radius: 2px;
		background-color: white;
		user-select: none;
	}

	input { margin: 0 }

	.panels .activeConcluido {
		background-color: rgb(180,240,100);
	}

	.panels .activeAndamento{
		background-color: #99ebff;
	}

	button {
		float:right;
		font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  		font-weight: bolder; 
		height: 1em;
		box-sizing: border-box;
		padding: 0 0.5em;
		line-height: 1;
		border: none;
		color: rgb(170,30,30);
		opacity: 0;
		transition: opacity 0.2s;
	}

	label:hover button {
		opacity: 1;
	}
</style>

<div class='main'>
<h2>T-Working</h2>

<div class='board'>
	<input
		class="new-todo"
		placeholder="Adicionar nova tarefa..."
		on:keydown="{event => event.which === 13 && add(event.target)}"
	>
	<div class='panels'>
		<h2>A fazer</h2>
		{#each todos.filter(t => t.state == 0) as todo (todo.id)}
			<label
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
				animate:flip
			>	
				{todo.description}
				<button class='proximo' on:click="{() => alterar(todo.id, 1)}">&raquo;</button>
			</label>
		{/each}
	</div>
	<div class='panels'>
		<h2>Em andamento</h2>
		{#each todos.filter(t => t.state == 1) as todo (todo.id)}
			<label
				class='activeAndamento'
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
				animate:flip
			>
				{todo.description}				
				<button class='proximo' on:click="{() => alterar(todo.id, 2)}">&raquo;</button>
				<button class='anterior' on:click="{() => alterar(todo.id, 0)}">&laquo;</button>
			</label>
		{/each}
	</div>

	<div class='panels'>
		<h2>Concluido</h2>
		{#each todos.filter(t => t.state == 2) as todo (todo.id)}
			<label
				class='activeConcluido'
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
				animate:flip
			>
				{todo.description}
				<button class='anterior' on:click="{() => alterar(todo.id, 1)}">&laquo;</button>
			</label>
		{/each}
	</div>
</div>
</div>