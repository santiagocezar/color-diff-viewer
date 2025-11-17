<script lang="ts">
    import { Checkbox, Pane, ThemeUtils, Slider } from "svelte-tweakpane-ui";

    interface Props {
        closeness: number;
    }

    let { closeness = $bindable() }: Props = $props();
</script>

<Pane theme={ThemeUtils.presets.light} position="fixed" title="Modelo">
    <Slider
        bind:value={closeness}
        min={0}
        max={1}
        step={0.01}
        label="Distancia máxima"
    />
</Pane>
