# WaveStudio TypeScript Tutorial

This tutorial teaches WaveStudio from the TypeScript API surface declared in
[`waveStudio-globals.d.ts`](../waveStudio-globals.d.ts). That file is generated
for Monaco IntelliSense, so it is the best local map of what the WaveStudio code
editor knows about: scene globals, engine APIs, object classes, components,
assets, physics, input, UI, and runtime data.

The live editor is available at:

https://wave3d.ai/waveStudio/

## Choose Your Creator Track

WaveStudio is broad enough that learning it front-to-back can feel like reading
an engine manual. A better way is to pick the kind of world you want to build,
then follow the lessons and boss projects that feed that style.

| Track | You learn to build | Start with | Boss projects to steal from |
| --- | --- | --- | --- |
| Artist Track | Shapes, materials, lights, decals, and mesh painting. | Lessons 2, 3, 5, 10 | Geometry Surgery, Particle Weather Machine |
| Animator Track | Fluent chains, timelines, paths, and completion callbacks. | Lessons 4, 7, 12 | Draw-to-World Magic Ink, Robot Arm Puzzle |
| Game Track | Keyboard control, click handlers, HUDs, cameras, and scoring. | Lessons 8, 11, 13, 16 | Keyboard Driving Lab, Recursive Click Explosions |
| Physics Track | Collisions, rigid bodies, joints, force fields, and destruction. | Lessons 9, 14, 17 | Tiny Solar System, Physics Piano |
| Worldbuilding Track | Terrain, roads, cities, atmosphere, and performance views. | Lessons 10, 11, 12, 19 | Terraforming Spellbook |
| Human Interface Track | Voice commands, webcam hands, microphone events, and permissions. | Lessons 8, 13, 14 | Voice/Gesture Character Controller |

You can jump between tracks at any time. The same object might be a painted
sculpture in the Artist Track, a clickable game target in the Game Track, and a
dynamic rigid body in the Physics Track.

## Learning Path

The tutorial is organized as a 20-lesson path. Each lesson should either teach a
new primitive idea, combine earlier ideas into a pattern, or point you toward a
larger project.

| Part | Lessons | Purpose |
| --- | --- | --- |
| Foundations | 1-4 | Active scene, built-in shapes, shape choices, transforms. |
| Visual language | 5-6 | Materials, textures, model assets, props, and staged objects. |
| Behavior | 7-9 | Animation chains, input, click handlers, physics, and collisions. |
| Scene systems | 10-15 | Camera, lighting, tags, spawning, UI, audio, FX, and saved data. |
| Projects and reference | 16-20 | Mini game, recursive boss project, advanced boss gallery, demo atlas, API catalog. |

When a new example is added, place it in the earliest lesson where the learner
already knows the prerequisites. If it needs too many earlier ideas, it belongs
in a boss project, the demo atlas, or the API catalog instead of the first
lesson that mentions that API.

## What You Are Writing

WaveStudio code runs inside a scene context. In the editor, several globals are
already declared for you:

```text
ctx;          // SceneContext
engine;       // engineLocal, shorthand for ctx.engine
scene;        // WaveScene, shorthand for ctx.scene
myScene;      // alias of scene
myCloud;      // persistent scene data store
assetManager; // WaveAssetManager

models;
textures;
materials;
animations;
audios;
videos;
hdr;
fonts;
particles;
```

Most beginner code uses `scene`, creates entities such as `waveCube` or
`wave3DObject`, and configures them with fluent methods. In the WaveStudio
editor, newly created scene objects are available in the active scene without a
manual add step.

Copy-paste rule: every fenced `ts` or `typescript` block in this tutorial is
intended to be a complete Studio paste unit. If a sample is only a note, a shell
command, a globals list, or an intentionally partial pattern, it should use a
different fence such as `text` or `bash` so learners do not mistake it for a
runnable scene snippet.

```ts
const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.CYAN);
cube.placeAt(0, 1, 0);
```

Teaching rule: prefer natural placement phrases. Use `placeAt` for first
placement, `placeAbove`, `placeInfrontOf`, and `placeAround` for relationships
between objects, and direction verbs such as `moveForward` or `moveUp` for
motion after placement. Save raw coordinate thinking for the rare moments when
you truly need exact numeric control.

### Fluent Dot Chaining

The repeated dots in examples such as `new Cube().placeAt(...).setColor(...)`
are called **method chaining** or a **fluent API**. Each method returns an
object, so the next `.method(...)` can keep working on that returned value.

```ts
const target = new Cube()
  .placeAt(0, 5, 0)
  .setColor(PALETTE.BLUE);
```

That is the same basic setup as writing separate statements:

```ts
const target = new Cube();
target.placeAt(0, 5, 0);
target.setColor(PALETTE.BLUE);
```

The leading dot on a new line belongs to the expression above it. The semicolon
ends the whole chain. This is different from TypeScript's three-dot `...`
spread/rest syntax.

WaveStudio uses chaining in two common ways:

```ts
const target = new Cube()
  .placeAt(0, 5, 0)
  .setColor(PALETTE.BLUE);

target
  .exploding()
  .intoPieces(3)
  .withStrength(2)
  .apply();
```

Use chaining when it reads naturally. If autocomplete becomes confusing, or a
method returns `void`, `null`, or a different builder type than you expected,
split the code into separate statements.

If you are writing in a standalone TypeScript file outside the WaveStudio
editor, add a reference so your editor sees the same declarations:

```text
/// <reference path="./waveStudio-globals.d.ts" />
```

## Lesson 1: The Smallest Scene

A scene is a world full of entities. A scene script usually has a `main`
function. In WaveStudio, you may already be editing inside that runtime context.
If you are defining a reusable scene object, the shape is:

```ts
const tutorialScene = new WaveScene("Tutorial");

tutorialScene.withMain(({ scene }) => {
  const cube = new waveCube(2, 2, 2);
  cube.setColor(PALETTE.CYAN);
  cube.placeAt(0, 1, 0);
});
```

Inside the WaveStudio editor, the shorter version is often enough because
`scene` is a predeclared global:

```ts
const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.CYAN);
cube.placeAt(0, 1, 0);
```

Key idea: create an object and configure it. In Studio snippets, that is enough
for normal objects.

### Scene Starter Gallery

Most useful scenes start with the same four ingredients: a ground or anchor,
one hero object, one visual cue, and one line of feedback. Treat this as the
"hello world" pattern for creative scenes.

| Starter | What it teaches | APIs to notice |
| --- | --- | --- |
| Hero object | Create and style one thing | `new Cube`, `setColor`, `placeAt` |
| Ground plane | Give the scene spatial context | `waveGround`, `setColor` |
| Debug label | Make the object explain itself | `showLabel`, `showName` |
| Scene message | Give the learner feedback | `scene.print` |

```ts
myScene.sky.backgroundColor(PALETTE.WHITE);

const ground = new waveGround(16, 16, 4);
ground.setColor(PALETTE.lighten(PALETTE.SLATE, 24));
ground.placeAt(0, 0, 0);

const hero = new Cube()
  .placeAt(0, 1, 0)
  .setColor(PALETTE.CYAN);

hero.setName("First Hero Cube");
hero.showLabel("click me");

hero.whenClickedOn(() => {
  hero
    .rotateAroundBy(Direction.Up, 45, Animate)
    .over(0.35, Seconds)
    .play();

  scene.print("The cube is already in the active scene.");
});
```

Remix challenges:

- Replace the hero cube with `Sphere`, `Capsule`, or `Prop`.
- Add `showDirection()` and `showPosition()` while you learn transforms.
- Change the sky color when the hero is clicked.
- Turn this into a two-object scene: a player and a goal.

## Lesson 2: Primitives

WaveStudio includes geometry classes for common shapes. Their constructors
usually take dimensions.

```ts
const ground = new waveGround(20, 20, 4);
ground.setColor(PALETTE.CHARCOAL);
ground.placeAt(0, 0, 0);

const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.TURQUOISE);
cube.placeAt(-3, 1, 0);

const sphere = new waveSphere(1, 32);
sphere.setColor(PALETTE.GOLDENROD);
sphere.placeAt(0, 1, 0);

const cylinder = new waveCylinder(2, 1.5, 1.5);
cylinder.setColor(PALETTE.FUCHSIA);
cylinder.placeAt(3, 1, 0);
```

Useful primitive classes from the declarations include:

- `waveCube(width, height, depth)`
- `waveBox(width, height, depth)` as an alias of `waveCube`
- `waveSphere(radius, segments)`
- `waveCylinder(height, diameterTop, diameterBottom)`
- `waveCapsule(radius, height)`
- `waveCone(height, diameter)`
- `waveTorus(radius, tubeRadius)`
- `waveTorusKnot(radius, tubeRadius, tessellation, p, q)`
- `wavePlane(width, height, doubleSided)`
- `waveGround(width, height, subdivisions)`
- `waveDisc(radius, tessellation)`
- `waveRing(outerRadius, innerRadius, tessellation)`
- `wavePolygon(radius, sides)`
- `waveLine(start, end)`
- `waveArc(radius, startAngle, endAngle, segments)`
- `wavePath(points, closed, curveType, segments)`
- `wavePointNet(points, options)`
- `wavePathExtrusion(path, shape, options)`
- `waveTube(radius, tessellation, path)`
- `waveLathe(radius, tessellation, shape, arc)`
- `waveIcoSphere(radius, subdivisions, flat)`
- `waveDome(diameter, arc, segments)`
- `waveWedge(width, height, depth, flipSlope)`
- `wavePrism(sides, radius, height, topRadius)`
- `waveTrapezoid(width, height, depth, topWidth, topDepth)`
- `waveCavity(width, height, depth, options)`
- `waveCavityTopology(options)`

## Lesson 3: Built-In 3D Shapes Gallery

The WaveStudio UI may describe shapes as `Cube`, `Capsule`, `Sphere`, and so
on. In TypeScript, those built-ins use class names such as `waveCube`,
`waveCapsule`, and `waveSphere`.

Use this mapping when translating from the visual idea to code:

WaveStudio also declares natural aliases for many of these classes. For
example, `Cube` means `waveCube`, `Sphere` means `waveSphere`, `Capsule` means
`waveCapsule`, and `Prop` means `waveProp`. The longer `wave...` names are
useful when searching the declaration file; the short aliases are nice for
readable lesson code.

| Shape name | TypeScript class | Example |
| --- | --- | --- |
| Cube | `waveCube` | `new waveCube(1, 1, 1)` |
| Box | `waveBox` | `new waveBox(2, 1, 1)` |
| Sphere | `waveSphere` | `new waveSphere(1, 32)` |
| Capsule | `waveCapsule` | `new waveCapsule(0.5, 2, 24)` |
| Cylinder | `waveCylinder` | `new waveCylinder(2, 1, 1, 32)` |
| Cone | `waveCone` | `new waveCone(2, 1.4, 32)` |
| Torus | `waveTorus` | `new waveTorus(1.2, 0.25, 64)` |
| Torus knot | `waveTorusKnot` | `new waveTorusKnot(1.2, 0.16, 96, 2, 3)` |
| Plane | `wavePlane` | `new wavePlane(2, 2, true)` |
| Ground | `waveGround` | `new waveGround(12, 12, 4)` |
| Disc | `waveDisc` | `new waveDisc(1.2, 48)` |
| Ring | `waveRing` | `new waveRing(1.4, 0.7, 64)` |
| Polygon | `wavePolygon` | `new wavePolygon(1.2, 6)` |
| Line | `waveLine` | `new waveLine({ x: -1, y: 0, z: 0 }, { x: 1, y: 0, z: 0 })` |
| Arc | `waveArc` | `new waveArc(1.2, 0, 180, 32)` |
| Path | `wavePath` | `new wavePath([{ x: -1, y: 0, z: 0 }, { x: 1, y: 0, z: 0 }])` |
| Point net | `wavePointNet` | `new wavePointNet(points)` |
| Path extrusion | `wavePathExtrusion` | `new wavePathExtrusion(path, shape)` |
| Tube | `waveTube` | `new waveTube(0.15, 32)` |
| Lathe | `waveLathe` | `new waveLathe(1, 48)` |
| Ico sphere | `waveIcoSphere` | `new waveIcoSphere(1, 2, false)` |
| Dome | `waveDome` | `new waveDome(2, 0.5, 32)` |
| Wedge | `waveWedge` | `new waveWedge(2, 1, 2)` |
| Prism | `wavePrism` | `new wavePrism(6, 1, 2)` |
| Trapezoid | `waveTrapezoid` | `new waveTrapezoid(2, 1.5, 2, 1, 1)` |
| Cavity | `waveCavity` | `new waveCavity(4, 3, 4)` |
| Cavity topology | `waveCavityTopology` | `new waveCavityTopology()` |

Here is a copyable gallery that lays out many of the built-in shapes in one
scene. It uses a factory list so you can add, remove, or rearrange shapes
without changing the placement loop.

```ts
type ShapeFactory = {
  label: string;
  create: () => waveGeometry;
};

const pathPoints = [
  { x: -0.9, y: 0, z: 0 },
  { x: 0, y: 0.8, z: 0 },
  { x: 0.9, y: 0, z: 0 },
];

const extrusionShape = [
  { x: -0.12, y: -0.12, z: 0 },
  { x: 0.12, y: -0.12, z: 0 },
  { x: 0.12, y: 0.12, z: 0 },
  { x: -0.12, y: 0.12, z: 0 },
];

const shapes: ShapeFactory[] = [
  { label: "Cube", create: () => new waveCube(1, 1, 1) },
  { label: "Box", create: () => new waveBox(1.4, 0.8, 1) },
  { label: "Sphere", create: () => new waveSphere(0.65, 32) },
  { label: "Capsule", create: () => new waveCapsule(0.35, 1.3, 24) },
  { label: "Cylinder", create: () => new waveCylinder(1.2, 0.8, 0.8, 32) },
  { label: "Cone", create: () => new waveCone(1.3, 0.9, 32) },
  { label: "Torus", create: () => new waveTorus(0.7, 0.16, 64) },
  { label: "TorusKnot", create: () => new waveTorusKnot(0.65, 0.12, 96, 2, 3) },
  { label: "Plane", create: () => new wavePlane(1.2, 1.2, true) },
  { label: "Disc", create: () => new waveDisc(0.7, 48) },
  { label: "Ring", create: () => new waveRing(0.75, 0.38, 64) },
  { label: "Polygon", create: () => new wavePolygon(0.75, 6) },
  { label: "Line", create: () => new waveLine({ x: -0.7, y: 0, z: 0 }, { x: 0.7, y: 0, z: 0 }) },
  { label: "Arc", create: () => new waveArc(0.75, 0, 210, 40) },
  { label: "Path", create: () => new wavePath(pathPoints) },
  { label: "PointNet", create: () => new wavePointNet(pathPoints) },
  { label: "Extrusion", create: () => new wavePathExtrusion(pathPoints, extrusionShape) },
  { label: "Tube", create: () => new waveTube(0.12, 24) },
  { label: "Lathe", create: () => new waveLathe(0.7, 48) },
  { label: "IcoSphere", create: () => new waveIcoSphere(0.7, 2, false) },
  { label: "Dome", create: () => new waveDome(1.2, 0.5, 32) },
  { label: "Wedge", create: () => new waveWedge(1.2, 0.8, 1.2) },
  { label: "Prism", create: () => new wavePrism(6, 0.7, 1.2) },
  { label: "Trapezoid", create: () => new waveTrapezoid(1.2, 1, 1.2, 0.7, 0.7) },
  { label: "Cavity", create: () => new waveCavity(1.3, 1, 1.3) },
];

const palette = [
  PALETTE.CYAN,
  PALETTE.GOLDENROD,
  PALETTE.CORAL,
  PALETTE.SAGE,
  PALETTE.COBALT,
];
const columns = 5;
const spacing = 2.6;
const gallerySpinDegreesPerSecond = 20;

for (let i = 0; i < shapes.length; i++) {
  const item = shapes[i];
  const shape = item.create();
  const col = i % columns;
  const row = Math.floor(i / columns);

  shape.setColor(palette[i % palette.length]);
  shape.placeAt((col - 2) * spacing, 1.2, row * spacing);
  shape.addTag("built-in-shape");

  shape.onTick((_self, deltaTime) => {
    shape.turnRight(gallerySpinDegreesPerSecond * deltaTime);
  });

  shape.whenClickedOn(() => {
    scene.print(item.label);
  });
}
```

Notice the pattern: every built-in shape can be created, styled, placed,
animated, and clicked with the same basic workflow in the active scene.

## Lesson 4: Position, Rotation, and Scale

Most 3D objects inherit transform helpers from `wave3DAbstract`.

```ts
const block = new waveCube(1, 1, 1);

block.placeAt(0, 1, 0);          // place it in the scene
block.moveRight(2);             // semantic direction helper
block.moveUp(1);
block.moveForward(3);

block.turnRight(45);            // degrees
block.pitchUp(10);
block.rollLeft(15);

block.setScale(2, 1, 1);
block.setUniformScale(1.5);
block.scaleBy(1.1);
```

For first placement, prefer `placeAt`. It reads like the intention and supports
both a simple `x, y, z` location and a target/reference. After that, use
direction verbs such as `moveForward`, `moveUp`, `placeAbove`, and
`placeInfrontOf` so the code stays spatial and readable.

```ts
const floor = new waveGround(10, 10, 2);
const crate = new waveCube(1, 1, 1);

crate.placeAbove(floor);
crate.moveForward(2);
```

You can also use the lower-level transform component:

```ts
const block = new waveCube(1, 1, 1);

block.transform.setPosition(0, 2, 0);
block.transform.setScale(1, 2, 1);
block.transform.moveAlong(Direction.Up, 1);
```

Tip: many transform methods return a base type such as `wave3DAbstract`. For
clean TypeScript autocomplete, keep the original variable and call methods on
separate lines instead of building one very long chain.

```ts
const cube = new waveCube(2, 2, 2);
cube.placeAt(0, 1, 0);
cube.setColor(PALETTE.WHITE);
cube.useDynamicBody();
```

### Transform Playground Gallery

Transforms are easier to learn when each object demonstrates one movement idea.
Build a tiny playground where the labels explain the operation.

| Pattern | Use it for | APIs |
| --- | --- | --- |
| Scene placement | Put an object in a readable place | `placeAt`, `placeAbove`, `placeInfrontOf` |
| Direction movement | Nudge from the current position | `moveLeft`, `moveUp`, `moveForward` |
| Orientation | Make direction visible | `turnRight`, `pitchUp`, `showDirection` |
| Scale | Resize without changing the mesh class | `setScale`, `setUniformScale`, `enlargeBy` |
| Targeting | Face or align with another object | `turnTo`, `fitBetween`, `placeAbove` |

