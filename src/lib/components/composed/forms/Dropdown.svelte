<script lang="ts" generics="Data">
  import lodash from "lodash";
  import Autocomplete, { type Item } from "../../../components/simple/forms/Autocomplete.svelte";
  import Button from '../../simple/buttons/Button.svelte'
  import Icon from '../../simple/media/Icon.svelte'
  import type { ComponentProps, Snippet } from "svelte";

  type ItemData = Item<Data>
  interface Props {
    items?: ItemData[];
    values?: ItemData[];
    multiple?: boolean;
    lang?: 'it' | 'en';
    searchText?: string;
    maxVisibleChips?: number;
    placeholder?: string;
    clearable?: boolean;
    mandatory?: boolean;
    icon?: string;
    menuOpened?: boolean;
    openingId?: string;
    width?: string;
    minWidth?: string;
    menuWidth?: string;
    mobileDrawer?: boolean;
    disabled?: boolean;
    onchange?: (event: {
      detail: {
        unselect: ItemData | undefined;
        select: ItemData | undefined;
        selection: ItemData[];
      }
    }) => void
    itemLabelSnippet?: ComponentProps<typeof Autocomplete<Data>>['itemLabelSnippet']
    labelSnippet?: Snippet<[{
      values: typeof values,
      items: typeof items,
      searchText: typeof searchText,
      generatedLabel: typeof generatedLabel,
      placeholder: typeof placeholder,
      clearable: typeof clearable,
      handleCloseClick: typeof handleCloseClick,
    }]>
  }

  let {
    items = [],
    values = $bindable([]),
    multiple = false,
    lang = 'en',
    searchText = $bindable(),
    maxVisibleChips,
    placeholder = lang === 'en' ? "Select" : "Seleziona",
    clearable = true,
    mandatory = true,
    icon,
    menuOpened = $bindable(false),
    openingId = $bindable("autocomplete-menu"),
    width,
    minWidth,
    menuWidth = width,
    mobileDrawer = false,
    disabled = false,
    onchange,
    itemLabelSnippet,
    labelSnippet,
  }: Props = $props();

  let generatedLabel = $derived(values.length == 1 ? values[0].label : `${values.length} Selezionati`)

  function handleCloseClick(event: Parameters<NonNullable<ComponentProps<typeof Icon>['onclick']>>[0]) {
    if(event){
      event.preventDefault()
      event.stopPropagation()
    }
    let valuesBefore = lodash.cloneDeep(values)
    values = []

    if(onchange){
      onchange({
        detail: {
          unselect: valuesBefore[0],
          select: undefined,
          selection: []
        }
      })
    }
  }
</script>

<Autocomplete
  {items}
  bind:values
  bind:searchText
  {multiple}
  {maxVisibleChips}
  {mandatory}
  {disabled}
  searchFunction={() => true}
  {onchange}
  bind:menuOpened
  bind:openingId
  {width}
  {mobileDrawer}
  {minWidth}
  {menuWidth}
  {itemLabelSnippet}
>
  {#snippet selectionContainerSnippet({ openMenu, handleKeyDown })}
    <Button
      --button-default-background-color="transparent"
      --button-default-focus-background-color="rgb(var(--global-color-primary-400), .3)"
      --button-default-focus-color="rgb(var(--global-color-contrast-900))"
      --button-default-border="2px solid rgb(var(--global-color-primary-400))"
      --button-default-color="rgb(var(--global-color-contrast-800))"
      onclick={openMenu}
      onkeydown={(event) => {
        handleKeyDown(event.detail.nativeEvent)
        if(event.detail.nativeEvent.key == 'ArrowDown' || event.detail.nativeEvent.key == 'ArrowUp') {
          event.detail.nativeEvent.stopPropagation()
          event.detail.nativeEvent.preventDefault()
        }
      }}
    >
      {#if labelSnippet}
        {@render labelSnippet({ 
          values,
          items,
          searchText,
          generatedLabel,
          placeholder,
          clearable,
          handleCloseClick,
        })}
      {:else}
        <div class="label">
          {#if !!icon}
            <Icon name={icon}></Icon>
          {/if}
          {#if values.length == 0}
            <div class="space-between">
              <div>{placeholder}</div>
              <Icon name="mdi-chevron-down"></Icon>
            </div>
          {:else}
            <div class="space-between">
              <div>{generatedLabel}</div>
              {#if clearable}
                <Icon
                  name="mdi-close"
                  onclick={handleCloseClick}
                ></Icon>
              {/if}
            </div>
          {/if}
        </div>
      {/if}
    </Button>
  {/snippet}
</Autocomplete>

<style>
  .label {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    gap: 10px;
    width: 100%;
    text-overflow: ellipsis;
    white-space: pre;
  }

  .space-between {
    flex-grow: 1;
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 10px;
  }
</style>