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
        border-bottom: 1px solid var(--ec-border-color);
    }

    .cm-cell {
        
        flex: 1;
        width: calc(100% / var(--count));

        text-align: center;
        padding: 4px 0;
        border-right: 1px solid var(--ec-border-color);
        border-left: 1px solid var(--ec-border-color);
    }
    .cm-pair{
        display: flex;
        flex: 1;
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
        border-left: 1px solid var(--ec-border-color);
    }

    .dea-cell:last-child {
        border-right: none;
    }

    .tp-row {
        display: flex;
        border-bottom: 1px solid var(--ec-border-color);
    }

    .tp-pair {
        display: flex;
        flex: 1;
    }

    .tp-cell {
        flex: 1;
        text-align: center;
        padding: 4px 0;
        border-right: 1px solid var(--ec-border-color);
        border-left: 1px solid var(--ec-border-color);
    }

    .tp-cell:last-child {
        border-right: none;
    }
</style>

<div class="{$theme.header}">
    <Section>
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
        <div class="cm-row" style={`--count: ${$_viewDates.length};`}>
            {#each $_viewDates as date}
                <div class="cm-pair">
                    {#each $_viewResources.filter(r => r.extendedProps && r.extendedProps.level === '1') as cmResource}
                        <div class="cm-cell">
                            <Label resource={cmResource} date={date} />
                        </div>
                    {/each}
                </div>
            {/each}
        </div>
        

            <div class="dea-row">
              {#each $_viewDates as date}
                <div class="dea-pair">
                  
                    {#each $_viewResources.filter(r => r.extendedProps && r.extendedProps.level === '2') as resource}

                    <div class="dea-cell">
                      <Label resource={resource} date={date} />
                    </div>
                  {/each}
                </div>
              {/each}
            </div>

            <div class="tp-row">
                {#each $_viewDates as date}
                    <div class="tp-pair">
                        {#each $_viewResources.filter(r => r.extendedProps && r.extendedProps.level === '3') as tpResource}
                            <div class="tp-cell">
                                <Label resource={tpResource} date={date} />
                            </div>
                        {/each}
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