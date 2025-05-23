<script>
  import { getContext } from 'svelte';
  import { setContent, toISOString } from '#lib';
  import { Section, Body, Day, Week } from '../time-grid/index.js';
  import Label from './Label.svelte';

  let {
    datesAboveResources,
    _viewDates,
    _viewResources,
    _intlDayHeader,
    _intlDayHeaderAL,
    allDaySlot,
    theme
  } = getContext('state');

  let loops = $derived(
    $datesAboveResources
      ? [$_viewDates, $_viewResources]
      : [$_viewResources, $_viewDates]
  );

  let resourceLabels = $state([]);
</script>

<style>
  .cm-row {
    display: flex;
    text-align: center;
    font-weight: bold;
    background: white;
    border-bottom: 1px solid var(--ec-border-color);
  }

  .cm-cell {
    flex: 1;
    padding: 4px 0;
    border-right: 1px solid var(--ec-border-color);
  }

  .cm-cell:last-child {
    border-right: none;
  }

  .dea-row {
    display: flex;
    border-bottom: 1px solid var(--ec-border-color);
  }

  .dea-pair {
    display: flex;
    flex: 1;
  }

  .dea-cell {
    flex: 1;
    text-align: center;
    padding: 4px 0;
    border-right: 1px solid var(--ec-border-color);
  }

  .dea-cell:last-child {
    border-right: none;
  }
</style>


<div class="{$theme.header}">
  <Section>
    <!-- Ligne de jours -->
    <div class="{$theme.resource}">
      <div class="{$theme.days}">
        {#each $_viewDates as date}
          <div class="{$theme.day} {$theme.weekdays?.[date.getUTCDay()]}" style="text-align: center;">
            <time
              datetime="{toISOString(date, 10)}"
              aria-label="{$_intlDayHeaderAL.format(date)}"
              use:setContent={$_intlDayHeader.format(date)}
            ></time>
          </div>
        {/each}
      </div>

      <!-- Ligne CM -->
      <div class="cm-row">
        {#each $_viewDates as _}
          <div class="cm-cell">CM</div>
        {/each}
      </div>

<!-- Ligne DEA1 + DEA2 avec séparation -->
<div class="dea-row">
  {#each $_viewDates as date}
    <div class="dea-pair">
      <div class="dea-cell">
        <Label resource={$_viewResources.find(r => r.id === 'DEA1')} date={date} />
      </div>
      <div class="dea-cell">
        <Label resource={$_viewResources.find(r => r.id === 'DEA2')} date={date} />
      </div>
    </div>
  {/each}
</div>
    </div>
  </Section>

  <div class="{$theme.hiddenScroll}"></div>
</div>

{#if $allDaySlot}
  <div class="{$theme.allDay}">
    <div class="{$theme.content}">
      <Section>
        {#if $datesAboveResources}
          {#each $_viewDates as date}
            <div class="{$theme.resource}">
              {#each $_viewResources as resource}
                <Week dates={[date]} {resource} />
              {/each}
            </div>
          {/each}
        {:else}
          {#each $_viewResources as resource}
            <div class="{$theme.resource}">
              <Week dates={$_viewDates} {resource} />
            </div>
          {/each}
        {/if}
      </Section>
      <div class="{$theme.hiddenScroll}"></div>
    </div>
  </div>
{/if}

<Body>
  {#each loops[0] as item0}
    <div class="{$theme.resource}">
      {#each loops[1] as item1}
        <Day
          date={$datesAboveResources ? item0 : item1}
          resource={$datesAboveResources ? item1 : item0}
        />
      {/each}
    </div>
  {/each}
</Body>
