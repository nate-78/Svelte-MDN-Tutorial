<script>
  import FilterButton from './FilterButton.svelte';
  import Todo from './Todo.svelte';
  import MoreActions from './MoreActions.svelte';
  import NewTodo from './NewTodo.svelte';
  import TodosStatus from './TodosStatus.svelte';
  import { alert } from '../stores';


  export let todos = [];

  let newTodoId;
  $: {
    if (todos.length === 0) {
        newTodoId = 1;
    } else {
        // get highest ID and add one to it
        newTodoId = Math.max(...todos.map(t => t.id)) + 1;
    }
  };

  function removeTodo(todo) {
    todos = todos.filter(t => t.id !== todo.id);
    todosStatus.focus();
    $alert = `Todo '${name}' has been added`;
  }

  function addTodo(name) {
      todos = [...todos, { id: newTodoId, name, completed: false }];
      $alert = `Todo '${name}' has been added`;
  }

  function updateTodo(todo) {
    const i = todos.findIndex(t => t.id === todo.id);
    if (todos[i].name !== todo.name) {
      $alert = `todo '${todos[i].name}' has been renamed to '${todo.name}'`;
    }
    if (todos[i].completed !== todo.completed) {
      $alert = `todo '${todos[i].name}' marked as ${todo.completed ? 'completed' : 'active'}`;
    }
    todos[i] = { ...todos[i], ...todo };
  }

  // create filter 
  let filter = 'all';
  $: {
    if (filter === 'all')               $alert = 'Browsing all todos'
    else if (filter === 'active')       $alert = 'Browsing active todos'
    else if (filter === 'completed')    $alert = 'Browsing completed todos'
  }
  const filterTodos = (filter, todos) =>
    filter === 'active' ? todos.filter(t => !t.completed) :
    filter === 'completed' ? todos.filter(t => t.completed) :
    todos;

  
  // general ToDo management
  const checkAllTodos = (completed) => {
    todos = todos.map(t => {
      return {...t, completed: completed};
    });
    $alert = `${completed ? 'Checked' : 'Unchecked'} ${todos.length} todos`;
  } // todos.forEach(t => t.completed = completed)

  const removeCompletedTodos = () => {
    todos = todos.filter(t => !t.completed);
    $alert = `Removed ${todos.filter(t => t.completed).length} todos`;
  }

  let todosStatus;

</script>


<!-- Todos.svelte -->
<div class="todoapp stack-large">

  <!-- NewTodo -->
  <NewTodo autofocus on:addTodo={e => addTodo(e.detail)} />

  <!-- Filter -->
  <FilterButton bind:filter={filter} />


  <!-- TodosStatus -->
  <TodosStatus bind:this={todosStatus} todos={todos} />

  <!-- Todos -->

  <!-- Todos -->
<ul role="list" class="todo-list stack-large" aria-labelledby="list-heading">
  {#each filterTodos(filter, todos) as todo (todo.id)}
    <li class="todo">
      <Todo {todo} 
        on:update={ e => updateTodo(e.detail) }
        on:remove={ e => removeTodo(e.detail) } 
      />
    </li>
  {:else}
    <li>Nothing to do here!</li>
  {/each}
</ul>


  <hr />

  <!-- MoreActions -->
  <MoreActions todos={todos}
    on:checkAll={e => checkAllTodos(e.detail)}
    on:removeCompleted={removeCompletedTodos}
  />

</div>