```ts
const originMarker = new wavePoint().placeAt(0, 0.1, 0);
originMarker.showLabel("origin");

const mover = new Cube().placeAt(-4, 0.5, 0).setColor(PALETTE.CYAN);
mover.showLabel("moveForward");
mover.showDirection();
mover.whenClickedOn(() => {
  mover
    .moveAlong(Direction.Forward, 1, Animate)
    .over(0.4, Seconds)
    .play();
});

const spinner = new Capsule().placeAt(-1.5, 1, 0).setColor(PALETTE.ORANGE);
spinner.showLabel("turnRight");
spinner.showDirection();
spinner.onTick((_self, dt) => spinner.turnRight(70 * dt));

const scaler = new Sphere(0.6).placeAt(1.5, 0.8, 0).setColor(PALETTE.PINK);
scaler.showLabel("scale");
scaler.whenClickedOn(() => {
  scaler.scaleBy(1.2, Animate).over(0.25, Seconds).play();
});

const bridge = new waveCylinder(1, 0.12, 0.12).setColor(PALETTE.GOLD);
bridge.showLabel("fitBetween");
bridge
  .fitBetween()
  .between(mover, scaler)
  .along(Direction.Up)
  .apply();
```

Remix challenges:

- Make the bridge update on tick as the endpoints move.
- Add `showCoordinate()` to compare local and world axes.
- Use `placeAbove(table)` from the still-life example instead of numeric Y.
- Make a transform obstacle course controlled with keyboard input.

## Lesson 5: Materials, Colors, and Textures

Simple colors are the fastest way to style primitives. You can use CSS-style
hex strings:

```ts
const floor = new waveGround(30, 30, 8);
floor.setColor(PALETTE.CHARCOAL);
```

WaveStudio also exposes a typed `PALETTE` constant. This is what you may have
seen as "Palette" in examples:

```ts
const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.CYAN);
cube.placeAt(0, 1, 0);

const accent = new waveSphere(0.6, 32);
accent.setColor(PALETTE.lighten(PALETTE.COBALT, 20));
accent.placeAt(2, 1, 0);
```

Useful palette names include `PALETTE.WHITE`, `PALETTE.BLACK`, `PALETTE.RED`,
`PALETTE.ORANGE`, `PALETTE.YELLOW`, `PALETTE.GREEN`, `PALETTE.BLUE`,
`PALETTE.CYAN`, `PALETTE.PURPLE`, `PALETTE.PINK`, `PALETTE.TEAL`,
`PALETTE.GOLD`, `PALETTE.SILVER`, `PALETTE.WARM_WHITE`, and
`PALETTE.CHARCOAL`. There is also a `COLOR` alias with the same color map.

Objects with visual models also expose material helpers:

```ts
const panel = new waveCube(4, 0.2, 2);
panel.setBaseTexture(textures.milkyway);
panel.setRoughness(0.6);
panel.setMetallic(0.1);
```

If you have named materials in the asset warehouse, use them by name:

```ts
const platform = new waveCube(6, 0.4, 6);
platform.useMaterial(materials.PavingStones);
platform.placeAt(0, 0.2, 0);
```

### Material and Palette Gallery

Use this gallery when you want to compare visual styles without changing the
shape. The important idea is that color, roughness, metallic response, opacity,
and emissive glow are separate controls.

| Style | Good for | Main APIs |
| --- | --- | --- |
| Matte | Clay, plastic, painted props | `setColor`, `setRoughness` |
| Metallic | Coins, machines, sci-fi panels | `setMetallic`, `setRoughness` |
| Transparent | Glassy overlays, ghost objects | `setOpacity` |
| Emissive | Neon, pickups, magic objects | `setEmissiveColor`, `setEmissiveIntensity` |
| Palette derived | Theme variants | `PALETTE.lighten`, `PALETTE.darken` |

```ts
type MaterialStyle = {
  label: string;
  color: number;
  apply: (object: waveCube) => void;
};

const materialStyles: MaterialStyle[] = [
  {
    label: "matte",
    color: PALETTE.SAGE,
    apply: (object) => {
      object.setRoughness(1);
      object.setMetallic(0);
    },
  },
  {
    label: "metal",
    color: PALETTE.SILVER,
    apply: (object) => {
      object.setMetallic(1);
      object.setRoughness(0.25);
    },
  },
  {
    label: "glass",
    color: PALETTE.SKY,
    apply: (object) => {
      object.setOpacity(0.45);
      object.setRoughness(0.05);
    },
  },
  {
    label: "emissive",
    color: PALETTE.FUCHSIA,
    apply: (object) => {
      object.setEmissiveColor(PALETTE.FUCHSIA);
      object.setEmissiveIntensity(1.5);
    },
  },
  {
    label: "palette mix",
    color: PALETTE.lighten(PALETTE.COBALT, 25),
    apply: (object) => {
      object.setRoughness(0.45);
      object.setEmissiveColor(PALETTE.darken(PALETTE.COBALT, 20));
      object.setEmissiveIntensity(0.4);
    },
  },
];

const styleSpacing = 2.2;

for (let i = 0; i < materialStyles.length; i++) {
  const style = materialStyles[i];
  const sample = new waveCube(1.3, 1.3, 1.3);

  sample.setColor(style.color);
  sample.placeAt((i - 2) * styleSpacing, 1, 0);
  style.apply(sample);

  sample.whenClickedOn(() => scene.print(style.label));
}
```

### Material Studio Gallery

The Shape Gallery is useful because it lets learners compare many objects in
one scene. Materials deserve the same treatment. Build a material studio with
three rows: palette swatches, surface presets, and texture/face/decal examples.

#### Row 1: Palette Swatches

This row teaches named colors, derived colors, and the difference between
`PALETTE` / `COLOR` constants and raw hex strings.

```ts
const colorSwatches = [
  { label: "WHITE", color: PALETTE.WHITE },
  { label: "BLACK", color: PALETTE.BLACK },
  { label: "RED", color: PALETTE.RED },
  { label: "ORANGE", color: PALETTE.ORANGE },
  { label: "YELLOW", color: PALETTE.YELLOW },
  { label: "GREEN", color: PALETTE.GREEN },
  { label: "BLUE", color: PALETTE.BLUE },
  { label: "CYAN", color: PALETTE.CYAN },
  { label: "PURPLE", color: PALETTE.PURPLE },
  { label: "PINK", color: PALETTE.PINK },
  { label: "COBALT + LIGHT", color: PALETTE.lighten(PALETTE.COBALT, 25) },
  { label: "CHARCOAL + DARK", color: PALETTE.darken(PALETTE.CHARCOAL, 12) },
];

for (let i = 0; i < colorSwatches.length; i++) {
  const swatch = colorSwatches[i];
  const chip = new waveCube(0.8, 0.25, 0.8);

  chip.setName(`palette-${swatch.label}`);
  chip.setColor(swatch.color);
  chip.placeAt((i - 5.5) * 1.05, 0.5, -5);
  chip.showLabel(swatch.label);
  chip.whenClickedOn(() => scene.print(`PALETTE.${swatch.label}`));
}
```

#### Row 2: Surface Presets

Use `SurfacePreset` when you want a natural-language material recipe instead of
remembering roughness, metallic, opacity, and emissive numbers every time.

```ts
const surfaceSwatches = [
  { label: "Matte", preset: SurfacePreset.Matte, color: PALETTE.SAGE },
  { label: "Satin", preset: SurfacePreset.Satin, color: PALETTE.TEAL },
  { label: "Gloss", preset: SurfacePreset.Gloss, color: PALETTE.CYAN },
  { label: "Plastic", preset: SurfacePreset.Plastic, color: PALETTE.ORANGE },
  { label: "Rubber", preset: SurfacePreset.Rubber, color: PALETTE.CHARCOAL },
  { label: "Metal", preset: SurfacePreset.Metal, color: PALETTE.SILVER },
  { label: "Chrome", preset: SurfacePreset.Chrome, color: PALETTE.WHITE },
  { label: "Glass", preset: SurfacePreset.Glass, color: PALETTE.SKY },
  { label: "Frosted", preset: SurfacePreset.FrostedGlass, color: PALETTE.WARM_WHITE },
  { label: "Toon", preset: SurfacePreset.Toon, color: PALETTE.YELLOW },
  { label: "Emissive", preset: SurfacePreset.Emissive, color: PALETTE.FUCHSIA },
];

for (let i = 0; i < surfaceSwatches.length; i++) {
  const swatch = surfaceSwatches[i];
  const sphere = new waveSphere(0.42, 32);

  sphere.setName(`surface-${swatch.label}`);
  sphere.setColor(swatch.color);
  sphere.setSurfacePreset(swatch.preset);
  sphere.placeAt((i - 5) * 1.2, 1.2, -2.5);
  sphere.showLabel(swatch.label);

  sphere.whenClickedOn(() => {
    scene.print(`SurfacePreset.${swatch.label}`);
  });
}
```

#### Row 3: Textures, Faces, and Decals

Use object materials for the whole mesh, `faceMaterial(...)` for one surface,
and decals when you want to stamp an image on top of a receiver.

```ts
const faceCube = new waveCube(1.8, 1.8, 1.8);
faceCube.placeAt(-4, 1.1, 1.5);
faceCube.showLabel("six face colors");

faceCube.faceMaterial(WaveSurface.Up).setColor(PALETTE.WARM_WHITE);
faceCube.faceMaterial(WaveSurface.Down).setColor(PALETTE.CHARCOAL);
faceCube.faceMaterial(WaveSurface.Forward).setColor(PALETTE.CYAN);
faceCube.faceMaterial(WaveSurface.Backward).setColor(PALETTE.PURPLE);
faceCube.faceMaterial(WaveSurface.Left).setColor(PALETTE.ORANGE);
faceCube.faceMaterial(WaveSurface.Right).setColor(PALETTE.YELLOW);

const textureCube = new waveCube(1.8, 1.8, 1.8);
textureCube.placeAt(0, 1.1, 1.5);
textureCube.showLabel("texture + UV motion");
textureCube.setBaseTexture(textures.milkyway);
textureCube.setRoughness(0.35);
textureCube.setMetallic(0.15);

let uvSpinDegrees = 0;
textureCube.onTick((_self, deltaTime) => {
  uvSpinDegrees += 90 * deltaTime;
  textureCube.faceMaterial(WaveSurface.Forward).setUVRotation(uvSpinDegrees);
});

const decalWall = new waveCube(3.2, 2.2, 0.25);
decalWall.placeAt(4, 1.3, 1.5);
decalWall.setColor(PALETTE.SLATE);
decalWall.setSurfacePreset(SurfacePreset.Matte);
decalWall.showLabel("decal stamp");

const poster = decalWall.addDecal({
  texture: textures.FarmGirlHappy,
  at: { x: 4, y: 1.4, z: 1.65 },
  facing: "front",
  size: { width: 1.6, height: 1.6 },
  opacity: 0.85,
  blend: "multiply",
  tint: PALETTE.PINK,
});

poster.setGlow(0.2);
```

### Composition Project: 3D Glass Bottle

The bottle is a useful bridge between the Shape Gallery and materials: it is
not a new mesh type. It is a stack of cylinders, a flattened sphere, torus
rings, a label cube, and small bubbles with glass and liquid materials.

```ts
myScene.sky.backgroundColor(PALETTE.WHITE);

const glassBody = new waveCylinder(3.1, 1.45, 1.65, 64);
glassBody.placeAt(0, 1.65, 0);
glassBody.setColor(PALETTE.SKY);
glassBody.setSurfacePreset(SurfacePreset.Glass);
glassBody.setOpacity(0.35);
glassBody.setRoughness(0.05);

const puntRing = new waveTorus(0.72, 0.045, 64);
puntRing.placeAt(0, 0.18, 0);
puntRing.setColor(PALETTE.WHITE);
puntRing.setSurfacePreset(SurfacePreset.Glass);
puntRing.setOpacity(0.5);

const shoulder = new waveSphere(1, 48);
shoulder.placeAt(0, 3.25, 0);
shoulder.setScale(0.86, 0.28, 0.86);
shoulder.setColor(PALETTE.SKY);
shoulder.setSurfacePreset(SurfacePreset.Glass);
shoulder.setOpacity(0.32);

const neck = new waveCylinder(1.1, 0.46, 0.62, 48);
neck.placeAt(0, 3.85, 0);
neck.setColor(PALETTE.SKY);
neck.setSurfacePreset(SurfacePreset.Glass);
neck.setOpacity(0.38);

const mouthLip = new waveTorus(0.27, 0.06, 64);
mouthLip.placeAt(0, 4.42, 0);
mouthLip.setColor(PALETTE.WHITE);
mouthLip.setSurfacePreset(SurfacePreset.Glass);
mouthLip.setOpacity(0.65);

const liquid = new waveCylinder(1.85, 1.28, 1.45, 64);
liquid.placeAt(0, 1.08, 0);
liquid.setColor(PALETTE.CYAN);
liquid.setOpacity(0.58);
liquid.setRoughness(0.18);

const liquidSurface = new waveCylinder(0.04, 1.26, 1.26, 64);
liquidSurface.placeAt(0, 2.02, 0);
liquidSurface.setColor(PALETTE.WARM_WHITE);
liquidSurface.setOpacity(0.62);

const label = new waveCube(1.35, 0.78, 0.055);
label.placeAt(0, 1.55, -0.84);
label.setColor(PALETTE.WARM_WHITE);
label.setRoughness(0.75);
label.showLabel("Wave Water");

const labelStripe = new waveCube(1.15, 0.12, 0.065);
labelStripe.placeAt(0, 1.35, -0.88);
labelStripe.setColor(PALETTE.CYAN);

const cap = new waveCylinder(0.55, 0.64, 0.64, 48);
cap.placeAt(0, 4.72, 0);
cap.setColor(PALETTE.COBALT);
cap.setSurfacePreset(SurfacePreset.Gloss);
cap.setRoughness(0.25);

const capTopRing = new waveTorus(0.32, 0.035, 48);
capTopRing.placeAt(0, 5.01, 0);
capTopRing.setColor(PALETTE.SILVER);
capTopRing.setMetallic(0.7);

const capBottomRing = new waveTorus(0.33, 0.03, 48);
capBottomRing.placeAt(0, 4.43, 0);
capBottomRing.setColor(PALETTE.SILVER);
capBottomRing.setMetallic(0.7);

for (let i = 0; i < 12; i++) {
  const angle = i * 30;
  const x = Math.cos(angle * Math.PI / 180) * 0.38;
  const z = Math.sin(angle * Math.PI / 180) * 0.38;
  const y = 0.65 + (i % 6) * 0.25;

  const bubble = new waveSphere(0.045 + (i % 3) * 0.015, 16);
  bubble.placeAt(x, y, z);
  bubble.setColor(PALETTE.WHITE);
  bubble.setSurfacePreset(SurfacePreset.Glass);
  bubble.setOpacity(0.55);

  bubble.onTick((_self, deltaTime) => {
    bubble.moveUp(deltaTime * (0.12 + (i % 4) * 0.02));
    bubble.turnRight(40 * deltaTime);
  });
}

const baseShadow = new waveCylinder(0.03, 1.9, 1.9, 64);
baseShadow.placeAt(0, 0.03, 0);
baseShadow.setColor(PALETTE.CHARCOAL);
baseShadow.setOpacity(0.18);

const displayBase = new waveCylinder(0.08, 2.2, 2.2, 64);
displayBase.placeAt(0, -0.03, 0);
displayBase.setColor(PALETTE.LIGHT_GRAY);
displayBase.setRoughness(0.55);
```

APIs to steal: `waveCylinder`, `waveSphere`, `waveTorus`, `waveCube`,
`setSurfacePreset`, `setOpacity`, `setMetallic`, `setRoughness`, `placeAt`,
`setScale`, and `onTick`.

Remix challenges:

- Turn it into a potion bottle with `PALETTE.PURPLE` liquid and emissive glow.
- Replace the cube label with an image decal.
- Add a cork using another short cylinder.
- Make bubbles stop at the liquid surface and respawn near the bottom.

#### Animated Material Chain

Object material methods can also join animation chains. Read this as: animate
the cube's color, give the animation a duration, play it, then trigger a
callback.

```ts
const textureCube = new waveCube(1.8, 1.8, 1.8);
textureCube.placeAt(0, 1.1, 1.5);
textureCube.setBaseTexture(textures.milkyway);

const boom = () => textureCube.exploding().intoPieces(15).apply();

textureCube
  .setColor(COLOR.RED, Animate)
  .over(3, Seconds)
  .play()
  .onComplete(boom);
```

APIs to steal: `setColor`, `setSurfacePreset`, `setRoughness`, `setMetallic`,
`setOpacity`, `setEmissiveColor`, `setEmissiveIntensity`, `enableGlow`,
`setBaseTexture`, `faceMaterial`, `setUVScale`, `setUVOffset`, `setUVRotation`,
`addDecal`, `paint`, `material`, and `setBlendMode`.

Natural-language constants to prefer: `PALETTE.CYAN`, `COLOR.RED`,
`SurfacePreset.Chrome`, `SurfacePreset.FrostedGlass`, `WaveSurface.Up`,
`WaveSurface.Forward`, `Animate`, and `Seconds`.

Remix challenges:

- Make a clickable material inspector where each click cycles `SurfacePreset`.
- Build a six-sided dice cube where every face has a different color or image.
- Make a neon sign by combining `setEmissiveColor`, `setEmissiveIntensity`, and
  `enableGlow`.
- Stamp decals onto a wall, then animate decal opacity or UV rotation.
- Use `sampleTexture` from Lesson 19's Demo Atlas to turn a material into a 3D pixel
  sculpture.

Source demo references: `material-surface-setter-probe-grid/main.ts`,
`vertex-color-pbr-painting/main.ts`, and `colored-light-projection/main.ts`.

## Lesson 6: Using Model Assets

Use `wave3DObject` when you want to place an imported model asset.

```ts
const ship = new wave3DObject(models.Spaceship);
ship.placeAt(0, 2, 0);
ship.setUniformScale(0.4);
ship.turnRight(30);
```

You can swap a model later:

```ts
const ship = new wave3DObject(models.Spaceship);
ship.placeAt(0, 2, 0);

ship.useModel(models.XWingStarfighter);
```

The global asset aliases are typed, so `models.` and `textures.` should
autocomplete inside the WaveStudio editor.

### Model Motion Lab

