<script lang="ts" module>
  type Divider = {
    divider: true,
    marginTop?: string,
    marginBottom?: string,
    marginRight?: string,
    marginLeft?: string
  }

  type Item = {
    name: string | number,
    label: string,
    icon: string,
    disabled?: boolean,
    divider?: undefined | false
  }

  export type MenuItem = Divider | Item;
</script>

<script lang="ts">
  import '../../../css/main.css'
  import './SelectableMenuList.css'
  import Icon from "$lib/components/simple/media/Icon.svelte";

  interface Props {
    collapsed?: boolean;
    items?: MenuItem[];
    selected?: string | number;
    onselect?: (event: {
      detail: {
        item: Item
      }
    }) => void
  }

  let {
    collapsed = $bindable(false),
    items = [],
    selected = $bindable(undefined),
    onselect,
  }: Props = $props();

  function handleItemClick(item: Item) {
    if(item.disabled) return

    selected = item.name
    if(onselect) {
      onselect({
        detail: {
          item
        }
      })
    }
  }
</script>

<div
  class="menu-container"
  class:collapsed={collapsed}
>
  <ul>
    {#each items as item}
      {#if !item.divider }
        <div
          role="button"
          tabindex="0"
          onclick={() => { if(!item.divider) handleItemClick(item) }}
          onkeypress={() => { if(!item.divider) handleItemClick(item) }}
        >
          <li
            class="menu-row"
            aria-label={item.label}
            class:selected={!!selected && item.name === selected}
            class:disabled={item.disabled}
          >
            <div class="menu-icon">
              <Icon 
                name={item.icon}
                --icon-size="1.5rem"
              ></Icon>
            </div>
            {#if !collapsed}
              <div class="menu-name">{item.label}</div>
            {/if}
          </li>
        </div>
      {:else}
        <div
          style:margin-top={item.marginTop}
          style:margin-bottom={item.marginBottom}
          style:margin-right={item.marginRight}
          style:margin-left={item.marginLeft}
          class="divider"
        ></div>
      {/if}
    {/each}
  </ul>
</div>

<style>
  ul {
    list-style: none;
    padding-left: 0rem;
    position: relative;
    line-height: 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: var(
      --selectable-menu-list-spacing,
      var(--selectable-menu-list-default-spacing)
    );
    margin-block-start: var(
      --selectable-menu-list-margin-block-start,
      var(--selectable-menu-list-default-margin-block-start)
    );
    margin-block-end: var(
      --selectable-menu-list-margin-block-end,
      var(--selectable-menu-list-default-margin-block-end)
    );
  }

  .menu-row {
    display: flex;
    gap: var(
      --selectable-menu-list-icon-gap,
      var(--selectable-menu-list-default-icon-gap)
    );
    cursor: pointer;
    padding-left: .8rem;
  }

  .menu-name {
    white-space: nowrap;
  }

  .menu-row.disabled {
    opacity: 50%;
    cursor: not-allowed;
  }

  .menu-row.selected {
    color: var(
      --selectable-menu-list-selected-color,
      var(--selectable-menu-list-default-selected-color)
    )
  }

  .divider {
    height: var(
      --selectable-menu-list-divider-height,
      var(--selectable-menu-list-default-divider-height)
    );
    background-color: var(
      --selectable-menu-list-divider-background-color,
      var(--selectable-menu-list-default-divider-background-color)
    );
  }
</style>