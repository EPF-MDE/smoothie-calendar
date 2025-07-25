<script>
    import {getContext} from 'svelte';
    import {setContent, toISOString} from '#lib';
    import {Section, Body, Day, Week} from '../time-grid/index.js';
    import Label from './Label.svelte';

    let {
        _viewDates, _viewResources, _intlDayHeader, _intlDayHeaderAL, theme
    } = getContext('state');

    let resourceLabels = $state([]);
</script>

<style>
    .trtg-row {
        display: flex;
        border-bottom: 1px solid var(--ec-border-color);
    }
    .trtg-resource {
        flex: 1;
        width: calc(100% / var(--count));

        text-align: center;
        padding: 4px 0;
        border-right: 1px solid var(--ec-border-color);
        border-left: 1px solid var(--ec-border-color);
    }

    .trtg-resource:last-child {
        border-right: none;
    }

    .trtg-date{
        display: flex;
        flex: 1;
    }

    .ec-resource {
        position: relative;
        height: 100%;
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
        {#each { length: 10 }, depth} <!--TODO: Abitrary depth... Change for the resource tree depth -->
         <div class="trtg-row">

            {#each $_viewDates as date}
                <div class="trtg-date">
                    {#each $_viewResources.filter(r => r.extendedProps && r.extendedProps.level == depth ) as resource}
                        <div class="trtg-resource" id="resource-{resource.title}" style="{resource.extendedProps.is_shown == 1 ? '':'visibility:hidden;height:0px;padding:0px'}">
                            <Label resource={resource} date={date} />
                        </div>
                    {/each}
                </div>
            {/each}

         </div>
        {/each}
        
        </div>
    </Section>

    <div class="{$theme.hiddenScroll}"></div>
</div>
<Body>
    {#each $_viewDates as date}
        <div class="{$theme.resource}">
            {#each $_viewResources as resource}
            <div id="column-{resource.title}" class="{(resource.extendedProps.show_column == 1)?$theme.resource+' shown':'stacked'}">
                <Day
                    date={date}
                    resource={resource}
                />
            </div>
            {/each}
        </div>
    {/each}
</Body>