Imported models are not only things to look at. Treat a model like any other 3D
entity: it can move every frame, bank, play sounds, leave trails, shoot
projectiles, collide, and trigger UI.

| Actor behavior | Use it for | APIs |
| --- | --- | --- |
| Fly loop | Spaceships, birds, drones, fish | `onTick`, `moveForward`, `turnRight`, `rollLeft` |
| Debug direction | Fix model orientation before gameplay | `showDirection`, `turnRight`, `resetOrigin` |
| Motion trail | Speed lines, thrusters, magic afterimages | `model.enableTrail` |
| Shooting | Lasers, cannonballs, spell bolts | `whenPress`, `getWorldPosition`, `alignDirectionWith`, `after` |

```ts
const fighter = new wave3DObject(models.XWingStarfighter);
fighter.setUniformScale(0.45);
fighter.placeAt(0, 2, -8);
fighter.showDirection();

fighter.model.enableTrail({
  trailColor: PALETTE.CYAN,
  intensity: 1.2,
  samples: 16,
});

let flightSeconds = 0;

fighter.onTick((_self, deltaTime) => {
  flightSeconds += deltaTime;
  fighter.moveForward(4 * deltaTime);
  fighter.turnRight(18 * deltaTime);
  fighter.rollLeft(Math.sin(flightSeconds * 3) * 0.35);
});
```

Now add a simple Space-key laser. The projectile copies the fighter's current
position and direction, moves forward every frame, then destroys itself after a
short lifetime.

```ts
const fighter = new wave3DObject(models.XWingStarfighter);
fighter.setUniformScale(0.45);
fighter.placeAt(0, 2, -8);
fighter.showDirection();

const projectileSpeed = 16;
const projectileLifetime = 1.4;
const fireCooldown = 0.18;
let canFire = true;

function fireLaser() {
  if (!canFire) return;
  canFire = false;

  const shot = new waveSphere(0.12, 12);
  shot.setColor(PALETTE.CYAN);
  shot.setEmissiveColor(PALETTE.CYAN);
  shot.setEmissiveIntensity(2);
  shot.placeAt(fighter);
  shot.alignDirectionWith(fighter);
  shot.moveForward(1.2);
  shot.addTag("player-shot");

  shot.onTick((_self, deltaTime) => {
    shot.moveForward(projectileSpeed * deltaTime);
  });

  shot.after(projectileLifetime, Seconds).do(() => shot.destroy());
  fighter.after(fireCooldown, Seconds).do(() => canFire = true);
  fighter.playSound(audios.canon_shoot, { volume: 0.6 });
}

myScene.director.whenPress(Keyboard.Space, fireLaser);
```

If a model appears to fly sideways, first make the direction visible with
`showDirection()`, then fix the asset orientation with `turnRight(...)`,
`resetOrigin()`, or the transform axis-swap helpers before adding gameplay.

### Asset Staging Gallery

Model assets become much easier to teach when you stage them like props: one
asset per pedestal, with labels, scale helpers, and optional part access.

| Asset task | Use it for | APIs |
| --- | --- | --- |
| Place a prop | Furniture, vehicles, characters | `new Prop(models.X)`, `placeAbove` |
| Normalize size | Imported models vary wildly | `enlargeBy`, `shrinkBy`, `setUniformScale` |
| Reorient | Model forward axes differ | `turnRight`, `resetOrigin`, `transform.swap...` |
| Inspect parts | Robot arms, doors, wheels | `getPart`, `showBoundingBox` |
| Swap assets | Character skins or vehicles | `useModel` |

```ts
const pedestal = new waveCylinder(0.25, 2.2, 2.2, 48);
pedestal.setColor(PALETTE.SLATE);
pedestal.placeAt(0, 0.15, 0);
pedestal.showLabel("asset pedestal");

const table = new Prop(models.Table_Large_Circular)
  .enlargeBy(4)
  .placeAbove(pedestal);

const vase = new Prop(models.vase_de_Nesle)
  .shrinkBy(170)
  .placeAbove(table)
  .moveLeft(0.8);

const books = new Prop(models.Books)
  .enlargeBy(6)
  .placeAbove(table)
  .moveRight(0.8);

table.whenClickedOn(() => {
  table.showBoundingBox();
  scene.print("Use scale helpers before tuning exact positions.");
});
```

For multipart models, inspect named parts instead of treating the model as a
single opaque object:

```ts
const robot = new Prop(models.roboticArm);
robot.placeAt(0, 0, 4);
robot.showPosition();

const gripper = robot.getPart("gripper");
gripper?.setColor(PALETTE.CYAN);
gripper?.showBoundingBox();
```

Remix challenges:

- Build a museum shelf with one model per pedestal.
- Add click handlers that swap each model between two variants.
- Turn one displayed model into a moving actor with the Model Motion Lab.
- Make a "scale fixer" lesson where every model starts too large or too small.
- Combine this with `placeAround` to stage chairs, trees, enemies, or crystals.

## Lesson 7: Animation Over Time

Use `onTick` when you want behavior to run every update. The callback receives
the entity and `deltaTime`.

```ts
const spinner = new waveCube(2, 2, 2);
spinner.setColor(PALETTE.SAGE);
spinner.placeAt(0, 2, 0);

spinner.onTick((_self, deltaTime) => {
  spinner.turnRight(90 * deltaTime);
  spinner.moveUp(Math.sin(Date.now() * 0.004) * 0.01);
});
```

You can schedule less frequent work with time rules:

```ts
const spinner = new waveCube(2, 2, 2);

spinner.onTick(() => {
  scene.print(`FPS: ${engine.getCurrentFPS().toFixed(0)}`);
}, every(1, Seconds));
```

For one-shot delayed behavior, use `after`:

```ts
const spinner = new waveCube(2, 2, 2);

spinner.after(2, Seconds).do(() => {
  spinner.setColor(PALETTE.FUCHSIA);
});
```

### Completion Chains

Animation builders return a lifecycle handle from `.play()`. That handle can
run code when the animation finishes with `.onComplete(...)`. This is the
pattern in examples that animate a face first, then trigger an explosion.

```ts
const myCube = new Cube()
  .placeAt(0, 5, 0)
  .setColor(COLOR.BLUE);


const boom = () => {
  myCube
    .exploding()
    .intoPieces(15)
    .apply();
};

myCube
  .setColor(COLOR.RED, Animate)
  .over(3, Seconds)
  .play()
  .onComplete(boom);
```

Read this from top to bottom:

1. `setColor(COLOR.RED, Animate)` creates a material animation builder.
2. `over(3, Seconds)` sets the duration.
3. `play()` starts the animation and returns a lifecycle handle.
4. `onComplete(boom)` runs `boom` after the color animation finishes.

### Motion Pattern Gallery

Animation in WaveStudio is a small set of repeatable patterns: frame updates,
scheduled actions, transform builders, and material builders. This gallery
shows the difference by placing one object per pattern.

| Pattern | Use it for | Main APIs |
| --- | --- | --- |
| Tick spin | Continuous simulation | `onTick`, `deltaTime` |
| Model actor | Flying, driving, or shooting models | `wave3DObject`, `moveForward`, `whenPress` |
| Timed transform | One movement over a duration | `moveAlong(..., Animate).over(...)` |
| Delayed action | Something that happens later | `after(...).do(...)` |
| Color animation | Smooth material change | `setColor(..., Animate)` |

```ts
type MotionPattern = {
  label: string;
  create: () => waveCube;
  animate: (object: waveCube) => void;
};

const motionPatterns: MotionPattern[] = [
  {
    label: "tick spin",
    create: () => new waveCube(1, 1, 1),
    animate: (object) => {
      object.onTick((_self, deltaTime) => {
        object.turnRight(90 * deltaTime);
      });
    },
  },
  {
    label: "timed rise",
    create: () => new waveCube(1, 1, 1),
    animate: (object) => {
      object
        .moveAlong(Direction.Up, 2, Animate)
        .over(1.5, Seconds)
        .withEasingEffect(GSAPEasing.SineInOut)
        .play();
    },
  },
  {
    label: "delayed push",
    create: () => new waveCube(1, 1, 1),
    animate: (object) => {
      object.after(1, Seconds).do(() => {
        object
          .moveAlong(Direction.Forward, 2, Animate)
          .over(1, Seconds)
          .play();
      });
    },
  },
  {
    label: "color fade",
    create: () => new waveCube(1, 1, 1),
    animate: (object) => {
      object
        .setColor(PALETTE.FUCHSIA, Animate)
        .over(2, Seconds)
        .play();
    },
  },
];

const motionColors = [
  PALETTE.CYAN,
  PALETTE.GOLDENROD,
  PALETTE.SAGE,
  PALETTE.COBALT,
];
const motionSpacing = 2.3;

for (let i = 0; i < motionPatterns.length; i++) {
  const pattern = motionPatterns[i];
  const object = pattern.create();

  object.setColor(motionColors[i]);
  object.placeAt((i - 1.5) * motionSpacing, 1, 0);
  object.addTag("motion-demo");

  pattern.animate(object);
  object.whenClickedOn(() => scene.print(pattern.label));
}
```

### Timeline Remix Ladder

Once the four animation patterns make sense, combine them into tiny sequences.

```ts
const dancer = new Cube().placeAt(0, 1, 0).setColor(PALETTE.PURPLE);

const dance = () => {
  dancer.moveUp(1, Animate).over(0.35, Seconds).play()
    .onComplete(() => {
      dancer.rotateAroundBy(Direction.Up, 180, Animate).over(0.35, Seconds).play()
        .onComplete(() => {
          dancer.moveDown(1, Animate).over(0.35, Seconds).play();
        });
    });
};

dancer.whenClickedOn(dance);
```

Remix challenges:

- Turn the nested callbacks into named functions: `jump`, `spin`, `land`.
- Add a color fade at the same time as the jump.
- Use `after(3, Seconds).do(dance)` for an automatic loop.
- Trigger a particle effect only when the final animation completes.

## Lesson 8: Input

Many 3D entities expose keyboard helpers such as `whenPress`, `whenRelease`,
and `whenHolding`.

```ts
const player = new waveCube(1, 1, 1);
player.setColor(PALETTE.TURQUOISE);
player.placeAt(0, 1, 0);

player.whenPress(Keyboard.W, () => player.moveForward(0.5));
player.whenPress(Keyboard.S, () => player.moveBackward(0.5));
player.whenPress(Keyboard.A, () => player.moveLeft(0.5));
player.whenPress(Keyboard.D, () => player.moveRight(0.5));
player.whenPress(Keyboard.Space, () => player.moveUp(1));
```

Object-level input is compact and great for learning. For whole-scene games,
prefer director-level input such as `myScene.director.whenPress(...)` and
`myScene.director.isKeyPressed(...)` so movement and firing are controlled by
the active scene instead of feeling tied to one object callback.

### Debug Helpers and Keyboard Driving

For game-like controls, `whenHolding` feels smoother than `whenPress` because
the callback keeps firing while the key is down. Debug helpers make the
object's state visible while you tune movement.

| Helper | What to use it for |
| --- | --- |
| `showDirection()` | Draw the object's forward arrow. |
| `showPosition()` | Show the object's world position. |
| `showCoordinate()` | Show local axes so turns are easier to reason about. |
| `showBoundingBox()` | Show the object's occupied bounds. |
| `showSpeed()` | Show motion speed while tuning movement. |

```ts
const car = new Cube()
  .placeAt(0, 0.5, 0)
  .setColor(PALETTE.BLUE);

car.setScale(1.6, 0.35, 2.6);
car.showDirection();
car.showPosition();
car.showCoordinate();
car.showBoundingBox();

const driveDistancePerTick = 0.18;
const reverseDistancePerTick = 0.1;
const turnDegreesPerTick = 4;

car.whenHolding(Keyboard.W, () => car.moveForward(driveDistancePerTick));
car.whenHolding(Keyboard.S, () => car.moveBackward(reverseDistancePerTick));
car.whenHolding(Keyboard.A, () => car.turnLeft(turnDegreesPerTick));
car.whenHolding(Keyboard.D, () => car.turnRight(turnDegreesPerTick));

car.whenPress(Keyboard.Space, () => {
  car.showSpeed();
  scene.print("Debug overlays are on");
});
```

Objects can also react to pointer interaction:

```ts
const button = new waveSphere(0.8, 24);
button.setColor(PALETTE.GOLDENROD);
button.placeAt(0, 1, -3);

button.whenClickedOn(() => {
  button.setColor(PALETTE.ORANGE);
  button.enlargeBy(1.2);
});
```

### Input Mode Gallery

Good interactive scenes usually need more than one input style. This gallery
shows a small command system: keyboard for movement, click for selection, and a
mode key for changing what clicks mean.

| Input style | Best for | APIs |
| --- | --- | --- |
| `whenPress` | One-shot commands | Jump, toggle debug, reset |
| `whenHolding` | Continuous movement | Driving, flying, walking |
| `whenClickedOn` | Object-specific actions | Select, explode, inspect |
| Director controls | Whole-scene controls | `myScene.director.whenPress`, `myScene.director.isKeyPressed` |

```ts
let editMode: "paint" | "inspect" = "paint";

const target = new Cube().placeAt(0, 1, 0).setColor(PALETTE.CYAN);
target.showDirection();
target.showPosition();

myScene.director.whenPress(Keyboard.Tab, () => {
  editMode = editMode === "paint" ? "inspect" : "paint";
  scene.print(`Mode: ${editMode}`);
});

target.whenHolding(Keyboard.W, () => target.moveForward(0.12));
target.whenHolding(Keyboard.A, () => target.turnLeft(2));
target.whenHolding(Keyboard.D, () => target.turnRight(2));

target.whenClickedOn(() => {
  if (editMode === "paint") {
    target.setColor(PALETTE.randomFrom([
      PALETTE.CYAN,
      PALETTE.GOLDENROD,
      PALETTE.PINK,
      PALETTE.GREEN,
    ]));
  } else {
    target.showBoundingBox();
    scene.print(target.name ?? "unnamed target");
  }
});
```

Remix challenges:

- Add `erase`, `clone`, and `explode` modes.
- Use a `waveUIText` label to display the current mode.
- Add keyboard shortcuts for camera changes.
- Turn the car example into a small driving course with debug toggles.

## Lesson 9: Physics

Use static bodies for floors and walls, dynamic bodies for objects that should
move under forces, and kinematic bodies for objects you control directly.

```ts
const floor = new waveGround(20, 20, 4);
floor.setColor(PALETTE.CHARCOAL);
floor.useStaticBody();
floor.addTag("floor");

const ball = new waveSphere(1, 32);
ball.setColor(PALETTE.GOLDENROD);
ball.placeAt(0, 8, 0);
ball.useDynamicBody();
ball.setMass(2);
ball.setRestitution(0.8);

ball.onCollisionBeginByTag("floor", () => {
  ball.setColor(PALETTE.SKY);
});
```

For manual forces:

```ts
const ball = new waveSphere(0.5, 24);
ball.useDynamicBody();
ball.placeAt(0, 2, 0);

ball.applyImpulse(Direction.Up, 8);
ball.applyForce(Direction.Forward, 20);
ball.setLinearVelocity(Direction.Forward, 4);
```

For controlled movement with collision handling:

```ts
const actor = new waveCube(1, 2, 1);
actor.useKinematicBody();
actor.placeAt(0, 1, 0);

actor.whenPress(Keyboard.ArrowUp, () => {
  actor.moveWithCollisions(Direction.Forward, 0.5);
});
```

### Physics Body Gallery

The body type is the first physics decision. Static objects do not move,
dynamic objects are controlled by physics, and kinematic objects are moved by
your code while still respecting collision checks.

| Body style | Use it for | Main APIs |
| --- | --- | --- |
| Static | Floors, walls, ramps | `useStaticBody` |
| Dynamic | Balls, crates, debris | `useDynamicBody`, `setMass`, `applyImpulse` |
| Kinematic | Players, platforms, doors | `useKinematicBody`, `moveWithCollisions` |
| Collision reaction | Game rules when things touch | `onCollisionBeginByTag` |

```ts
type PhysicsExample = {
  label: string;
  create: () => waveCube | waveSphere;
  setup: (object: waveCube | waveSphere) => void;
};

const physicsExamples: PhysicsExample[] = [
  {
    label: "static floor",
    create: () => new waveCube(2.5, 0.25, 2.5),
    setup: (object) => {
      object.useStaticBody();
      object.addTag("physics-floor");
      object.setColor(PALETTE.CHARCOAL);
    },
  },
  {
    label: "dynamic ball",
    create: () => new waveSphere(0.55, 32),
    setup: (object) => {
      object.useDynamicBody();
      object.setMass(1);
      object.setRestitution(0.85);
      object.setColor(PALETTE.GOLDENROD);
      object.applyImpulse(Direction.Up, 2);
      object.onCollisionBeginByTag("physics-floor", () => {
        object.setColor(PALETTE.SKY);
      });
    },
  },
  {
    label: "kinematic mover",
    create: () => new waveCube(1, 1, 1),
    setup: (object) => {
      object.useKinematicBody();
      object.setColor(PALETTE.TURQUOISE);
      object.whenPress(Keyboard.ArrowUp, () => {
        object.moveWithCollisions(Direction.Forward, 0.4);
      });
    },
  },
];

const physicsSpacing = 3;

for (let i = 0; i < physicsExamples.length; i++) {
  const example = physicsExamples[i];
  const object = example.create();

  object.placeAt((i - 1) * physicsSpacing, 1, 0);
  object.addTag("physics-demo");
  example.setup(object);

  object.whenClickedOn(() => scene.print(example.label));
}
```

### Physics Interaction Lab

Physics gets interesting when collisions change the scene. Build a pressure pad
that opens a door, then swap the ball for crates, cars, or fragments.

```ts
const pad = new waveCube(2, 0.18, 2);
pad.placeAt(0, 0.1, 0);
pad.setColor(PALETTE.SAGE);
pad.useStaticBody();
pad.addTag("pressure-pad");

const door = new waveCube(2, 3, 0.3);
door.placeAt(0, 1.5, 3);
door.setColor(PALETTE.CHARCOAL);
door.useStaticBody();

const ball = new waveSphere(0.55, 32);
ball.placeAt(0, 4, -2);
ball.setColor(PALETTE.GOLDENROD);
ball.useDynamicBody();
ball.setMass(1.2);

ball.onCollisionBeginByTag("pressure-pad", () => {
  pad.setColor(PALETTE.GREEN);
  door.moveUp(3, Animate).over(0.8, Seconds).play();
  scene.print("Door opened by collision");
});
```

Remix challenges:

