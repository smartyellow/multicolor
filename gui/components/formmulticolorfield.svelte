<script>
  import { createEventDispatcher } from 'svelte';

  export let specs = {};
  export let value = specs.multiple ? [] : '';
  export let readonly = false;
  export let error = false;
  export let langlabel = false;
  export let language = 'en';
  export let translate = s => s;

  const dispatch = createEventDispatcher();
  const defaultColors = {
    green: 'green',
    blue: 'blue',
    grey: 'grey',
    black: 'black',
    red: 'red',
    orange: 'orange',
    brown: 'brown',
  };

  if (specs.multiple && !Array.isArray(value)) {
    value = value ? [ value ] : [];
  }
  else if (!value) {
    value = '';
  }

  $: {
    if (specs.extendDefault) {
      specs.options = { ...defaultColors, ...specs.options };
    }

    if (!specs.options) {
      specs.options = { ...defaultColors };
    }

    if (!specs.padding) {
      specs.padding = 15;
    }
  }

  function select(name) {
    if (specs.multiple) {
      if (value.includes(name)) {
        value.splice(value.indexOf(name), 1);
        value = value;
      }
      else {
        value.push(name);
        value = value;
      }
    }
    else {
      value = name;
    }

    dispatch('changeValue', value);
  }
</script>

{#if specs.label}
  <span class:alignright={specs.labelPosition == 'right'}>
    {translate(specs.label, language)}
  </span>
{/if}

{#if langlabel}
  <div class="lang">{langlabel}</div>
{/if}

<div class="colors" style="--padding: {specs.padding}px;">
  {#each Object.entries(specs.options) as [ name, color ]}
    <div
      class="option"
      class:selected={
        (!specs.multiple && value === name) ||
        (specs.multiple && value.includes(name))
      }
      style="--color: {color};"
      on:click={() => select(name)}
    />
  {/each}
</div>

{#if error}
  <span class="error">{error}</span>
{/if}

{#if readonly}
  <span class="readonly" />
{/if}

<style>
  .colors {
    line-height: 1;
  }

  .colors .option {
    display: inline-block;
    position: relative;
    padding: var(--padding);
    background: var(--color);
    margin-right: 0.3rem;
    min-width: 0.75rem;
    min-height: 0.75rem;
    border-radius: 4px;
    cursor: pointer;
  }

  .colors .option.selected::after {
    content: '';
    position: absolute;
    top: 0.8rem;
    left: 0.8rem;
    width: 1rem;
    height: 1rem;
    background-image: url('data:image/svg+xml,%3Csvg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" viewBox="0 0 405.27 405.27"%3E%3Cpath fill="white" d="M393.4 124.42 179.6 338.21a40.57 40.57 0 0 1-57.36 0L11.88 227.84a40.56 40.56 0 0 1 57.35-57.37l81.7 81.7 185.1-185.1a40.57 40.57 0 0 1 57.37 57.36z"/%3E%3C/svg%3E');
  }
</style>
