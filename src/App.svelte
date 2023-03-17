<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),
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

  let tasks = [];
  let inputTask = "";

  const onAddClick = () => {
    tasks = [{id: Date.now(), name:inputTask, status: false}, ...tasks];
    inputTask = "";
  };

  const onInputChange = (e) => {
    inputTask = e.target.value;
  };
  function remove(todo) {
		tasks = tasks.filter(t => t.name !== todo.name);
	}
  const onCheckboxClicked = (task, status) => {
	task.status = status;
	remove(task);
	tasks = tasks.concat(task);
	
  };
  console.log(tasks)
</script>

<main>
  <h1>To Do List:</h1>
  <input type="text" on:input={onInputChange} value={inputTask} />
  <button on:click={onAddClick}>Add</button>
  {#if tasks.length} 
  <div class="container">
    <div class="left">
		<h3>TO DO: </h3>
		<ul>
		  {#each  tasks.filter((t)=>!t.status) as task}
			<label in:receive={{ key: task.name }} out:send={{ key: task.name }}>
				<input type=checkbox  on:change = {(e)=>{onCheckboxClicked(task, true)}}/>
				{task.name}
			</label>
		  {/each}
		</ul>
	  </div>
	  <div class="right">
		<h3>DONE: </h3>
		  <ul>
			  {#each tasks.filter((t)=>t.status) as task}
				<label in:receive={{ key: task.name }} out:send={{ key: task.name }}>
					<input type=checkbox checked on:change = {(e)=> {onCheckboxClicked(task, false)}}/>
					{task.name}
				</label>
			  {/each}
			</ul>
	  </div>
  </div>
  {:else}
  <p>There is no task to show!</p>
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
  .container {
    display: flex;
    justify-content: space-around;
    padding: 0px 24%;
  }
  label {
		position: relative;
		line-height: 1.2;
		padding: 0.5em 2.5em 0.5em 2em;
		margin: 0 0 0.5em 0;
		border-radius: 2px;
		user-select: none;
		border: 1px solid hsl(240, 8%, 70%);
		background-color:hsl(240, 8%, 93%);
		color: #333;
	}
	input[type="checkbox"] {
		position: absolute;
		left: 0.5em;
		top: 0.6em;
		margin: 0;
	}
</style>
