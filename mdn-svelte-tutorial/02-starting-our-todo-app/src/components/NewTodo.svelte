<script>
  import { onMount } from 'svelte';
  import { createEventDispatcher } from 'svelte';
  import { selectOnFocus } from '../actions';
  const dispatch = createEventDispatcher();

  export let autofocus = false;

  let name = '';
  let nameEl; // reference to the name input element

  const addTodo = () => {
    dispatch('addTodo', name);
    name = '';
    nameEl.focus();
  }

  const onCancel = () => {
    name = '';
    nameEl.focus();
  }

  onMount( () => {
    if (autofocus) {
        nameEl.focus();
    }
  });

</script>

<form on:submit|preventDefault={addTodo} on:keydown={e => e.key === 'Escape' && onCancel()}>
  <h2 class="label-wrapper">
    <label for="todo-0" class="label__lg">What needs to be done?</label>
  </h2>
  <input use:selectOnFocus bind:value={name} bind:this={nameEl} type="text" id="todo-0" autoComplete="off" class="input input__lg" />
  <button type="submit" disabled={!name} class="btn btn__primary btn__lg">Add</button>
</form>
