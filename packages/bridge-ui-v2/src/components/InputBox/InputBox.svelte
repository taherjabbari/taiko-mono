<script lang="ts">
  import { classNames } from '$libs/util/classNames';

  export let error = false;

  let input: HTMLInputElement;

  let classes = classNames(
    'w-full input-box placeholder:text-tertiary-content bg-neutral-background border-0 shadow-none outline-none font-bold text-2xl',
    $$props.class,
  );

  // Public API
  export const getValue = () => input.value;
  export const setValue = (value: string) => (input.value = value);
  export const clear = () => setValue('');
  export const focus = () => input.focus();
</script>

<input bind:this={input} {...$$restProps} class={classes} class:error on:input on:blur />
