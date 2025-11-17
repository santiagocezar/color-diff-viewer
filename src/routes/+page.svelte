<script lang="ts">
    import { Canvas } from "@threlte/core";
    import Scene from "./Scene.svelte";
    import type { Color } from "$lib";
    import ColorDials from "$lib/ColorDials.svelte";
    import { rgb2oklab } from "colorizr";
    import * as THREE from "three";
    import Settings from "./Settings.svelte";

    let color: Color = $state([128, 128, 128]);

    let closeness: number = $state(0.03);

    const oklab = $derived(rgb2oklab(color));
</script>

<div class="h-dvh relative">
    <div class="absolute w-64 bottom-2 right-2">
        <ColorDials bind:color />
    </div>

    <Settings bind:closeness />

    <Canvas toneMapping={THREE.NoToneMapping}>
        <Scene {closeness} {color} />
    </Canvas>
</div>

<div class="">
    {JSON.stringify(oklab)}
</div>