- Require two pads before the door opens.
- Add `setLifeTime` to falling objects.
- Make a pinball bumper using `applyImpulse`.
- Use `exploding().enablePhysics(true)` to create physical debris.

## Lesson 10: Camera, Lighting, and Atmosphere

The scene has configurators for camera, sky, fog, lighting, weather, terrain,
and post-processing. Configurators usually end with `.apply()`.

```ts
scene.camera
  .orbit({
    target: { x: 0, y: 1, z: 0 },
    distance: 10,
    fov: 60,
    rotateSensitivity: 0.8,
  })
  .gestureOrbit({ enabled: true })
  .apply();

scene.lighting
  .sun({
    direction: { x: -1, y: -2, z: -1 },
    color: PALETTE.WHITE,
    intensity: 1.2,
    shadowEnabled: true,
  })
  .ambient({ color: PALETTE.SKY, intensity: 0.35 })
  .apply();

scene.sky
  .backgroundColor(PALETTE.MIDNIGHT)
  .brightness(0.8)
  .apply();

scene.fog
  .exp2()
  .color(PALETTE.SKY)
  .density(0.01)
  .apply();
```

You can also create camera entities. Any entity with a camera component can
possess or activate a view:

```ts
const cameraRig = new waveCube(0.2, 0.2, 0.2);
cameraRig.placeAt(0, 5, -8);
cameraRig.camera.setCameraLookAt(0, 1, 0);
cameraRig.camera.activate();
```

### Camera, Light, and Atmosphere Gallery

Use scene configurators for global setup, and use `waveCamera` or `waveLight`
when you want cameras and lights to behave like movable scene objects.

| Tool | Use it for | Main APIs |
| --- | --- | --- |
| Orbit camera | Inspecting a scene interactively | `scene.camera.orbit`, `gestureOrbit` |
| Camera entity | Cutscenes or switchable views | `waveCamera`, `activate`, `assignToNum1` |
| Point light | Local glow from a lamp or pickup | `waveLight.asPointLight` |
| Spotlight | Cones, flashlights, stage lights | `asSpotlight`, `aimAt` |
| Sky and fog | World mood and depth | `scene.sky`, `scene.fog` |

```ts
scene.camera
  .orbit({
    target: { x: 0, y: 1, z: 0 },
    distance: 12,
    fov: 55,
  })
  .gestureOrbit({ enabled: true })
  .apply();

scene.sky
  .backgroundColor(PALETTE.MIDNIGHT)
  .brightness(0.75)
  .apply();

scene.fog
  .exp2()
  .color(PALETTE.SKY)
  .density(0.012)
  .apply();

const cinematicCamera = new waveCamera();
cinematicCamera.placeAt(0, 4, -8);
cinematicCamera.turnCameraTo(0, 1, 0);
cinematicCamera.setFov(50);
cinematicCamera.assignToNum1();

const pointLight = new waveLight();
pointLight.asPointLight();
pointLight.setLightColor(PALETTE.GOLDENROD);
pointLight.setIntensity(1.4);
pointLight.setRange(8);
pointLight.placeAt(-3, 3, 0);

const spotlight = new waveLight();
spotlight.asSpotlight();
spotlight.setLightColor(PALETTE.CYAN);
spotlight.setIntensity(2);
spotlight.setAngle(45);
spotlight.placeAt(3, 4, -2);
spotlight.aimAt(0, 1, 0);
```

### Mood Preset Gallery

Camera and atmosphere settings are design tools. A small preset system lets
learners compare moods without rewriting the scene.

```ts
const moods = {
  study: {
    sky: PALETTE.WHITE,
    fog: PALETTE.SKY,
    sun: PALETTE.WARM_WHITE,
    intensity: 0.9,
  },
  night: {
    sky: PALETTE.MIDNIGHT,
    fog: PALETTE.BLUE,
    sun: PALETTE.CYAN,
    intensity: 0.35,
  },
  alarm: {
    sky: PALETTE.CHARCOAL,
    fog: PALETTE.RED,
    sun: PALETTE.RED,
    intensity: 1.6,
  },
};

function applyMood(name: keyof typeof moods) {
  const mood = moods[name];

  scene.sky.backgroundColor(mood.sky).brightness(mood.intensity).apply();
  scene.fog.exp2().color(mood.fog).density(0.012).apply();
  scene.lighting
    .sun({ direction: { x: -1, y: -2, z: -1 }, color: mood.sun, intensity: mood.intensity })
    .ambient({ color: mood.fog, intensity: 0.25 })
    .apply();

  scene.print(`Mood: ${name}`);
}

myScene.director.whenPress(Keyboard.Num1, () => applyMood("study"));
myScene.director.whenPress(Keyboard.Num2, () => applyMood("night"));
myScene.director.whenPress(Keyboard.Num3, () => applyMood("alarm"));
```

Remix challenges:

- Add rain or snow to one mood.
- Add a spotlight that follows the player in the night preset.
- Make a photo-mode UI with buttons instead of number keys.
- Store the last selected mood with `myCloud`.

## Lesson 11: Scene Queries and Organization

Names and tags make scenes easier to manage.

```ts
const door = new waveCube(2, 3, 0.3);
door.setName("main-door");
door.addTag("door");
door.addTag("interactive");

const doors = scene.getByTag("door");
const mainDoor = scene.getFirstByName("main-door");

for (const entity of doors) {
  if (entity instanceof waveCube) {
    entity.showBoundingBox();
  }
}

if (mainDoor instanceof waveCube) {
  mainDoor.hideBoundingBox();
}
```

Scene-level helpers include:

```ts
const helperDoor = new waveCube(2, 3, 0.3);
helperDoor.setName("main-door");
helperDoor.addTag("door");
helperDoor.addTag("interactive");

const temporaryMarker = new waveSphere(0.25, 16);
temporaryMarker.setColor(PALETTE.YELLOW);
temporaryMarker.placeAbove(helperDoor);
temporaryMarker.addTag("temporary");

scene.getAll();
scene.getByName("main-door");
scene.getByTag("interactive");
scene.find((entity) => entity.hasTag("door"));
scene.destroyByTag("temporary");
```

### Query and Tag Inspector

Tags are not only for cleanup. They let you build systems that affect whole
families of objects: all enemies, all pickups, all lights, all temporary
helpers.

```ts
const categories = [
  { tag: "enemy", color: PALETTE.RED, x: -3 },
  { tag: "pickup", color: PALETTE.GOLDENROD, x: 0 },
  { tag: "helper", color: PALETTE.CYAN, x: 3 },
];

for (const category of categories) {
  for (let i = 0; i < 3; i++) {
    const marker = new waveSphere(0.35, 16);
    marker.setColor(category.color);
    marker.placeAt(category.x, 0.6, i * 1.1);
    marker.addTag(category.tag);
    marker.showLabel(category.tag);
  }
}

function highlightTag(tag: string) {
  for (const entity of scene.getByTag(tag)) {
    if (entity instanceof waveSphere) {
      entity.showBoundingBox();
      entity.scaleBy(1.15, Animate).over(0.2, Seconds).play();
    }
  }

  scene.print(`${scene.getByTag(tag).length} object(s) tagged ${tag}`);
}

myScene.director.whenPress(Keyboard.Num1, () => highlightTag("enemy"));
myScene.director.whenPress(Keyboard.Num2, () => highlightTag("pickup"));
myScene.director.whenPress(Keyboard.Num3, () => highlightTag("helper"));
myScene.director.whenPress(Keyboard.Delete, () => scene.destroyByTag("helper"));
```

Remix challenges:

- Add a `selected` tag when an object is clicked.
- Make a cleanup key for every object tagged `temporary`.
- Use names for unique objects and tags for groups.
- Build a scene debugger that prints counts by tag.

## Lesson 12: Groups and Spawning

Use groups when objects belong together.

```ts
const row = scene.createGroup<waveCube>("row");

for (let i = 0; i < 5; i++) {
  const cube = new waveCube(1, 1, 1);
  cube.setColor(PALETTE.CYAN);
  cube.placeAt(i * 1.4 - 2.8, 0.5, 0);
  row.add(cube);
}
```

The scene also exposes placement helpers such as `spawnInGrid`,
`spawnOnPath`, `spawnAround`, and `spawnOnSurface`.

```ts
const tile = new waveCube(1, 0.2, 1);
tile.setColor(PALETTE.LIGHT_GRAY);

const grid = scene.spawnInGrid(tile, {
  origin: { x: -2, y: 0.1, z: -2 },
  rows: 4,
  cols: 4,
  spacing: 1.25,
});
```

If your editor complains about option names, open autocomplete on the second
argument. The generated declaration file contains the exact `SpawnInGridOptions`
shape.

### Spawning Pattern Gallery

Spawning is how small code creates large scenes. Use a different placement
pattern depending on the shape of the idea.

| Pattern | Use it for | APIs |
| --- | --- | --- |
| Row or grid | tiles, seats, pixels, crops | `placeInGrid`, `withRows`, `withCols` |
| Circle | chairs, enemies, crystals | `placeAround`, `inRadius`, `faceCenter` |
| Path | roads, patrol routes, particle trails | `wavePath`, `placeOnPath`, `followPath` |
| Group | move or hide many objects together | `createGroup`, `group.add` |

```ts
const treeSource = new Cone(2, 1.2);
treeSource.setColor(PALETTE.GREEN);

const treeAsset = treeSource.createInstanceSource();
const tree = new waveInstanceMesh().useModel(treeAsset.assetName);

const orchard = myScene
  .placeInGrid(tree)
  .hideOriginal()
  .at({ x: -5, y: 1, z: -5 })
  .withRows(5)
  .withCols(5)
  .withSpacing(2)
  .onEachCell((entity, row, col) => {
    entity.setName(`tree-${row}-${col}`);
    entity.moveUp(Math.random() * 0.25);
  })
  .place();

const campfire = new Cylinder(0.4, 0.8, 0.8);
campfire.placeAt(4, 0.2, 0);
campfire.setColor(PALETTE.ORANGE);

const chair = new Prop(models.Chair).enlargeBy(5);

myScene
  .placeAround(campfire)
  .with(chair)
  .inTotal(6)
  .inRadius(3)
  .onPerimeter()
  .faceCenter()
  .place();
```

Remix challenges:

- Scatter pickups in a grid, then remove random cells.
- Make every spawned object clickable.
- Use `placeAround` for enemies and `faceCenter()` so they look at the player.
- Convert a drawn `wavePath` into a patrol route.

## Lesson 13: UI Overlays

WaveStudio has 2D UI entities such as `waveUIText`, `waveUIButton`,
`waveUIImage`, `waveUISlider`, and more.

```ts
const hudBackground = PALETTE.modifyAlphaChannel(PALETTE.BLACK, 55);

const scoreLabel = new waveUIText();
scoreLabel.setText("Score: 0");
scoreLabel.setFontSize(28);
scoreLabel.setColor(PALETTE.WHITE);
scoreLabel.setBackgroundColor(hudBackground);
scoreLabel.setSize(180, 48);
scoreLabel.setScreenPositionPixels(24, 24);
```

Update UI from gameplay:

```ts
const scoreLabel = new waveUIText()
  .setText("Score: 0")
  .setFontSize(24)
  .setScreenPositionPixels(24, 24);

let score = 0;

function addScore(points: number) {
  score += points;
  scoreLabel.setText(`Score: ${score}`);
}

const gem = new waveSphere(0.5, 24);
gem.setColor(PALETTE.GOLDENROD);
gem.placeAt(0, 1, -4);

gem.whenClickedOn(() => {
  addScore(1);
  gem.hide();
  gem.destroy();
});
```

### UI Widget Gallery

UI objects use the same basic workflow as 3D objects: create, configure, place
in the active scene, then attach callbacks when the widget should react.

| Widget | Use it for | Main APIs |
| --- | --- | --- |
| `waveUIText` | Score, labels, status text | `setText`, `setFontSize` |
| `waveUIButton` | Clickable commands | `setText`, `onClick` |
| `waveUIProgressBar` | Health, progress, loading | `setProgress`, `setProgressColor` |
| `waveUISlider` | Tunable numeric values | `setRange`, `setValue`, `onChange` |
| `waveUICrosshair` | Reticles and pointer overlays | `setCrosshairColor`, `setDot` |

```ts
const panelColor = PALETTE.modifyAlphaChannel(PALETTE.BLACK, 55);

const title = new waveUIText();
title.setText("WaveStudio HUD");
title.setFontSize(30);
title.setColor(PALETTE.WHITE);
title.setBackgroundColor(panelColor);
title.setSize(260, 48);
title.setScreenPositionPixels(24, 24);

const startButton = new waveUIButton();
startButton.setText("Start");
startButton.setFontSize(22);
startButton.setColor(PALETTE.BLACK);
startButton.setBackgroundColor(PALETTE.GOLDENROD);
startButton.setSize(160, 44);
startButton.setScreenPositionPixels(24, 84);
startButton.onClick(() => scene.print("Button clicked"));

const energyBar = new waveUIProgressBar();
energyBar.setProgress(72, 100);
energyBar.setProgressColor(PALETTE.GREEN);
energyBar.setBackgroundColor(panelColor);
energyBar.setSize(220, 24);
energyBar.setScreenPositionPixels(24, 144);

const energySlider = new waveUISlider();
energySlider.setRange(0, 100, 1);
energySlider.setValue(72);
energySlider.setBackgroundColor(panelColor);
energySlider.setSize(220, 30);
energySlider.setScreenPositionPixels(24, 184);
energySlider.onChange((event) => {
  energyBar.setProgress(event.data.value, 100);
});

const crosshair = new waveUICrosshair();
crosshair.setCrosshairColor(PALETTE.CYAN);
crosshair.setDot(3, PALETTE.WHITE);
crosshair.setSize(80, 80);
crosshair.setNormalizedScreenPosition(0.5, 0.5);
```

### HUD Recipe Gallery

A useful HUD usually has three layers: always-visible status, contextual
prompts, and controls that change the world.

```ts
const statusText = new waveUIText()
  .setText("Mode: build")
  .setFontSize(20)
  .setColor(PALETTE.WHITE)
  .setBackgroundColor(PALETTE.modifyAlphaChannel(PALETTE.BLACK, 55))
  .setSize(220, 40)
  .setScreenPositionPixels(24, 24);

const promptText = new waveUIText()
  .setText("Click a cube to paint it")
  .setFontSize(18)
  .setColor(PALETTE.CYAN)
  .setScreenPositionPixels(24, 72);

const resetButton = new waveUIButton()
  .setText("Reset")
  .setSize(120, 40)
  .setScreenPositionPixels(24, 112);

resetButton.onClick(() => {
  scene.destroyByTag("temporary");
  statusText.setText("Mode: reset complete");
});
```

Remix challenges:

- Add a health bar and update it from collisions.
- Add a slider that controls light intensity.
- Add a dropdown for mood presets from Lesson 10.
- Use `waveUICanvas` when text/widgets are not enough and you want custom
  drawing.

## Lesson 14: Audio and Effects

Objects can play sounds by asset name or path:

```ts
const speaker = new waveCube(1, 1, 1);
speaker.placeAt(0, 1, 0);

speaker.playSound(audios.studioSound, {
  loop: false,
  volume: 0.8,
});
```

Many 3D objects expose an `fx` facade:

```ts
const emitter = new waveSphere(0.4, 16);
emitter.setColor(PALETTE.WHITE);
emitter.placeAt(0, 1, 0);

emitter.fx
  .createSmoke("soft-smoke")
  .color(PALETTE.toNormalizedAlpha(PALETTE.LIGHT_GRAY))
  .rate(20)
  .play();
```

Effect option names vary by effect system. Let autocomplete guide the options
object.

### Audio and FX Gallery

Audio and effects can be attached to ordinary scene objects. A hidden or tiny
anchor object is often enough when the effect is environmental rather than
visible geometry.

| Effect type | Use it for | Main APIs |
| --- | --- | --- |
| Sound | Clicks, pickups, ambience | `playSound` |
| Smoke | Exhaust, clouds, mystery | `fx.createSmoke(...).play()` |
| Preset burst | Sparks and impacts | `waveFxPresets.impactSparks` |
| Weather preset | Rain, snow, lightning | `waveFxPresets.rain`, `fx.play` |
| Explosion | Breakable objects | `explode`, `exploding` |

```ts
const smokeOrb = new waveSphere(0.35, 16);
smokeOrb.setColor(PALETTE.LIGHT_GRAY);
smokeOrb.placeAt(-3, 1, 0);

smokeOrb.fx
  .createSmoke("gallery-smoke")
  .rising()
  .color(PALETTE.toNormalizedAlpha(PALETTE.LIGHT_GRAY))
  .rate(14)
  .play();

const sparkPad = new waveCube(1, 0.25, 1);
sparkPad.setColor(PALETTE.GOLDENROD);
sparkPad.placeAt(0, 0.5, 0);

sparkPad.whenClickedOn(() => {
  sparkPad.playSound(audios.studioSound, { volume: 0.7 });
  sparkPad.fx.play(waveFxPresets.impactSparks());
});

const rainAnchor = new waveCube(0.2, 0.2, 0.2);
rainAnchor.placeAt(0, 4, 0);
rainAnchor.hide();
rainAnchor.fx.play(waveFxPresets.rain());

const breakable = new waveCube(1, 1, 1);
breakable.setColor(PALETTE.CORAL);
breakable.placeAt(3, 1, 0);

breakable.whenClickedOn(() => {
  if (breakable.isDestroyed) return;
  breakable.exploding().intoPieces(10).withStrength(5).apply();
});
```

### Triggered FX Recipes

Effects feel best when they are tied to a clear event: click, collision, speed,
state change, or completion callback.

```ts
const portal = new Torus(1.2, 0.12);
portal.placeAt(0, 1.5, 0);
portal.setColor(PALETTE.CYAN);
portal.setEmissiveColor(PALETTE.CYAN);
portal.setEmissiveIntensity(1.2);
portal.enableGlow(0.8, PALETTE.CYAN);

portal.onTick((_self, dt) => {
  portal.turnRight(90 * dt);
});

portal.whenClickedOn(() => {
  portal.playSound(audios.studioSound, { volume: 0.8 });
  portal.fx.play(waveFxPresets.impactSparks());
  portal
    .setColor(PALETTE.FUCHSIA, Animate)
    .over(0.4, Seconds)
    .play();
});
```

Remix challenges:

- Trigger smoke while a vehicle is moving.
- Play a sound only on the first collision.
- Add a cooldown so effects cannot spam.
- Pair every visual effect with a small UI message.

## Lesson 15: Persistent Scene Data

`myCloud` is a `WaveSceneDataStore`. It can open, edit, and save JSON records.

