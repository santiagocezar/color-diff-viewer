<script lang="ts">
    import type { Color } from "$lib";
    import { T, useThrelte } from "@threlte/core";
    import * as THREE from "three";
    import { Edges, Gizmo, OrbitControls } from "@threlte/extras";
    import { rgb2oklab, oklab2rgb } from "colorizr";

    interface Props {
        closeness: number;
        color: Color;
    }

    const { closeness, color }: Props = $props();

    const { invalidate } = useThrelte();

    let geoRef = $state<THREE.SphereGeometry>();

    const geo = $derived.by(() => {
        if (!geoRef) return;

        const pos = geoRef.getAttribute("position");
        const count = pos.count;
        const col = new THREE.BufferAttribute(new Float32Array(count * 3), 3);
        geoRef.setAttribute("color", col);

        return { pos, count, col };
    });

    const oklab = $derived(rgb2oklab(color));

    const clamp = THREE.MathUtils.clamp;

    $effect(() => {
        if (!geo) return;

        const { pos, count, col } = geo;

        for (let i = 0; i < count; i++) {
            const { r, g, b } = oklab2rgb({
                l: clamp(oklab.l + pos.getX(i) * closeness, 0, 1),
                a: clamp(oklab.a + pos.getY(i) * closeness, -1, 1),
                b: clamp(oklab.b + pos.getZ(i) * closeness, -1, 1),
            });

            col.setXYZ(i, r / 256, g / 256, b / 256);
        }

        col.needsUpdate = true;
        invalidate();
    });
</script>

<!-- <T.Mesh position={[oklab.l, oklab.a, oklab.b]}> -->
<T.Mesh renderOrder={5} scale={closeness}>
    <T.SphereGeometry args={[1]} bind:ref={geoRef} />
    <T.MeshBasicMaterial depthTest={false} vertexColors />
</T.Mesh>
<!-- <T.Mesh position={[oklab.l, oklab.a, oklab.b]}> -->
<T.Mesh renderOrder={10}>
    <T.SphereGeometry args={[0.01]} />
    <T.MeshBasicMaterial
        depthTest={false}
        color={[color[0] / 256, color[1] / 256, color[2] / 256]}
    />
</T.Mesh>
<T.Mesh scale={closeness}>
    <T.BoxGeometry args={[2, 2, 2]} />
    <T.MeshBasicMaterial transparent={true} opacity={0} />
    <Edges color={"black"} />
</T.Mesh>
<!-- <T.Mesh position.y={rad}>
    <T.ConeGeometry args={[rad, rad]} />
    <T.MeshBasicMaterial transparent={true} opacity={0} />
    <Edges color={"yellow"} />
</T.Mesh> -->
<!-- 
<T.Mesh>
    <T.BoxGeometry />
</T.Mesh>
 -->

<T.LineSegments>
    <T.LineBasicMaterial color="#888888" transparent opacity={0.5} />
</T.LineSegments>

<T.PerspectiveCamera makeDefault position={[1, 0.5, 1]}>
    <OrbitControls enablePan={false} enableDamping enableZoom>
        <Gizmo placement="top-center" offset={{ top: 100 }} />
    </OrbitControls>
</T.PerspectiveCamera>