```ts
async function incrementHighScore(points: number) {
  const record = await myCloud.openRecord("scores");

  const nextScore = myCloud.json.inc(record, "highScore", points);
  myCloud.json.push(record, "events", {
    kind: "score",
    value: points,
    at: Date.now(),
  });

  const result = await myCloud.saveRecord("scores", record);

  if (result.ok) {
    scene.print(`Saved high score: ${nextScore}`);
  } else {
    scene.print("Could not save score");
  }
}
```

Check availability before depending on persistence:

```ts
if (!myCloud.available) {
  scene.print(`Cloud data unavailable: ${myCloud.reason ?? "unknown reason"}`);
}
```

### Persistence Pattern Gallery

Persistence is most useful when records have a simple shape. Keep one record
per system, then store small values and arrays inside it.

| Pattern | Record | Keys |
| --- | --- | --- |
| High score | `"scores"` | `highScore`, `events` |
| Settings | `"settings"` | `musicVolume`, `lastMood`, `difficulty` |
| World state | `"world"` | `unlockedDoors`, `collectedPickups` |
| Study/tutorial progress | `"tutorial"` | `completedLessons`, `lastOpenedAt` |

```ts
async function saveSettings(settings: {
  musicVolume: number;
  lastMood: string;
  difficulty: "easy" | "normal" | "hard";
}) {
  if (!myCloud.available) {
    scene.print("Settings are local-only in this environment.");
    return;
  }

  const record = await myCloud.openRecord("settings");

  myCloud.json.set(record, "musicVolume", settings.musicVolume);
  myCloud.json.set(record, "lastMood", settings.lastMood);
  myCloud.json.set(record, "difficulty", settings.difficulty);
  myCloud.json.set(record, "savedAt", Date.now());

  const result = await myCloud.saveRecord("settings", record);
  scene.print(result.ok ? "Settings saved" : "Settings save failed");
}

saveSettings({
  musicVolume: 0.7,
  lastMood: "night",
  difficulty: "normal",
});
```

Remix challenges:

- Save the number of exploded fragments from Boss Project 1.
- Save the last selected material preset from Lesson 5.
- Save player position and restore it on load.
- Add a "clear progress" button in UI that resets a record.

## Lesson 16: A Complete Mini Project

This creates a tiny clickable collection scene with a camera, lighting, a floor,
five rotating pickups, and a score label.

```ts
let score = 0;
const totalPickups = 5;
const playerStepDistance = 0.75;
const completionMessageFontSize = 32;
const hudBackground = PALETTE.modifyAlphaChannel(PALETTE.BLACK, 55);

scene.camera
  .orbit({
    target: { x: 0, y: 1, z: 0 },
    distance: 12,
    fov: 60,
  })
  .gestureOrbit({ enabled: true })
  .apply();

scene.lighting
  .sun({
    direction: { x: -1, y: -2, z: -1 },
    intensity: 1.2,
    shadowEnabled: true,
  })
  .ambient({ color: PALETTE.SKY, intensity: 0.35 })
  .apply();

const scoreLabel = new waveUIText();
scoreLabel.setText(`Score: ${score}/${totalPickups}`);
scoreLabel.setFontSize(28);
scoreLabel.setColor(PALETTE.WHITE);
scoreLabel.setBackgroundColor(hudBackground);
scoreLabel.setSize(220, 48);
scoreLabel.setScreenPositionPixels(24, 24);

const floor = new waveGround(20, 20, 4);
floor.setColor(PALETTE.CHARCOAL);
floor.useStaticBody();
floor.addTag("floor");

const player = new waveCube(1, 1, 1);
player.setColor(PALETTE.TURQUOISE);
player.placeAt(0, 0.5, 0);
player.useKinematicBody();

player.whenPress(Keyboard.W, () => player.moveForward(playerStepDistance));
player.whenPress(Keyboard.S, () => player.moveBackward(playerStepDistance));
player.whenPress(Keyboard.A, () => player.moveLeft(playerStepDistance));
player.whenPress(Keyboard.D, () => player.moveRight(playerStepDistance));

function updateScore(points: number) {
  score += points;
  scoreLabel.setText(`Score: ${score}/${totalPickups}`);

  if (score >= totalPickups) {
    scene.print("All pickups collected!", completionMessageFontSize, PALETTE.WHITE);
  }
}

for (let i = 0; i < totalPickups; i++) {
  const pickup = new waveSphere(0.45, 24);
  pickup.setColor(PALETTE.GOLDENROD);
  pickup.placeAt(i * 2 - 4, 0.75, -3);
  pickup.addTag("pickup");

  pickup.onTick((_self, deltaTime) => {
    pickup.turnRight(180 * deltaTime);
  });

  pickup.whenClickedOn(() => {
    if (pickup.isDestroyed) return;

    updateScore(1);
    pickup.hide();
    pickup.destroy();
  });
}
```

Try changing this project in small steps:

- Replace the pickups with `waveCube`, `waveTorus`, or `wave3DObject`.
- Use `models.Spaceship` for the player.
- Save the score with `myCloud`.
- Play a sound when a pickup is clicked.
- Add `useDynamicBody()` and collision callbacks for a physics version.

### Mini Project Upgrade Map

The mini project is intentionally small. Use it as a base and upgrade one
system at a time.

| Upgrade | What changes | Lessons to reuse |
| --- | --- | --- |
| Better world | Replace the floor with terrain, props, and lights | Lessons 5, 6, 10 |
| Better controls | Use `whenHolding`, debug overlays, camera follow | Lessons 8, 10 |
| Better pickups | Spawn in grid/circle/path and add effects | Lessons 12, 14 |
| Better rules | Add timers, win state, reset button | Lessons 7, 13 |
| Better memory | Save high score and chosen mood | Lesson 15 |

```ts
const pickupGroup = scene.createGroup("pickup-group");
const pickupPrototype = new waveSphere(0.45, 24);
pickupPrototype.setColor(PALETTE.GOLDENROD);

let score = 0;
function updateScore(points: number) {
  score += points;
  scene.print(`Score: ${score}`);
}

myScene
  .placeInGrid(pickupPrototype)
  .hideOriginal()
  .at({ x: -4, y: 0.75, z: -4 })
  .withRows(2)
  .withCols(5)
  .withSpacing(2)
  .onEachCell((pickup) => {
    pickup.addTag("pickup");
    pickupGroup.add(pickup);
    pickup.onTick((_self, dt) => pickup.turnRight(180 * dt));
    pickup.whenClickedOn(() => {
      if (pickup.isDestroyed) return;
      pickup.fx.play(waveFxPresets.impactSparks());
      updateScore(1);
      pickup.destroy();
    });
  })
  .place();
```

Boss-mode remix:

- Add a timer and fail state.
- Add a moving enemy that uses `whenHolding` or `onTick`.
- Save the best completion time with `myCloud`.
- Replace click collection with collision collection.
- Add a final recursive explosion when every pickup is collected.

## Lesson 17: Boss Project 1: Recursive Click Explosions

WaveStudio can do the concise recursive explosion pattern your friend showed:
click a shape, fracture it into pieces, then give each new piece the same click
handler. The declaration file exposes the natural aliases used in that style:
`Cube` is an alias for `waveCube`, and `Prop` is an alias for `waveProp`.

### What You Build

A clickable shape that splits into pieces. Each piece becomes clickable too, so
the scene teaches recursion by letting the learner keep exploding smaller and
smaller fragments.

The core recursion looks like this:

```ts
const target = new Cube()
  .placeAt(0, 5, 0)
  .setColor(PALETTE.BLUE);


const boom = (object: Prop): void => {
  object
    .exploding()
    .intoPieces(3)
    .withStrength(2)
    .apply()
    ?.forEach((piece) => {
      piece.whenClickedOn(() => boom(piece));
    });
};

target.whenClickedOn(() => boom(target));
```

That is recursion because `boom` attaches `boom` to every fragment it creates.
The `?.forEach(...)` keeps the example type-safe because `apply()` is declared
as `WaveGroup<T> | null`.

The two important APIs are:

- `object.whenClickedOn(...)` to react to clicks.
- `object.exploding().intoPieces(...).withStrength(...).apply()` to create
  a `WaveGroup` of fracture fragments.

### Fluent Chain Anatomy

| Chain step | Meaning |
| --- | --- |
| `object.exploding()` | Start a destruction builder from the clicked object. |
| `.intoPieces(3)` | Ask for three generated fragments. |
| `.withStrength(2)` | Push the fragments outward with more force. |
| `.withInteriorColor(PALETTE.MIDNIGHT)` | Color the newly exposed inside faces. |
| `.withLifeTime(4, Seconds)` | Let fragments clean themselves up after four seconds. |
| `.apply()` | Run the builder and return the created pieces. |

Here is the same idea with a few practical guardrails: a maximum recursion
depth, a maximum piece count, and temporary fragment lifetimes.

```ts
const root = new Cube()
  .placeAt(0, 5, 0)
  .setColor(PALETTE.CYAN);


const maxDepth = 4;
const maxPieces = 120;
let pieceCount = 0;

function boom(object: Prop, depth = maxDepth): void {
  if (object.isDestroyed || depth <= 0 || pieceCount >= maxPieces) return;

  pieceCount += 1;
  const nextDepth = depth - 1;

  object
    .exploding()
    .intoPieces(3)
    .withStrength(2)
    .withInteriorColor(PALETTE.MIDNIGHT)
    .withLifeTime(4, Seconds)
    .apply()
    ?.forEach((piece) => {
      piece.setColor(PALETTE.randomFrom([
        PALETTE.CYAN,
        PALETTE.GOLDENROD,
        PALETTE.CORAL,
        PALETTE.SAGE,
      ], pieceCount));
      piece.whenClickedOn(() => boom(piece, nextDepth));
    });
}

root.whenClickedOn(() => boom(root));
```

The manual split below is a different kind of recursion. It is useful when you
want to teach the algorithm yourself instead of using WaveStudio's fracture
system: destroy the clicked cube, create eight smaller cubes around its old
center, and attach the same click handler to each child.

```ts
const palette = [PALETTE.CYAN, PALETTE.GOLDENROD, PALETTE.CORAL, PALETTE.SAGE];
const maxDepth = 4;
const maxPieces = 256;
const spinYawDegreesPerSecond = 20;
const spinPitchDegreesPerSecond = 12;
const childScale = 0.52;
const childSpreadScale = 1.15;
const splitPushScale = 0.35;
const splitAnimationDuration = 0.25;
let createdPieces = 0;

function spawnPiece(
  x: number,
  y: number,
  z: number,
  size: number,
  depth: number
): waveCube | null {
  if (createdPieces >= maxPieces) return null;

  createdPieces += 1;

  const piece = new waveCube(size, size, size);
  piece.setColor(palette[depth % palette.length] ?? PALETTE.WHITE);
  piece.placeAt(x, y, z);
  piece.addTag("recursive-piece");

  piece.onTick((_self, deltaTime) => {
    piece.turnRight(spinYawDegreesPerSecond * deltaTime);
    piece.pitchUp(spinPitchDegreesPerSecond * deltaTime);
  });

  piece.whenClickedOn(() => {
    splitPiece(piece, size, depth);
  });

  return piece;
}

function splitPiece(piece: waveCube, size: number, depth: number): void {
  if (piece.isDestroyed || depth <= 0) return;

  const center = piece.getWorldPosition();
  const nextDepth = depth - 1;
  const childSize = size * childScale;
  const spread = childSize * childSpreadScale;

  piece.hide();
  piece.destroy();

  const offsets = [
    [-spread, -spread, -spread],
    [spread, -spread, -spread],
    [-spread, spread, -spread],
    [spread, spread, -spread],
    [-spread, -spread, spread],
    [spread, -spread, spread],
    [-spread, spread, spread],
    [spread, spread, spread],
  ];

  for (const [dx, dy, dz] of offsets) {
    const child = spawnPiece(
      center.x + dx,
      center.y + dy,
      center.z + dz,
      childSize,
      nextDepth
    );

    if (child) {
      child
        .moveBy({
          x: dx * splitPushScale,
          y: dy * splitPushScale,
          z: dz * splitPushScale,
        }, Animate)
        .over(splitAnimationDuration, Seconds)
        .play();
    }
  }

  scene.print(`Depth left: ${nextDepth}`);
}

spawnPiece(0, 2, 0, 3, maxDepth);
```

In the manual version, the recursive idea is the call chain:

1. `spawnPiece(...)` creates one cube.
2. The cube click calls `splitPiece(...)`.
3. `splitPiece(...)` creates children with `spawnPiece(...)`.
4. Each child gets the same click behavior.
5. `depth` and `maxPieces` stop the recursion before it overwhelms the scene.

### Remix Challenges

- Color pieces by depth with `PALETTE.randomFrom(...)` or a fixed palette array.
- Call `showBoundingBox()` on fragments while tuning the split distances.
- Add a sound or `waveFx` burst inside `boom(...)`.
- Stop recursion when the object is destroyed, the size is too small, or the
  scene reaches `maxPieces`.
- Replace the starting cube with `waveSphere`, `waveCapsule`, or a loaded model.

## Lesson 18: Advanced Gallery: Boss Projects

The demos in `wave-engine/demo/do-not-edit` show WaveStudio acting less like a
shape toy and more like a small creative engine. Use these as remixable recipes:
learn the smallest pattern, then open the source demo when you want the full
system.

Each boss project below follows the same teaching format: what you build,
smallest useful snippet, APIs to steal, natural-language constants, remix
challenges, and source demo reference.

### Boss Project 2: Draw-to-World Magic Ink

**What you build:** draw on a `waveUICanvas`, release the pointer, and turn the
stroke into a 3D `wavePath` on the floor.

**Smallest useful snippet:**

```ts
const drawingCanvas = new waveUICanvas();
drawingCanvas.setSize(360, 240);
drawingCanvas.setScreenPositionPixels(24, 24);

const worldPaths = new Map<string, wavePath>();

drawingCanvas
  .pointerDraw()
  .withColor(PALETTE.CANARY)
  .withWidth(6)
  .withOpacity(0.9)
  .smooth()
  .onFinish(({ stroke, pointCount }) => {
    if (pointCount < 2) return;

    const path = drawingCanvas
      .curvePath(stroke.id)
      .onXZPlane()
      .withScale(0.04)
      .centeredAt({ x: 0, y: 0.05, z: 0 })
      .withCurveSegments(24)
      .create();

    path.setColor(PALETTE.CANARY);
    worldPaths.set(stroke.id, path);
  })
  .enable();
```

**APIs to steal:** `waveUICanvas.pointerDraw`, `curvePath`, `wavePath`,
`followPath`, `onFinish`.

**Natural-language constants:** `PALETTE.CANARY`, `Seconds`, named centers such
as `WORLD_DRAW_CENTER`.

**Remix challenges:** make a car follow the drawn path, convert strokes into
spell trails, add erase mode, or spawn particles along each path.

**Source demo reference:** `wave-ui-canvas-pointer-drawing/main.ts`.

### Boss Project 3: Keyboard Driving Lab

**What you build:** a small car controlled by keyboard input, with debug helpers
that make orientation, position, and bounds visible.

**Smallest useful snippet:**

```ts
const car = new Cube()
  .placeAt(0, 0.5, 0)
  .setColor(PALETTE.BLUE);

car.setScale(1.6, 0.35, 2.6);
car.showDirection();
car.showPosition();
car.showBoundingBox();

car.whenHolding(Keyboard.W, () => car.moveForward(0.18));
car.whenHolding(Keyboard.S, () => car.moveBackward(0.1));
car.whenHolding(Keyboard.A, () => car.turnLeft(4));
car.whenHolding(Keyboard.D, () => car.turnRight(4));
```

**APIs to steal:** `whenPress`, `whenHolding`, `showDirection`, `showPosition`,
`showCoordinate`, `showBoundingBox`, camera follow patterns.

**Natural-language constants:** `Keyboard.W`, `Keyboard.Space`,
`PALETTE.BLUE`.

**Remix challenges:** add obstacles, a finish line, drifting, a minimap camera,
or health and lap counters with `waveUIText`.

**Source demo reference:** `runway-runner/main.ts` and the input lesson above.

### Boss Project 4: Tiny Solar System

**What you build:** a sun, planets, and a gravity field that can be visualized
with directional markers.

**Smallest useful snippet:**

```ts
const sunBody = new waveSphere(1.2, 32).placeAt(0, 5, 0);
sunBody.setColor(PALETTE.GOLDENROD);
sunBody.useStaticBody();

const planetBody = new waveSphere(0.45, 24).placeAt(8, 5, 0);
planetBody.setColor(PALETTE.CYAN);
planetBody.useDynamicBody();

const cometBody = new waveSphere(0.25, 16).placeAt(-12, 6, 0);
cometBody.setColor(PALETTE.WHITE);
cometBody.useDynamicBody();

const gravityField = scene
  .createField("solar-gravity")
  .asSphere(30)
  .anchorAt(sunBody)
  .affects(sunBody, planetBody, cometBody)
  .ignoreDefaultGravity()
  .addGravity({
    id: "sun-source-gravity",
    source: sunBody,
    gravityConstant: 9.8,
    collisionRadius: 2,
    forceLimit: 60,
  })
  .withVoxelGrid({
    voxelSize: 4,
    mode: WaveRangeVoxelFillMode.Center,
  });
```

**APIs to steal:** `scene.createField`, `asSphere`, `anchorAt`, `affects`,
`addGravity`, `withVoxelGrid`, `showDirection`.

**Natural-language constants:** `WaveRangeVoxelFillMode.Center`,
`PALETTE.GOLDENROD`, named values such as `GRAVITY_FIELD_RADIUS`.

**Remix challenges:** add a slingshot comet, show field arrows, make the sun
grow on click, or compare default gravity against field gravity.

**Source demo reference:** `gravity-force-field/main.ts`.

### Boss Project 5: Terraforming Spellbook

**What you build:** paint dirt roads, stamp brush circles, and dent terrain
where projectiles hit.

**Smallest useful snippet:**

```ts
const terrain = myScene.terrain;
const ROAD_LAYER = "dirt";
const startTerrainPoint = { x: -12, y: -4 };
const endTerrainPoint = { x: 14, y: 6 };
const impactPoint = { x: 0, y: 0 };

terrain
  .paint()
  .layer(ROAD_LAYER)
  .path()
  .along([startTerrainPoint, endTerrainPoint])
  .width(4)
  .strength(0.7)
  .falloff("smooth")
  .apply();

terrain.lowerCircle(impactPoint, 2.5, 0.4, {
  strength: 0.95,
  falloff: "smooth",
  jitter: 0.14,
  noiseScale: 0.12,
});
```

**APIs to steal:** `terrain.paint`, `layer`, `circle`, `path`, `terrain.author`,
`lowerCircle`, `noiseCircle`, `smoothCircle`, `terrain.query().heightAt`.

**Natural-language constants:** `ROAD_LAYER`, `PALETTE.SAGE`,
`PALETTE.GOLDENROD`, named brush sizes.

**Remix challenges:** build a road editor, crater spell, farming game, tank
tracks, or terrain restoration brush.

**Source demo reference:** `dirt-road-terrain/main.ts`.

### Boss Project 6: Physics Piano

**What you build:** each piano key is a dynamic rigid body connected by a hinge
joint, and pressing it triggers sound.

**Smallest useful snippet:**

```ts
const body = new waveCube(4, 0.08, 1.2);
body.placeAt(0, 1, 0);
body.rigidBody.asStatic().withBox().disableGravity();

const pianoAssembly = scene.createJointAssembly("physics-piano", body);

const key = new waveCube(0.25, 0.06, 1.2);
key.rigidBody
  .asDynamic()
  .autoFitFromBounds()
  .setMass(0.2)
  .disableGravity();

pianoAssembly
  .joint(body, key)
  .named("middle-c-hinge")
  .asHinge({ x: 1, y: 0, z: 0 })
  .withAngleRange(-12, 0)
  .build();
```

**APIs to steal:** `createJointAssembly`, `rigidBody.asDynamic`,
`autoFitFromBounds`, `joint`, `asHinge`, `createActuationTrigger`,
`audio.useInstrument`.

**Natural-language constants:** `ConstraintAxis.ANGULAR_X`,
`MotorMode.POSITION`, `RigidBodyActuationMetricKind.PitchAngle`.

**Remix challenges:** build a drum kit, pinball flippers, drawbridge, trapdoor,
or rhythm game.

**Source demo reference:** `physics-piano/main.ts`.

### Boss Project 7: Robot Arm Puzzle

**What you build:** click target cubes and let an articulated model move its
gripper toward them using an end-effector animation chain.

**Smallest useful snippet:**

```ts
const roboticArm = new wave3DObject(models.RoboticArm);
const GRIPPER_PART_NAME = "gripper";
const TARGET_IK_MOVE_SECONDS = 1.2;
const targetCube = new Cube().placeAt(2, 1, 0).setColor(PALETTE.GOLDENROD);

function tryPickUpTarget(target: waveCube) {
  target.setColor(PALETTE.GREEN);
}

const gripper = roboticArm.getPart(GRIPPER_PART_NAME);

if (gripper) {
  const distanceToTarget = gripper.distanceTo(targetCube);

  gripper.turnTo(targetCube);
  gripper
    .moveForward(distanceToTarget, Animate)
    .over(TARGET_IK_MOVE_SECONDS, Seconds)
    .play()
    .onComplete(() => tryPickUpTarget(targetCube));
}
```

**APIs to steal:** model parts, part metadata, the source demo's end-effector
helper, `getPart`, `turnTo`, `distanceTo`, `moveForward`, `onComplete`, click
target registries.

**Natural-language constants:** `GRIPPER_PART_NAME`,
`TARGET_IK_MOVE_SECONDS`, `Animate`, `Seconds`.

**Remix challenges:** sort colored cubes, stack blocks, build a claw machine,
or make the robot solve a memory puzzle.

**Source demo reference:** `articulated-robotic-arm/main.ts`.

### Boss Project 8: Particle Weather Machine

**What you build:** big expressive particle systems: dust wakes, rain, point
fields, fireworks, and particles that follow paths.

**Smallest useful snippet:**

```ts
const heroField = waveFx.emitter("emitter.heroPointField").pointField({
  count: 34000,
  bounds: { x: 38, y: 15, z: 14 },
  palette: [
    PALETTE.toNormalizedAlpha(PALETTE.CYAN),
    PALETTE.toNormalizedAlpha(PALETTE.VIOLET),
    PALETTE.toNormalizedAlpha(PALETTE.WHITE),
  ],
  streams: [
    {
      yOffset: 0,
      zOffset: 0,
      span: 1,
      phase: 0,
      yAmp: 0.25,
      zAmp: 0.2,
      yWave: 2,
      spin: 0.4,
      width: 0.35,
    },
  ],
});

const heroSystem = waveFx.system("system.heroField", [heroField]);
waveFx.register(engine, heroSystem);
```

**APIs to steal:** `waveFx.emitter`, `pointField`, `burst`, `motion`,
`followPath`, `alphaOverLife`, `sizeOverLife`, `waveFx.system`.

**Natural-language constants:** `PALETTE.CYAN`, `PALETTE.VIOLET`, named emitter
ids such as `emitter.heroPointField`.

**Remix challenges:** make a storm controller, fireworks sequencer, engine
exhaust, magic portal, or path-following swarm.

**Source demo reference:** `wavefx-builder/main.ts`.

### Boss Project 9: Voice/Gesture Character Controller

**What you build:** speech commands, webcam hand gestures, and microphone snap
detection that control a character.

**Smallest useful snippet:**

```ts
const speechCommandSpecs = [
  { name: "jump", aliases: ["jump", "hop"] },
  { name: "wave", aliases: ["wave", "hello"] },
];

function handleSpeechCommand(match: WaveSpeechCommandMatch) {
  scene.print(`voice: ${match.command}`);
}

function handleGestureCommand(name: VisionGestureName | string) {
  scene.print(`gesture: ${name}`);
}

const recognition = scene.director.sensing
  .speechCommands()
  .withCommands(speechCommandSpecs)
  .withLanguage("en-US")
  .withContinuousListening()
  .withInterimTranscripts()
  .withMinScore(0.68)
  .onCommand(handleSpeechCommand)
  .startListening();

async function startHandTracking() {
  await scene.director.sensing
    .webcamHands()
    .withFacingMode("user")
    .withGestureRecognition()
    .withMaxHands(1)
    .onGesture((data) => handleGestureCommand(data.name))
    .startTracking();
}

startHandTracking();
```

**APIs to steal:** `director.sensing.speechCommands`,
`director.sensing.webcamHands`, `director.audio.analyzeMicrophone`,
`withGestureRecognition`, `onCommand`, `onGesture`, `onSnap`.

**Natural-language constants:** `"en-US"`, named command specs, named gesture
handlers such as `handleGestureCommand`.

**Remix challenges:** voice-controlled robot, hand-pose spell casting,
snap-to-switch-lights, or accessibility controls for a game.

**Source demo reference:** `amy-voice-gesture/main.ts`.

### Boss Project 10: Geometry Surgery

**What you build:** fracture meshes, stamp decals, deform surfaces, and combine
or cut geometry.

**Smallest useful snippet:**

```ts
const ring = new Torus().placeAt(0, 1.5, 0);

const result = ring.model
  .fragmenting()
  .intoPieces(5)
  .withInteriorColor(PALETTE.GRAY)
  .withSeed(1337)
  .apply();

if (result.success) {
  result.fragments.forEach((fragment) => {
    fragment.setColor(PALETTE.randomFrom([
      PALETTE.CYAN,
      PALETTE.GOLDENROD,
      PALETTE.CORAL,
    ]));
  });
}
```

**APIs to steal:** `model.fragmenting`, `model.deforming`, `addDecal`,
`material`, `unioning`, `subtracting`, `intersecting`, `forceMergeMesh`.

**Natural-language constants:** `WaveSurface.Up`, `PALETTE.GRAY`, named seeds
for repeatable cuts.

**Remix challenges:** make a wall that dents when hit, a graffiti system, a
sculpting brush, or a CSG puzzle where players carve keys from blocks.

**Source demo reference:** `mesh-sculpting/main.ts` and
`vertex-color-pbr-painting/main.ts`.

### Boss Project 11: Asteroid Evader

**What you build:** a keyboard-controlled spaceship, moving asteroids, laser
shots, score/lives HUD, and recursive-style asteroid breakup. Large asteroids
turn into small fragments; fragments fade away over time.

**Smallest useful snippet:**

```ts
type GameProjectile = {
  body: waveSphere;
  age: number;
  lifetime: number;
};

type GameAsteroid = {
  body: waveSphere;
  radius: number;
  speed: number;
  driftX: number;
  driftY: number;
  spin: number;
  small: boolean;
  age: number;
  lifetime: number;
};

const arenaHalfWidth = 8;
const arenaMinY = 0.8;
const arenaMaxY = 5.6;
const asteroidSpawnZ = 9;
const asteroidExitZ = -10;
const shipZ = -7;
const shipMoveSpeed = 5;
const laserSpeed = 18;
const laserLifetime = 1.2;
const fireCooldown = 0.18;
const shipHitRadius = 0.75;
const laserRadius = 0.18;

let score = 0;
let lives = 3;
let spawnTimer = 0;
let canFire = true;
let gameOver = false;

const projectiles: GameProjectile[] = [];
const asteroids: GameAsteroid[] = [];

scene.camera
  .orbit({
    target: { x: 0, y: 2.6, z: 0 },
    distance: 17,
    fov: 58,
  })
  .apply();

scene.lighting
  .ambient({ color: PALETTE.SKY, intensity: 0.45 })
  .sun({
    direction: { x: -1, y: -2, z: -1 },
    intensity: 1.5,
    shadowEnabled: true,
  })
  .apply();

const hudBackground = PALETTE.modifyAlphaChannel(PALETTE.BLACK, 55);
const scoreText = new waveUIText();
scoreText.setFontSize(24);
scoreText.setColor(PALETTE.WHITE);
scoreText.setBackgroundColor(hudBackground);
scoreText.setSize(330, 46);
scoreText.setScreenPositionPixels(24, 24);

const helpText = new waveUIText();
helpText.setText("Click preview, then WASD move | Space fire");
helpText.setFontSize(18);
helpText.setColor(PALETTE.CYAN);
helpText.setScreenPositionPixels(24, 76);

const playfield = new waveGround(18, 22, 4);
playfield.placeAt(0, -0.05, 0);
playfield.setColor(PALETTE.CHARCOAL);
playfield.setOpacity(0.35);

const ship = new wave3DObject(models.Spaceship);
ship.placeAt(0, 2.7, shipZ);
ship.setUniformScale(0.35);
ship.showDirection({ length: 1.5 });

function updateHud() {
  scoreText.setText(`Score: ${score}   Lives: ${lives}`);
}

function clamp(value: number, min: number, max: number) {
  return Math.max(min, Math.min(max, value));
}

function placeShipInArena(x: number, y: number) {
  const nextX = clamp(x, -arenaHalfWidth, arenaHalfWidth);
  const nextY = clamp(y, arenaMinY, arenaMaxY);
  ship.placeAt(nextX, nextY, shipZ);
}

function updateShipControls(deltaTime: number) {
  let inputX = 0;
  let inputY = 0;

  if (myScene.director.isKeyPressed(Keyboard.A)) inputX -= 1;
  if (myScene.director.isKeyPressed(Keyboard.D)) inputX += 1;
  if (myScene.director.isKeyPressed(Keyboard.W)) inputY += 1;
  if (myScene.director.isKeyPressed(Keyboard.S)) inputY -= 1;

  if (inputX === 0 && inputY === 0) return;

  const diagonalScale = inputX !== 0 && inputY !== 0 ? 0.707 : 1;
  const step = shipMoveSpeed * deltaTime * diagonalScale;
  const position = ship.position;
  placeShipInArena(position.x + inputX * step, position.y + inputY * step);
}

function clampShipToArena() {
  const position = ship.position;
  const x = clamp(position.x, -arenaHalfWidth, arenaHalfWidth);
  const y = clamp(position.y, arenaMinY, arenaMaxY);
  ship.placeAt(x, y, shipZ);
}

function randomRange(min: number, max: number) {
  return min + Math.random() * (max - min);
}

function spawnAsteroid() {
  const radius = randomRange(0.55, 1.05);
  const asteroid = new waveSphere(radius, 12);
  asteroid.placeAt(
    randomRange(-arenaHalfWidth, arenaHalfWidth),
    randomRange(arenaMinY, arenaMaxY),
    asteroidSpawnZ
  );
  asteroid.setColor(PALETTE.darken(PALETTE.LIGHT_GRAY, 20));
  asteroid.setRoughness(0.9);
  asteroid.addTag("asteroid");

  asteroids.push({
    body: asteroid,
    radius,
    speed: randomRange(2.2, 4.2),
    driftX: randomRange(-0.8, 0.8),
    driftY: randomRange(-0.25, 0.25),
    spin: randomRange(45, 130),
    small: false,
    age: 0,
    lifetime: 8,
  });
}

function spawnShard(center: { x: number; y: number; z: number }, angleDegrees: number) {
  const shardRadius = randomRange(0.16, 0.28);
  const radians = angleDegrees * Math.PI / 180;
  const shard = new waveSphere(shardRadius, 8);
  shard.placeAt(
    center.x + Math.cos(radians) * 0.5,
    center.y + Math.sin(radians) * 0.3,
    center.z
  );
  shard.setColor(PALETTE.SILVER);
  shard.setOpacity(0.85);
  shard.setRoughness(0.8);
  shard.addTag("asteroid-fragment");

  asteroids.push({
    body: shard,
    radius: shardRadius,
    speed: randomRange(1.5, 2.6),
    driftX: Math.cos(radians) * randomRange(0.8, 1.8),
    driftY: Math.sin(radians) * randomRange(0.3, 0.9),
    spin: randomRange(120, 260),
    small: true,
    age: 0,
    lifetime: randomRange(1.4, 2.4),
  });
}

function removeProjectile(index: number) {
  const projectile = projectiles[index];
  if (!projectile.body.isDestroyed) projectile.body.destroy();
  projectiles.splice(index, 1);
}

function removeAsteroid(index: number) {
  const asteroid = asteroids[index];
  if (!asteroid.body.isDestroyed) asteroid.body.destroy();
  asteroids.splice(index, 1);
}

function pulverizeAsteroid(index: number) {
  const asteroid = asteroids[index];
  const center = {
    x: asteroid.body.position.x,
    y: asteroid.body.position.y,
    z: asteroid.body.position.z,
  };
  asteroid.body.fx.play(waveFxPresets.impactSparks());
  removeAsteroid(index);

  if (asteroid.small) {
    score += 1;
    updateHud();
    return;
  }

  score += 5;
  updateHud();

  for (let i = 0; i < 7; i++) {
    spawnShard(center, i * 360 / 7);
  }
}

function fireLaser() {
  if (!canFire || gameOver) return;
  canFire = false;

  const laser = new waveSphere(0.12, 12);
  const shipPosition = ship.position;
  laser.placeAt(shipPosition.x, shipPosition.y, shipPosition.z + 0.8);
  laser.setColor(PALETTE.CYAN);
  laser.setEmissiveColor(PALETTE.CYAN);
  laser.setEmissiveIntensity(2.5);
  laser.addTag("laser");

  projectiles.push({
    body: laser,
    age: 0,
    lifetime: laserLifetime,
  });

  ship.after(fireCooldown, Seconds).do(() => canFire = true);
}

function damageShip() {
  lives -= 1;
  updateHud();
  ship.setColor(PALETTE.CORAL);
  ship.after(0.2, Seconds).do(() => ship.setColor(PALETTE.WHITE));

  if (lives <= 0) {
    gameOver = true;
    scene.print("Game over: press Run to restart", 30, PALETTE.WHITE);
  }
}

function updateProjectiles(deltaTime: number) {
  for (let i = projectiles.length - 1; i >= 0; i--) {
    const projectile = projectiles[i];
    projectile.age += deltaTime;
    projectile.body.moveBy({ x: 0, y: 0, z: laserSpeed * deltaTime });

    if (projectile.age > projectile.lifetime || projectile.body.position.z > asteroidSpawnZ + 2) {
      removeProjectile(i);
    }
  }
}

function updateAsteroids(deltaTime: number) {
  for (let i = asteroids.length - 1; i >= 0; i--) {
    const asteroid = asteroids[i];
    asteroid.age += deltaTime;
    asteroid.body.moveBy({
      x: asteroid.driftX * deltaTime,
      y: asteroid.driftY * deltaTime,
      z: -asteroid.speed * deltaTime,
    });
    asteroid.body.turnRight(asteroid.spin * deltaTime);

    if (asteroid.small) {
      const fade = Math.max(0, 1 - asteroid.age / asteroid.lifetime);
      asteroid.body.setOpacity(fade);
    }

    if (asteroid.age > asteroid.lifetime || asteroid.body.position.z < asteroidExitZ) {
      removeAsteroid(i);
    }
  }
}

function checkLaserHits() {
  for (let p = projectiles.length - 1; p >= 0; p--) {
    const projectile = projectiles[p];

    for (let a = asteroids.length - 1; a >= 0; a--) {
      const asteroid = asteroids[a];
      const hitDistance = asteroid.radius + laserRadius;

      if (projectile.body.distanceTo(asteroid.body) <= hitDistance) {
        removeProjectile(p);
        pulverizeAsteroid(a);
        break;
      }
    }
  }
}

function checkShipHits() {
  for (let i = asteroids.length - 1; i >= 0; i--) {
    const asteroid = asteroids[i];
    const hitDistance = asteroid.radius + shipHitRadius;

    if (ship.distanceTo(asteroid.body) <= hitDistance) {
      removeAsteroid(i);
      damageShip();
    }
  }
}

myScene.director.whenPress(Keyboard.Space, fireLaser);

ship.onTick((_self, deltaTime) => {
  if (gameOver) return;

  updateShipControls(deltaTime);
  clampShipToArena();

  spawnTimer -= deltaTime;
  if (spawnTimer <= 0) {
    spawnAsteroid();
    spawnTimer = randomRange(0.55, 1.05);
  }

  updateProjectiles(deltaTime);
  updateAsteroids(deltaTime);
  checkLaserHits();
  checkShipHits();
});

for (let i = 0; i < 5; i++) {
  spawnAsteroid();
}

updateHud();
```

This version polls `myScene.director.isKeyPressed(...)` inside the frame loop,
which is usually more dependable for a copied game snippet than attaching
movement to object-scoped `whenHolding` callbacks.

**APIs to steal:** `wave3DObject`, `models.Spaceship`,
`myScene.director.isKeyPressed`, `myScene.director.whenPress`, `onTick`,
`waveUIText`, `distanceTo`, `moveBy`, `setOpacity`,
`waveFxPresets.impactSparks`, `after`, `Seconds`, and array-backed game state.

**Natural-language constants:** `Keyboard.W`, `Keyboard.Space`,
`PALETTE.CYAN`, `PALETTE.CORAL`, `Seconds`, and named tuning values such as
`laserSpeed`, `fireCooldown`, and `shipHitRadius`.

**Remix challenges:** add a start screen, save high score with `myCloud`, make
asteroids split twice, add shield pickups, use a real asteroid model, or add a
boss asteroid with a health bar.

**Source demo reference:** combines ideas from `runway-runner/main.ts`,
`shooting-range/main.ts`, and Boss Project 1's recursive-fragment mindset.

## Lesson 19: Demo Atlas: 30 Project Seeds from `wave-engine`

The boss projects above teach a few patterns in depth. The atlas below is the
next layer: every extracted demo becomes a project prompt. Treat each row as a
small product idea you can build toward, not just a file to read.

| Demo | Project seed | APIs and ideas to study | Remix challenge |
| --- | --- | --- | --- |
| `amy-vat-crowd` | **Dance-Crowd Stress Test** | `playAnimation`, shared timing, `waveInstanceMesh`, procedural GPU animation. | Build a concert crowd where rows react to music or keyboard cues. |
| `amy-voice-gesture` | **Talk-to-Amy Stage Director** | Speech commands, webcam hands, microphone snap analysis, character lighting. | Let voice pick animation states and hand gestures move the performer. |
| `animal-signal-cartographer` | **Animal Signal Radar** | `waveUICanvas`, canvas sprites, `waveUIImage`, animal tags, animated signal arcs. | Make a wildlife tracker where clicking an animal updates a live radar panel. |
| `articulated-robotic-arm` | **Factory Arm Pick-and-Place** | Model parts, target cubes, `getPart`, end-effector helper patterns, completion callbacks. | Turn it into a color-sorting factory puzzle. |
| `collision-stress-lab` | **Physics Crash Museum** | `rigidBody.asStatic`, `asDynamic`, convex hulls, compound colliders, collision callbacks. | Build a test hall where each exhibit teaches one collider mistake. |
| `colored-light-projection` | **Prism Light Theater** | Spotlights, `setProjectionPreset`, translucent materials, refraction, planar reflections. | Make a stage where keys cycle caustics, gradients, and stained-glass beams. |
| `dirt-road-terrain` | **Mud Road Terraformer** | `terrain.paint`, splat layers, road paths, crater authoring, reset tools. | Build a road editor with brush size, flatten, roughen, and restore modes. |
| `gravity-force-field` | **Vector Physics Planetarium** | `createField`, gravity sources, voxel grids, `wavePoint.showDirection`. | Make a gravity sandbox where arrows explain every invisible force. |
| `material-surface-setter-probe-grid` | **Surface Material Inspector** | `WaveSurface`, face materials, probe grids, material setters. | Click cube faces to paint named surfaces instead of whole objects. |
| `mesh-sculpting` | **Geometry Surgery Room** | Fragmenting, CSG, vertex editing, point nets, mesh repair patterns. | Build a sculpting classroom with one station per mesh operation. |
| `multipart-demo-with-vfx` | **Creature FX Sandbox** | Multipart models, ranges, keyboard control, effects bound to moving parts. | Make a boss creature with weak spots, sparks, and staged attacks. |
| `nme-ocean-showcase` | **Ocean Island Diorama** | Terrain height controls, water/ocean scene composition, atmospheric tuning. | Add boats, buoys, and weather presets that change the mood. |
| `physics-cube-modes` | **Rigid Body Mode Playground** | Static/dynamic/kinematic modes, UI buttons, sliders, grid placement. | Let learners switch body modes and immediately see how motion changes. |
| `physics-piano` | **Hinged Instrument Lab** | Joint assemblies, hinge constraints, actuation triggers, audio instruments. | Build drums, bells, pinball flippers, or a rhythm training game. |
| `playground-3d` | **Placement Playground** | `placeOnPath`, `placeOnSurfaceOf`, `waveThinBatch`, terrain snapping, UI panels. | Make a level editor where learners scatter props along drawn routes. |
| `rigging-workshop` | **Humanoid Rig Studio** | `waveHumanoid`, transform authoring tools, rig controls, camera and terrain staging. | Add sliders that pose arms, head, spine, and debug markers live. |
| `room-color-lightshow` | **Programmable Room Lightshow** | Keyboard-driven lights, projection presets, room materials, color palettes. | Build a DJ light board with named scenes like warm, storm, neon, alarm. |
| `runway-runner` | **Endless Runner Course** | Game states, spawning segments, HUD hearts, jump impulse, camera follow. | Turn it into a full lesson on game loops, failure states, and score. |
| `sheep-migration` | **Pathfinding Farm** | `placeInGrid`, grid cells, destroyed obstacles, `pathfind`, `followPath`. | Visualize maze algorithms by making animals walk the computed route. |
| `shooting-range` | **Multi-Camera Shuriken Range** | Projectiles, target registry, recursive fragments, picture-in-picture cameras. | Add four camera modes and make every broken target become a new target. |
| `text-renderer` | **3D Typography Fireworks** | Text rendering, `waveFx.emitter().text`, keyboard-triggered word effects. | Let users type a word and explode it into particles. |
| `thin-client-rendering` | **Thin Client Harness** | Minimal runtime shell and remote/thin rendering structure. | Use it as the smallest place to test loading and scene boot behavior. |
| `thinbatch-gpu-pathfollow` | **GPU Path Parade** | `waveThinBatch`, `wavePath`, path-following batches, GPU-scale motion. | Create a parade of thousands of objects following braided paths. |
| `tracked-car-spotlight` | **Night Patrol Car** | Vehicle tracking, `waveLight`, spotlights, ranges, terrain awareness. | Build a stealth game where patrol cones reveal hidden targets. |
| `urban-occlusion-culling` | **Performance City** | Procedural city blocks, fade transitions, occlusion toggles, window effects. | Teach performance by showing the same city with culling on and off. |
| `vertex-color-pbr-painting` | **Living Graffiti Wall** | Vertex painting, texture painting, decals, material layers, mesh deformation. | Let clicks paint, stamp, dent, fade, and restore surfaces. |
| `visual-piano` | **UI Piano Composer** | Visual keys, UI controls, dropdowns, inputs, instrument/audio state. | Build a composition toy that records notes and replays patterns. |
| `wave-range-crystal-basin` | **Crystal Range Scanner** | `createRange`, terrain placement, `waveThinBatch`, lighting and sky mood. | Show range membership by lighting crystals inside a moving scanner. |
| `wave-ui-canvas-pointer-drawing` | **Draw-to-World Tool** | Pointer drawing, curve conversion, `wavePath`, erase/reset flows. | Turn sketches into roads, roller coasters, spell paths, or patrol routes. |
| `wavefx-builder` | **Particle Weather Machine** | Point fields, bursts, rain, fire, text strikes, path-following particles. | Build a weather console with buttons for sparks, rain, dust, and fireworks. |

### Atlas Starter: Pathfinding Farm

This is the learning shape behind `sheep-migration`: make a grid, choose a
target, compute a route, then animate a character along that route.

```ts
const brickGrid = scene.create3DGrid<waveCube>("farm-grid");
brickGrid.configureGrid({
  origin: { x: -2, y: 0.5, z: -2 },
  rows: 3,
  cols: 3,
  layers: 1,
  plane: "xz",
  rowSpacing: 1.5,
  colSpacing: 1.5,
  layerSpacing: 1,
});

for (let row = 0; row < 3; row++) {
  for (let col = 0; col < 3; col++) {
    const brick = new waveCube(1, 0.2, 1);
    brick.placeAt(brickGrid.getCellWorldPosition(row, col, 0));
    brickGrid.setAt(row, col, 0, brick);
  }
}

const startCell = { row: 0, col: 0 };
const targetBrick = brickGrid.getCell(2, 2, 0);
const sheep = new wave3DObject(models.Sheep).placeAt(-2, 1, -2);

targetBrick.whenClickedOn(() => {
  if (!targetBrick) return;

  const points = brickGrid
    .pathfind()
    .fromCell(startCell.row, startCell.col, 0)
    .to(targetBrick)
    .asWorldPoints();

  sheep.animator.followPath(points, 4, { turnToFace: true });
});
```

Remix it into A-star visualization, maze games, tower-defense routing, or an
algorithm lesson where every visited cell lights up.

### Atlas Starter: Light Projection Stage

This is the learning shape behind `colored-light-projection` and
`room-color-lightshow`: a light is not just illumination; it can be a projected
texture, a moving gradient, or a controlled stage cue.

```ts
const projector = new waveLight()
  .asSpotlight()
  .setLightColor(PALETTE.WHITE)
  .setIntensity(8)
  .setAngle(50)
  .setRange(40)
  .setProjectionPreset({
    type: "gradient",
    config: {
      colorStops: [
        { position: 0, color: PALETTE.CYAN },
        { position: 0.5, color: PALETTE.FUCHSIA },
        { position: 1, color: PALETTE.GOLDENROD },
      ],
      direction: "radial",
      animationMode: GradientAnimationMode.Scroll,
      gpuDriven: true,
    },
  });

```

Remix it into a concert rig, museum installation, puzzle room, alarm system, or
shader/color-theory lesson.

### Atlas Starter: Performance City Toggle

This is the learning shape behind `urban-occlusion-culling`: make performance
visible by giving learners a switch they can understand.

```ts
let cullingEnabled = false;

scene.director.whenPress(Keyboard.O, () => {
  cullingEnabled = !cullingEnabled;

  if (cullingEnabled) {
    scene.occlusion.enable({
      strategy: "optimistic",
      algorithm: "conservative",
    });
    scene.print("Occlusion culling enabled");
  } else {
    scene.occlusion.disable();
    scene.print("Occlusion culling disabled");
  }
});
```

Remix it into a performance lab with FPS labels, building fade masks, window
flicker, and before/after camera tours.

### Atlas Starter: Crowd Animation Lab

This is the learning shape behind `amy-vat-crowd`: give one leader an animation
style, then reuse the same timing across many performers.

```ts
const leader = new wave3DObject(models.Amy);
leader.placeAt(0, 0, 0);

const crowd = [
  new wave3DObject(models.Amy).placeAt(-2, 0, 0),
  new wave3DObject(models.Amy).placeAt(2, 0, 0),
];

leader.playAnimation(animations.Walking, {
  loop: true,
  speed: 1,
});

for (const performer of crowd) {
  performer.playAnimation(animations.Walking, {
    loop: true,
    speed: 1,
  });
}
```

Remix it into a parade, dance floor, stadium, school hallway, or background
population system.

### Atlas Starter: Voice and Gesture Stage Director

This is the learning shape behind `amy-voice-gesture`: browser permissions
become part of the creative interface. Speech can trigger animation, hands can
steer focus, and microphone analysis can drive stage effects.

```ts
const amy = new wave3DObject(models.Amy).placeAt(0, 0, 0);
const supportBox = new waveCube(1, 1, 1).placeAt(2, 1, 0);

const speechCommandSpecs = [
  { name: "dance", aliases: ["dance", "spin"], color: PALETTE.VIOLET },
  { name: "wave", aliases: ["wave", "hello"], color: PALETTE.PINK },
  { name: "boom", aliases: ["boom", "explode"], color: PALETTE.RED },
];

myScene.director.sensing
  .speechCommands()
  .withCommands(speechCommandSpecs)
  .withLanguage("en-US")
  .withContinuousListening()
  .withInterimTranscripts()
  .withMinScore(0.68)
  .onCommand((match) => {
    if (match.command === "dance") amy.playAnimation(ASSET_WAREHOUSE.animations.Crazy);
    if (match.command === "wave") amy.playAnimation(ASSET_WAREHOUSE.animations.Waving);
    if (match.command === "boom") supportBox.exploding().intoPieces(18).withStrength(34).apply();
  })
  .startListening();

const startHands = async () => {
  await myScene.director.sensing
    .webcamHands()
    .withFacingMode("user")
    .withGestureRecognition()
    .withMaxHands(1)
    .onGesture(({ name }) => {
      if (name === VisionGestureName.OpenPalm) {
        amy.playAnimation(ASSET_WAREHOUSE.animations.Waving);
      }
    })
    .startTracking();
};
```

Remix it into a voice-controlled actor, accessibility interface, gesture magic
system, rhythm game, or classroom demo about browser sensors.

### Atlas Starter: Terraforming Spell Brush

This is the learning shape behind `dirt-road-terrain`,
`wave-range-crystal-basin`, and `nme-ocean-showcase`: create ranges, then apply
terrain paint, sculpting, erosion, water, and road strokes inside those ranges.

```ts
const terrain = myScene.terrain;
const craterRange = myScene
  .createRange("craterBrush")
  .anchorAt({ x: 0, y: 0, z: 0 })
  .asSphere(24)
  .showHelper();

terrain.sculpt()
  .noise()
  .around({ x: 0, y: 0 }, 24)
  .amplitude(1.8)
  .frequency(0.045)
  .octaves(2)
  .apply();

terrain.paint()
  .layer("dirt")
  .path()
  .along([{ x: -18, y: -8 }, { x: 0, y: 4 }, { x: 22, y: 1 }])
  .width(6)
  .strength(0.92)
  .falloff("smooth")
  .apply();

terrain.erode()
  .around({ x: 0, y: 0 }, 24)
  .preset("alpine")
  .strength(0.72)
  .apply();
```

Remix it into a terrain painter, road builder, crater spell, golf course
designer, island generator, or explainable geology toy.

### Atlas Starter: Vector Physics Planetarium

This is the learning shape behind `gravity-force-field`: a force field is easier
to teach when you draw the invisible vectors.

```ts
const ENGINE_GRAVITY_CONSTANT = 8;

const sunBody = new waveSphere(1.4, 32).placeAt(0, 24, 0);
sunBody.setColor(PALETTE.GOLDENROD);
sunBody.useStaticBody();

const planetBody = new waveSphere(0.45, 24).placeAt(18, 28, 0);
planetBody.setColor(PALETTE.CYAN);
planetBody.useDynamicBody();

const cometBody = new waveSphere(0.3, 16).placeAt(-18, 32, 0);
cometBody.setColor(PALETTE.WHITE);
cometBody.useDynamicBody();

const gravityField = myScene
  .createField("solarField")
  .asSphere(42)
  .anchorAt(sunBody)
  .affects(sunBody, planetBody, cometBody)
  .ignoreDefaultGravity()
  .addGravity({
    id: "sunGravity",
    source: sunBody,
    gravityConstant: ENGINE_GRAVITY_CONSTANT,
    collisionRadius: 2.25,
    forceLimit: 8,
  })
  .withVoxelGrid({
    voxelSize: 6,
    mode: WaveRangeVoxelFillMode.Center,
  });

const samplePoint = new Vector3(18, 28, 0);
const sample = gravityField.sampleAt(samplePoint, { targetMass: 1 });

new wavePoint()
  .placeAt(samplePoint)
  .showDirection(sample.netForce, {
    showLabels: false,
    coordinateSpace: WaveSpace.WORLD,
  });
```

Remix it into a tiny solar system, magnet puzzle, wind tunnel, steering field,
or "why did the comet curve?" physics lesson.

### Atlas Starter: Hinged Physics Piano

This is the learning shape behind `physics-piano`: a key is a dynamic rigid
body, a hinge joint constrains it, and an actuation trigger turns motion into
sound.

```ts
const body = new waveCube(4, 0.05, 0.6).placeAt(0, 1, 0);
body.rigidBody.asStatic().withBox().disableGravity();

const key = new waveCube(0.18, 0.05, 0.8).placeAt(0, 1.1, 0.15);
key.rigidBody
  .asDynamic()
  .autoFitFromBounds()
  .setMass(0.032)
  .setAngularDamping(1)
  .disableGravity();

const pianoAssembly = myScene.createJointAssembly("physicsPiano", body);
pianoAssembly
  .joint(body, key)
  .named("middleCHinge")
  .asHinge({ x: 1, y: 0, z: 0 })
  .atPivots({ x: 0, y: 0.02, z: -0.4 }, { x: 0, y: 0.02, z: -0.4 })
  .withAngleRange(-0.095, 0)
  .withMotor(ConstraintAxis.ANGULAR_X, MotorMode.POSITION, 0, 0.012)
  .build();

key.rigidBody
  .createActuationTrigger({
    id: "middleC",
    metric: { kind: RigidBodyActuationMetricKind.PitchAngle, space: WaveSpace.PARENT },
    actuateWhen: { crosses: RigidBodyActuationCrossing.Above, value: 1.35 },
    rearmWhen: { crosses: RigidBodyActuationCrossing.Below, value: 0.35 },
    requireImpact: true,
  })
  .onActuate(() => key.playSound(audios.studioSound, { volume: 1 }));
```

Remix it into drums, bells, pinball flippers, keyboard training, a music puzzle,
or a machine where every collision becomes a note.

### Atlas Starter: Vehicle Control Lab

This is the learning shape behind `wavefx-builder`, `tracked-car-spotlight`, and
`multipart-demo-with-vfx`: combine keyboard input, physics or kinematic motion,
debug helpers, and a camera that follows the player.

```ts
const car = new waveActor();
car.useModel(ASSET_WAREHOUSE.models.Toyoyo_Highlight);
car.resetOrigin();
car.transform.swapLeftForward().swapFrontBack();
car.placeAt(0, 1, 0);

car.movementController.useVehicleScheme({
  baseSpeed: 30,
  acceleration: 12,
  brakeStrength: 20,
  steeringSensitivity: 0.05,
});

car.useKinematicBody();
car.camera.possess().followFrom(car.backward, 10, 25).activate();
car.showDirection({ length: 2 });
car.showPosition();

myScene.director.whenPress(Keyboard.Escape, () => {
  car.camera.deactivate();
});
```

Remix it into a driving game, stealth patrol, chase camera lab, VFX car toy,
aircraft controller, or keyboard-input lesson.

### Atlas Starter: Texture-to-3D Pixel Painting

This is the learning shape behind the `sampleTexture` idea in
`vertex-color-pbr-painting`: load an image into a hidden UI canvas, sample its
pixels, then turn those samples into a grid of tiny 3D objects. It teaches
texture data, instancing, grid placement, and per-cell callbacks all at once.

Start with `60 x 40` while experimenting. Then scale up to `240 x 120` when you
want the full sculpture.

```ts
myScene.sky.backgroundColor(COLOR.WHITE);

const board = new waveUICanvas()
  .setVisible(false)
  .loadTexture(textures.FarmGirlHappy);

const pictureGrid = board.sampleTexture(240, 120);

const pixelAsset = new Sphere(0.08).createInstanceSource();
const pixel = new waveInstanceMesh().useModel(pixelAsset.assetName);

const painting = myScene
  .placeInGrid(pixel)
  .hideOriginal()
  .at(pixel.position)
  .withRows(240)
  .withCols(120)
  .withSpacing(0.08)
  .onXZPlane()
  .onEachCell((pixelEntity, row, col) => {
    const sample = pictureGrid.cell(row, col);
    const luminance = sample.luminance;

    if (luminance > 0.99) {
      pixelEntity.destroy();
      return;
    }

    pixelEntity.setColor({
      r: luminance,
      g: luminance * 0.3,
      b: luminance,
    });

    pixelEntity.moveUp(luminance * 3);
  })
  .place();
```

APIs to steal: `waveUICanvas.loadTexture`, `sampleTexture`, `cell(row, col)`,
`createInstanceSource`, `waveInstanceMesh`, `placeInGrid`, `hideOriginal`, and
`onEachCell`.

Remix it into image relief sculpture, pixel terrain, bead art, height-map
portraits, music-reactive mosaics, or a lesson on how images become data.

### Atlas Starter: Tabletop Still Life Composer

This is the learning shape behind expressive prop staging: stop hand-computing
Y positions and use semantic placement. `placeAbove(table)` reads like the
intention, and `placeAround(table)` makes circular layouts teachable.

```ts
const table = new Prop(models.Table_Large_Circular)
  .enlargeBy(5)
  .placeAbove(myScene.terrain);

const vase = new Prop(models.vase_de_Nesle)
  .shrinkBy(170)
  .placeAbove(table)
  .moveLeft(1);

const book = new Prop(models.Books)
  .enlargeBy(7)
  .placeAbove(table)
  .moveRight(1);

const fruit = new Prop(models.Fruit_Bowl)
  .enlargeBy(7)
  .placeAbove(table)
  .moveBackward(1.7);

const chair = new Prop(models.Chair).enlargeBy(5);

myScene
  .placeAround(table)
  .with(chair)
  .inTotal(3)
  .inRadius(3)
  .onPerimeter()
  .includingOriginal()
  .faceCenter()
  .place();
```

APIs to steal: `Prop`, `models.*`, `placeAbove`, `moveLeft`, `moveRight`,
`moveBackward`, `placeAround`, `inTotal`, `inRadius`, `onPerimeter`,
`includingOriginal`, and `faceCenter`.

Remix it into room staging, a restaurant table generator, classroom seating,
museum displays, inventory layouts, or a cozy scene-building exercise.

## Lesson 20: Complete Object Catalog

`waveStudio-globals.d.ts` declares hundreds of classes. Not every declared class
is meant to be the first thing a learner writes; many are option objects,
Babylon.js/WebXR types, physics internals, or helper builders. The lists below
cover the scene-facing objects you normally instantiate or call from WaveStudio
TypeScript.

### All 3D Geometry Classes

The 26 `wave...` classes below extend `waveGeometry`, so they share transforms,
materials, physics, animation playback, sounds, click/hover input, cloning,
part creation, and `explode` / `exploding`. `waveBox` is also available as an
alias of `waveCube`.

| Class | Constructor | Good for |
| --- | --- | --- |
| `waveCube` | `(width?, height?, depth?, widthSegments?, heightSegments?, depthSegments?)` | Boxes, platforms, voxels, walls. |
| `waveBox` | Alias of `waveCube` | Same as `waveCube`. |
| `waveSphere` | `(radius?, segments?)` | Balls, planets, sensors, smooth markers. |
| `waveCylinder` | `(height?, diameterTop?, diameterBottom?, tessellation?, heightSegments?)` | Pillars, barrels, pipes, tapered tubes. |
| `waveCapsule` | `(radius?, height?, tessellation?, heightSegments?, capSubdivisions?)` | Characters, rounded collision bodies, pills. |
| `waveCone` | `(height?, diameter?, tessellation?, heightSegments?)` | Cones, arrows, spikes, markers. |
| `waveTorus` | `(radius?, tubeRadius?, tessellation?)` | Rings, portals, tires, handles. |
| `waveTorusKnot` | `(radius?, tubeRadius?, tessellation?, p?, q?)` | Knotted rings and mathematical shapes. |
| `wavePlane` | `(width?, height?, doubleSided?, widthSegments?, heightSegments?)` | Flat cards, screens, billboards, walls. |
| `waveGround` | `(width?, height?, subdivisions?)` | Floors and terrain-like flat surfaces. |
| `waveDisc` | `(radius?, tessellation?)` | Flat circles and partial circular panels. |
| `waveRing` | `(outerRadius?, innerRadius?, tessellation?)` | Flat rings and 2D circular frames. |
| `wavePolygon` | `(radius?, sides?)` | Regular or seeded polygons, flat or extruded. |
| `waveLine` | `(start?, end?)` | Straight segments and visual connectors. |
| `waveArc` | `(radius?, startAngle?, endAngle?, segments?)` | Curves, gauges, orbit arcs. |
| `wavePath` | `(points?, closed?, curveType?, segments?, catmullRomTension?)` | Editable paths, splines, sampled motion routes. |
| `wavePointNet` | `(points?, options?)` | Networks of points and linked path nodes. |
| `wavePathExtrusion` | `(path, shape, options?)` | Tubes, rails, swept profiles, custom extrusions. |
| `waveTube` | `(radius?, tessellation?, path?)` | Tubes following 3D point paths. |
| `waveLathe` | `(radius?, tessellation?, shape?, arc?)` | Revolved shapes like vases, bowls, turned parts. |
| `waveIcoSphere` | `(radius?, subdivisions?, flat?)` | Low-poly spheres and geodesic shapes. |
| `waveDome` | `(diameter?, arc?, segments?)` | Domes, caps, hemispheres. |
| `waveWedge` | `(width?, height?, depth?, flipSlope?)` | Ramps, triangular blocks, sloped pieces. |
| `wavePrism` | `(sides?, radius?, height?, topRadius?)` | Extruded polygons and tapered columns. |
| `waveTrapezoid` | `(width?, height?, depth?, topWidth?, topDepth?)` | Tapered boxes, frustums, blocks with smaller tops. |
| `waveCavity` | `(width?, height?, depth?, options?)` | Rooms, tunnels, interior spaces, openings. |
| `waveCavityTopology` | `(options?)` | Graph-like cave or room topology generation. |

Important shape-specific methods usually start with `as...` or `setBase...`.
For example, `waveCube` has `asBox` and `setBaseSizes`, while `waveTorus`
has `setBaseRadius`, `setBaseTubeRadius`, and `setBaseArc`.

```ts
const ring = new waveTorus(2, 0.18, 96);
ring.setColor(PALETTE.CYAN);
ring.placeAt(0, 2, 0);

ring.moveAlong(Direction.Up, 1, Animate).over(2, Seconds).play();
```

### 3D Scene Objects Beyond Raw Geometry

| Class | Use |
| --- | --- |
| `wave3DObject` | Generic 3D model/object wrapper with model, animation, physics, input, parts, and explosion APIs. |
| `waveProp` / `xrProp` | Prop-style objects, including XR variants. |
| `waveActor` / `waveHumanoid` / `xrActor` | Actor-style objects for characters and controlled entities. |
| `xr3DObject` | XR-ready 3D object. |
| `wavePoint` | Invisible or visible point with history, range, speed, position, and orientation helpers. |
| `waveMarker` | Marker object that can follow locators, anchors, effects, or world positions. |
| `waveCamera` | Scene camera entity with follow, helper, FOV, look direction, and activation helpers. |
| `waveLight` | Light entity supporting spot, point, directional, hemispheric, area, shadow, and beam helpers. |
| `waveTerrain` / `waveTerrainTile` | Terrain authoring, sculpting, generated terrain, and terrain tile objects. |
| `waveDecal` | Decals projected onto scene surfaces. |
| `waveInstanceMesh` | Single instance mesh with click and color controls. |
| `waveInstanceGroup` | Many model instances controlled as one group. |
| `waveThinBatch` | High-performance batch rendering for many instances. |
| `wavePointCloudBatch` | Point-cloud style batch effects, including dissolve playback. |

Use these when the object is not just a primitive shape. For example, a
`waveLight` is still an object in the scene, but its purpose is lighting rather
than mesh geometry.

### Collections, Spawning, Rigs, and Fields

| Class | Use |
| --- | --- |
| `WaveGroup` | Add, remove, map, filter, explode, implode, enable physics, or destroy members together. |
| `Wave3DGrid` | Arrange objects in rows, columns, and layers. |
| `WaveRig` | Group and attach entities for rigged structures. |
| `WaveJointAssembly` / `WaveJointBuilder` | Build physics joint assemblies. |
| `WaveCloth` / `WaveClothBuilder` | Cloth grids, pins, anchors, and soft-body style cloth behavior. |
| `WaveRope` / `WaveRopeBuilder` | Rope chains, start/end anchors, and soft-body style rope behavior. |
| `WaveRange` | AABB, sphere, model, or voxel query regions. |
| `WaveField` | Gravity, wind, vortex, buoyancy, vector, radial, and texture-driven fields. |
| `WaveSpawner` | Placement helpers for spawning batches inline, around, in ranges, on paths, on surfaces, or in grids. |

### Animation and Motion APIs

WaveStudio animation is not only one class. It is a pattern shared by transform,
material, UI, model clips, and frame updates.

| API | Use |
| --- | --- |
| `Animate`, `Apply`, `Snapshot` | Mode constants for transform and property verbs. |
| `WaveTransformAnimationBuilder` | Returned by transform calls such as `moveBy(..., Animate)` and supports `over`, `for`, `after`, `atRateOf`, `withEasingEffect`, and `play`. |
| `WaveTransformFitBetweenAnimationBuilder` | Animates fit-between placement. |
| `WaveMaterialPropertyAnimationBuilder` | Animates material/color property changes. |
| `WaveUIPropertyAnimationBuilder` | Animates UI properties. |
| `WaveAnimationClip` | Loads or creates animation clips, adds events, clones clips. |
| `AnimationGroup` / `AnimationEvent` | Underlying animation group/event types. |
| `GSAPEasing` | Named easing constants such as `GSAPEasing.Power2InOut`. |
| `onTick` / `every` / `once` | Frame and time-based behavior patterns. |
| `playAnimation` | Plays a model or object animation clip by name. |

```ts
const block = new waveCube(1, 1, 1);

block.moveAlong(Direction.Right, 4, Animate)
  .over(1.5, Seconds)
  .withEasingEffect(GSAPEasing.Power2InOut)
  .play();

block.onTick((_self, deltaTime) => {
  block.turnRight(90 * deltaTime);
});
```

### UI Objects

WaveStudio has local UI objects and network-ready `netUI...` variants. The
local UI family is:

- `waveUI`
- `waveUIText`
- `waveUIImage`
- `waveUICanvas`
- `waveUIFrame`
- `waveUIButton`
- `waveUIProgressBar`
- `waveUISlider`
- `waveUIInput`
- `waveUITextArea`
- `waveUIDropdown`
- `waveUIToggle`
- `waveUICrosshair`
- `waveUIProgressRing`
- `waveUITooltip`
- `waveUINotification`
- `waveUIDebugOverlay`
- `waveUIHandGestureOverlay`
- `waveUIInventory`
- `waveUIHealthBar`
- `waveUIDialogue`
- `waveUIMinimap`

Network-ready variants declared in the same file include:

- `netUI`
- `netUIText`
- `netUIImage`
- `netUICanvas`
- `netUIFrame`
- `netUIButton`
- `netUIProgressBar`
- `netUISlider`
- `netUIInput`
- `netUITextArea`
- `netUIDropdown`
- `netUIToggle`
- `netUIHealthBar`
- `netUIDialogue`

### Effects, Assets, Time, and Scene Services

| API | Use |
| --- | --- |
| `scene` / `myScene` | Active `WaveScene`; manage entities, groups, ranges, fields, spawners, camera, lighting, weather, terrain, data, and screen helpers. |
| `engine` / `ctx` | Runtime engine and scene context. |
| `assetManager` | Resolve, register, inspect, and load assets. |
| `models`, `textures`, `materials`, `animations`, `audios`, `videos`, `hdr`, `fonts`, `particles` | Typed asset warehouses exposed to Monaco autocomplete. |
| `waveFx` | Build custom effects such as emitters, trails, wakes, exhaust, impacts, muzzle flashes, and sparks. |
| `waveFxPresets` | Ready-made effect systems such as impact sparks, rain, snow, and lightning. |
| `WavePostFx` | Scene post-processing effects through `scene.postFx`. |
| `WaveScreenMedia` | Full-screen media through `scene.screenMedia`. |
| `WaveOcclusionCulling` | Culling configuration through `scene.occlusion`. |
| `WaveEventBus` / `waveEventBus` | Event subscription and emission. |
| `WaveTime`, `WaveTimeSpan`, `WaveRate`, `Seconds`, `Ticks`, `UnitsPerSecond`, `DegreesPerSecond` | Time and rate helpers. |
| `myCloud` | Persistent scene data store. |

## How to Learn the API from IntelliSense

The declaration file is large, but the authoring pattern is consistent:

1. Start from a concrete class: `waveCube`, `waveSphere`, `wave3DObject`,
   `waveUIText`, or `WaveScene`.
2. Type a dot and use autocomplete.
3. Prefer methods that return the same object type when chaining.
4. If a method returns a broad base type, split the code into multiple lines.
5. For object creation, check constructors first.
6. For behavior, search for `onTick`, `whenPress`, `whenClickedOn`,
   `onCollisionBegin`, and `after`.
7. For assets, type `models.`, `textures.`, `materials.`, `audios.`, or
   `animations.` and let the typed warehouse show valid names.

## Common Patterns

Create and configure:

```ts
const object = new waveCube(1, 1, 1);
object.placeAt(0, 1, 0);
```

Find by tag:

```ts
for (const pickup of scene.getByTag("pickup")) {
  if (pickup instanceof waveSphere) {
    pickup.showBoundingBox();
  }
}
```

Run every frame:

```ts
const object = new waveCube(1, 1, 1);
object.placeAt(0, 1, 0);

object.onTick((_object, deltaTime) => {
  object.turnRight(45 * deltaTime);
});
```

Run every second:

```ts
const object = new waveCube(1, 1, 1);

object.onTick(() => {
  scene.print("One second passed");
}, every(1, Seconds));
```

Handle a click:

```ts
const object = new waveCube(1, 1, 1);

object.whenClickedOn(() => {
  object.setColor(PALETTE.FUCHSIA);
});
```

Make physics:

```ts
const floor = new waveGround(12, 12, 2);
const ball = new waveSphere(0.5, 24).placeAt(0, 3, 0);
const player = new waveCube(1, 2, 1).placeAt(0, 1, -4);

floor.useStaticBody();
ball.useDynamicBody();
player.useKinematicBody();
```

Save JSON:

```ts
async function saveLastPlayedAt() {
  const record = await myCloud.openRecord("game");
  myCloud.json.set(record, "lastPlayedAt", Date.now());
  await myCloud.saveRecord("game", record);
}

saveLastPlayedAt();
```

## Quick Reference

Core globals:

- `scene`: active `WaveScene`
- `engine`: active engine
- `assetManager`: asset loading and generated mesh utilities
- `myCloud`: scene data store
- `PALETTE`: typed color constants and helpers such as `PALETTE.CYAN`
- `COLOR`: alias of the same color map
- `models`, `textures`, `materials`, `animations`, `audios`: typed assets

Core scene methods:

- `scene.remove(entityOrId)`
- `scene.getByTag(tag)`
- `scene.getFirstByName(name)`
- `scene.find(predicate)`
- `scene.createGroup(name)`
- `myScene.placeInGrid(input)`
- `myScene.placeAround(target)`
- `scene.spawnInGrid(input, options)`
- `scene.print(text)`

Core entity methods:

- `placeAt`, `placeAbove`, `placeInfrontOf`, `moveBy`, `moveForward`, `moveUp`
- `turnRight`, `turnLeft`, `pitchUp`, `rollLeft`
- `setScale`, `setUniformScale`, `scaleBy`
- `setColor`, `useMaterial`, `setBaseTexture`
- `onTick`, `after`
- `whenPress`, `whenHolding`, `whenClickedOn`
- `showDirection`, `showPosition`, `showCoordinate`, `showBoundingBox`
- `explode`, `exploding`
- `useStaticBody`, `useDynamicBody`, `useKinematicBody`
- `onCollisionBegin`, `onCollisionBeginByTag`

Core UI classes:

- `waveUIText`
- `waveUIButton`
- `waveUIImage`
- `waveUISlider`
- `waveUIProgressBar`

## When Something Does Not Typecheck

Use the generated declarations as the source of truth:

```bash
rg "declare class waveCube" waveStudio-globals.d.ts
rg "whenClickedOn" waveStudio-globals.d.ts
rg "interface WaveSceneDataStore" waveStudio-globals.d.ts
```

Common fixes:

- Split long method chains into multiple statements.
- Check constructor parameter order.
- Use autocomplete for option object names.
- Use asset aliases instead of raw strings when possible.
- Remember that globals such as `scene` and `models` exist in WaveStudio, but
  not in a plain TypeScript project unless you include the declaration file.

## Tutorial Maintenance Check

Run this check every time the tutorial is added to, removed from, or modified:

1. Curriculum order: the lesson still flows from foundations, to visuals, to
   behavior, to scene systems, to projects and reference.
2. Numbering: lesson headings, side navigation labels, `data-lesson` values,
   progress text, and creator-track references all agree.
3. Placement: each example lives in the earliest lesson where its prerequisites
   are already taught; larger mixtures go in Lesson 18 or Lesson 19.
4. Search: new terms appear in both visible copy and `data-keywords`.
5. Interaction: copy buttons attach to every code block, progress checkboxes
   still save/reset, and search still finds the new material.
6. Copyability: every `ts` or `typescript` block compiles as an isolated
   WaveStudio paste; partial fragments, command examples, and globals lists use
   non-TypeScript fences.
7. Runtime behavior: interactive snippets are pasted into WaveStudio, Run is
   pressed, the preview is focused, and the primary behavior is checked with
   real input such as click, WASD, Space, or pointer drawing. Typecheck alone is
   not enough for game examples.
8. Studio style: snippets avoid unnecessary manual scene insertion calls,
   prefer typed constants such as `PALETTE`, `Direction`, `Seconds`, and
   `Keyboard`, prefer natural placement verbs such as `placeAt`, `placeAbove`,
   and `placeAround`, and split chains when a method does not return a
   chainable object.
9. Repository hygiene: tutorial edits stay docs-only unless the request clearly
   asks for runtime files; no `*.ts` files are staged by accident.
