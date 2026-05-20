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
| Artist Track | Shapes, materials, lights, decals, and mesh painting. | Lessons 2, 2A, 4, 9 | Geometry Surgery, Particle Weather Machine |
| Animator Track | Fluent chains, timelines, paths, and completion callbacks. | Lessons 3, 6, 11 | Draw-to-World Magic Ink, Robot Arm Puzzle |
| Game Track | Keyboard control, click handlers, HUDs, cameras, and scoring. | Lessons 7, 10, 12, 15 | Keyboard Driving Lab, Recursive Click Explosions |
| Physics Track | Collisions, rigid bodies, joints, force fields, and destruction. | Lessons 8, 13, 16 | Tiny Solar System, Physics Piano |
| Worldbuilding Track | Terrain, roads, cities, atmosphere, and performance views. | Lessons 9, 10, 11 | Terraforming Spellbook |
| Human Interface Track | Voice commands, webcam hands, microphone events, and permissions. | Lessons 7, 12, 13 | Voice/Gesture Character Controller |

You can jump between tracks at any time. The same object might be a painted
sculpture in the Artist Track, a clickable game target in the Game Track, and a
dynamic rigid body in the Physics Track.

## What You Are Writing

WaveStudio code runs inside a scene context. In the editor, several globals are
already declared for you:

```ts
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
`wave3DObject`, configures them with fluent methods, then adds them to the
scene.

```ts
const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.CYAN);
cube.moveTo({ x: 0, y: 1, z: 0 });
scene.add(cube);
```

### Fluent Dot Chaining

The repeated dots in examples such as `new Cube().placeAt(...).setColor(...)`
are called **method chaining** or a **fluent API**. Each method returns an
object, so the next `.method(...)` can keep working on that returned value.

```ts
const target = new Cube()
  .placeAt(0, 5, 0)
  .setColor(PALETTE.BLUE);

scene.add(target);
```

That is the same basic setup as writing separate statements:

```ts
const target = new Cube();
target.placeAt(0, 5, 0);
target.setColor(PALETTE.BLUE);

scene.add(target);
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

```ts
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
  cube.moveTo({ x: 0, y: 1, z: 0 });

  scene.add(cube);
});
```

Inside the WaveStudio editor, the shorter version is often enough because
`scene` is a predeclared global:

```ts
const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.CYAN);
cube.moveTo({ x: 0, y: 1, z: 0 });
scene.add(cube);
```

Key idea: create an object, configure it, then `scene.add(...)`.

## Lesson 2: Primitives

WaveStudio includes geometry classes for common shapes. Their constructors
usually take dimensions.

```ts
const ground = new waveGround(20, 20, 4);
ground.setColor(PALETTE.CHARCOAL);
ground.moveTo({ x: 0, y: 0, z: 0 });
scene.add(ground);

const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.TURQUOISE);
cube.moveTo({ x: -3, y: 1, z: 0 });
scene.add(cube);

const sphere = new waveSphere(1, 32);
sphere.setColor(PALETTE.GOLDENROD);
sphere.moveTo({ x: 0, y: 1, z: 0 });
scene.add(sphere);

const cylinder = new waveCylinder(2, 1.5, 1.5);
cylinder.setColor(PALETTE.FUCHSIA);
cylinder.moveTo({ x: 3, y: 1, z: 0 });
scene.add(cylinder);
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

## Lesson 2A: Built-In 3D Shapes Gallery

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
  shape.moveTo({ x: (col - 2) * spacing, y: 1.2, z: row * spacing });
  shape.addTag("built-in-shape");
  scene.add(shape);

  shape.onTick((_self, deltaTime) => {
    shape.turnRight(gallerySpinDegreesPerSecond * deltaTime);
  });

  shape.whenClickedOn(() => {
    scene.print(item.label);
  });
}
```

Notice the pattern: every built-in shape can be created, styled, placed, added
to the scene, animated, and clicked with the same basic workflow.

## Lesson 3: Position, Rotation, and Scale

Most 3D objects inherit transform helpers from `wave3DAbstract`.

```ts
const block = new waveCube(1, 1, 1);
scene.add(block);

block.moveTo({ x: 0, y: 1, z: 0 });          // world position
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

For 3D objects, `moveTo` expects one target point/reference. Use an object for
coordinates. If you want the three-number form, use `setWorldPosition`.

```ts
block.moveTo({ x: 0, y: 1, z: 0 });
block.setWorldPosition(0, 1, 0);
```

You can also use the lower-level transform component:

```ts
block.transform.setPosition(0, 2, 0);
block.transform.setScale(1, 2, 1);
block.transform.moveAlong(Direction.Up, 1);
```

Tip: many transform methods return a base type such as `wave3DAbstract`. For
clean TypeScript autocomplete, keep the original variable and call methods on
separate lines instead of building one very long chain.

```ts
const cube = new waveCube(2, 2, 2);
cube.moveTo({ x: 0, y: 1, z: 0 });
cube.setColor(PALETTE.WHITE);
cube.useDynamicBody();
```

## Lesson 4: Materials, Colors, and Textures

Simple colors are the fastest way to style primitives. You can use CSS-style
hex strings:

```ts
const floor = new waveGround(30, 30, 8);
floor.setColor(PALETTE.CHARCOAL);
scene.add(floor);
```

WaveStudio also exposes a typed `PALETTE` constant. This is what you may have
seen as "Palette" in examples:

```ts
const cube = new waveCube(2, 2, 2);
cube.setColor(PALETTE.CYAN);
cube.moveTo({ x: 0, y: 1, z: 0 });
scene.add(cube);

const accent = new waveSphere(0.6, 32);
accent.setColor(PALETTE.lighten(PALETTE.COBALT, 20));
accent.moveTo({ x: 2, y: 1, z: 0 });
scene.add(accent);
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
scene.add(panel);
```

If you have named materials in the asset warehouse, use them by name:

```ts
const platform = new waveCube(6, 0.4, 6);
platform.useMaterial(materials.PavingStones);
platform.moveTo({ x: 0, y: 0.2, z: 0 });
scene.add(platform);
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
  sample.moveTo({ x: (i - 2) * styleSpacing, y: 1, z: 0 });
  style.apply(sample);
  scene.add(sample);

  sample.whenClickedOn(() => scene.print(style.label));
}
```

## Lesson 5: Using Model Assets

Use `wave3DObject` when you want to place an imported model asset.

```ts
const ship = new wave3DObject(models.Spaceship);
ship.moveTo({ x: 0, y: 2, z: 0 });
ship.setUniformScale(0.4);
ship.turnRight(30);
scene.add(ship);
```

You can swap a model later:

```ts
ship.useModel(models.XWingStarfighter);
```

The global asset aliases are typed, so `models.` and `textures.` should
autocomplete inside the WaveStudio editor.

## Lesson 6: Animation Over Time

Use `onTick` when you want behavior to run every update. The callback receives
the entity and `deltaTime`.

```ts
const spinner = new waveCube(2, 2, 2);
spinner.setColor(PALETTE.SAGE);
spinner.moveTo({ x: 0, y: 2, z: 0 });
scene.add(spinner);

spinner.onTick((_self, deltaTime) => {
  spinner.turnRight(90 * deltaTime);
  spinner.moveUp(Math.sin(Date.now() * 0.004) * 0.01);
});
```

You can schedule less frequent work with time rules:

```ts
spinner.onTick(() => {
  scene.print(`FPS: ${engine.getCurrentFPS().toFixed(0)}`);
}, every(1, Seconds));
```

For one-shot delayed behavior, use `after`:

```ts
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

scene.add(myCube);

const boom = () => {
  myCube
    .exploding()
    .intoPieces(15)
    .apply();
};

myCube
  .faceMaterial(WaveSurface.Left)
  .setColor(COLOR.RED, Animate)
  .over(3, Seconds)
  .play()
  .onComplete(boom);
```

Read this from top to bottom:

1. `faceMaterial(WaveSurface.Left)` selects one side of the cube.
2. `setColor(COLOR.RED, Animate)` creates a material animation builder.
3. `over(3, Seconds)` sets the duration.
4. `play()` starts the animation and returns a lifecycle handle.
5. `onComplete(boom)` runs `boom` after the red-face animation finishes.

### Motion Pattern Gallery

Animation in WaveStudio is a small set of repeatable patterns: frame updates,
scheduled actions, transform builders, and material builders. This gallery
shows the difference by placing one object per pattern.

| Pattern | Use it for | Main APIs |
| --- | --- | --- |
| Tick spin | Continuous simulation | `onTick`, `deltaTime` |
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
  object.moveTo({ x: (i - 1.5) * motionSpacing, y: 1, z: 0 });
  object.addTag("motion-demo");
  scene.add(object);

  pattern.animate(object);
  object.whenClickedOn(() => scene.print(pattern.label));
}
```

## Lesson 7: Input

Many 3D entities expose keyboard helpers such as `whenPress`, `whenRelease`,
and `whenHolding`.

```ts
const player = new waveCube(1, 1, 1);
player.setColor(PALETTE.TURQUOISE);
player.moveTo({ x: 0, y: 1, z: 0 });
scene.add(player);

player.whenPress(Keyboard.W, () => player.moveForward(0.5));
player.whenPress(Keyboard.S, () => player.moveBackward(0.5));
player.whenPress(Keyboard.A, () => player.moveLeft(0.5));
player.whenPress(Keyboard.D, () => player.moveRight(0.5));
player.whenPress(Keyboard.Space, () => player.moveUp(1));
```

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
scene.add(car);

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
button.moveTo({ x: 0, y: 1, z: -3 });
scene.add(button);

button.whenClickedOn(() => {
  button.setColor(PALETTE.ORANGE);
  button.enlargeBy(1.2);
});
```

## Lesson 8: Physics

Use static bodies for floors and walls, dynamic bodies for objects that should
move under forces, and kinematic bodies for objects you control directly.

```ts
const floor = new waveGround(20, 20, 4);
floor.setColor(PALETTE.CHARCOAL);
floor.useStaticBody();
floor.addTag("floor");
scene.add(floor);

const ball = new waveSphere(1, 32);
ball.setColor(PALETTE.GOLDENROD);
ball.moveTo({ x: 0, y: 8, z: 0 });
ball.useDynamicBody();
ball.setMass(2);
ball.setRestitution(0.8);
scene.add(ball);

ball.onCollisionBeginByTag("floor", () => {
  ball.setColor(PALETTE.SKY);
});
```

For manual forces:

```ts
ball.applyImpulse(Direction.Up, 8);
ball.applyForce(Direction.Forward, 20);
ball.setLinearVelocity(Direction.Forward, 4);
```

For controlled movement with collision handling:

```ts
const actor = new waveCube(1, 2, 1);
actor.useKinematicBody();
actor.moveTo({ x: 0, y: 1, z: 0 });
scene.add(actor);

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

  object.moveTo({ x: (i - 1) * physicsSpacing, y: 1, z: 0 });
  object.addTag("physics-demo");
  example.setup(object);
  scene.add(object);

  object.whenClickedOn(() => scene.print(example.label));
}
```

## Lesson 9: Camera, Lighting, and Atmosphere

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
cameraRig.moveTo({ x: 0, y: 5, z: -8 });
scene.add(cameraRig);
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
cinematicCamera.moveTo({ x: 0, y: 4, z: -8 });
cinematicCamera.turnCameraTo(0, 1, 0);
cinematicCamera.setFov(50);
cinematicCamera.assignToNum1();
scene.add(cinematicCamera);

const pointLight = new waveLight();
pointLight.asPointLight();
pointLight.setLightColor(PALETTE.GOLDENROD);
pointLight.setIntensity(1.4);
pointLight.setRange(8);
pointLight.moveTo({ x: -3, y: 3, z: 0 });
scene.add(pointLight);

const spotlight = new waveLight();
spotlight.asSpotlight();
spotlight.setLightColor(PALETTE.CYAN);
spotlight.setIntensity(2);
spotlight.setAngle(45);
spotlight.moveTo({ x: 3, y: 4, z: -2 });
spotlight.aimAt(0, 1, 0);
scene.add(spotlight);
```

## Lesson 10: Scene Queries and Organization

Names and tags make scenes easier to manage.

```ts
const door = new waveCube(2, 3, 0.3);
door.setName("main-door");
door.addTag("door");
door.addTag("interactive");
scene.add(door);

const doors = scene.getByTag("door");
const mainDoor = scene.getFirstByName("main-door");

for (const entity of doors) {
  entity.showBoundingBox();
}

if (mainDoor) {
  mainDoor.hideBoundingBox();
}
```

Scene-level helpers include:

```ts
scene.getAll();
scene.getByName("main-door");
scene.getByTag("interactive");
scene.find((entity) => entity.hasTag("door"));
scene.destroyByTag("temporary");
```

## Lesson 11: Groups and Spawning

Use groups when objects belong together.

```ts
const row = scene.createGroup<waveCube>("row");

for (let i = 0; i < 5; i++) {
  const cube = new waveCube(1, 1, 1);
  cube.setColor(PALETTE.CYAN);
  cube.moveTo({ x: i * 1.4 - 2.8, y: 0.5, z: 0 });
  scene.add(cube);
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

## Lesson 12: UI Overlays

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
scene.add(scoreLabel);
```

Update UI from gameplay:

```ts
let score = 0;

function addScore(points: number) {
  score += points;
  scoreLabel.setText(`Score: ${score}`);
}

const gem = new waveSphere(0.5, 24);
gem.setColor(PALETTE.GOLDENROD);
gem.moveTo({ x: 0, y: 1, z: -4 });
scene.add(gem);

gem.whenClickedOn(() => {
  addScore(1);
  gem.hide();
  gem.destroy();
});
```

### UI Widget Gallery

UI objects use the same basic workflow as 3D objects: create, configure, place,
add to the scene, then attach callbacks when the widget should react.

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
scene.add(title);

const startButton = new waveUIButton();
startButton.setText("Start");
startButton.setFontSize(22);
startButton.setColor(PALETTE.BLACK);
startButton.setBackgroundColor(PALETTE.GOLDENROD);
startButton.setSize(160, 44);
startButton.setScreenPositionPixels(24, 84);
startButton.onClick(() => scene.print("Button clicked"));
scene.add(startButton);

const energyBar = new waveUIProgressBar();
energyBar.setProgress(72, 100);
energyBar.setProgressColor(PALETTE.GREEN);
energyBar.setBackgroundColor(panelColor);
energyBar.setSize(220, 24);
energyBar.setScreenPositionPixels(24, 144);
scene.add(energyBar);

const energySlider = new waveUISlider();
energySlider.setRange(0, 100, 1);
energySlider.setValue(72);
energySlider.setBackgroundColor(panelColor);
energySlider.setSize(220, 30);
energySlider.setScreenPositionPixels(24, 184);
energySlider.onChange((event) => {
  energyBar.setProgress(event.data.value, 100);
});
scene.add(energySlider);

const crosshair = new waveUICrosshair();
crosshair.setCrosshairColor(PALETTE.CYAN);
crosshair.setDot(3, PALETTE.WHITE);
crosshair.setSize(80, 80);
crosshair.setNormalizedScreenPosition(0.5, 0.5);
scene.add(crosshair);
```

## Lesson 13: Audio and Effects

Objects can play sounds by asset name or path:

```ts
const speaker = new waveCube(1, 1, 1);
speaker.moveTo({ x: 0, y: 1, z: 0 });
scene.add(speaker);

speaker.playSound(audios.studioSound, {
  loop: false,
  volume: 0.8,
});
```

Many 3D objects expose an `fx` facade:

```ts
const emitter = new waveSphere(0.4, 16);
emitter.setColor(PALETTE.WHITE);
emitter.moveTo({ x: 0, y: 1, z: 0 });
scene.add(emitter);

emitter.fx
  .createSmoke("soft-smoke")
  .color(PALETTE.LIGHT_GRAY)
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
smokeOrb.moveTo({ x: -3, y: 1, z: 0 });
scene.add(smokeOrb);

smokeOrb.fx
  .createSmoke("gallery-smoke")
  .rising()
  .color(PALETTE.LIGHT_GRAY)
  .rate(14)
  .play();

const sparkPad = new waveCube(1, 0.25, 1);
sparkPad.setColor(PALETTE.GOLDENROD);
sparkPad.moveTo({ x: 0, y: 0.5, z: 0 });
scene.add(sparkPad);

sparkPad.whenClickedOn(() => {
  sparkPad.playSound(audios.studioSound, { volume: 0.7 });
  sparkPad.fx.play(waveFxPresets.impactSparks());
});

const rainAnchor = new waveCube(0.2, 0.2, 0.2);
rainAnchor.moveTo({ x: 0, y: 4, z: 0 });
rainAnchor.hide();
scene.add(rainAnchor);
rainAnchor.fx.play(waveFxPresets.rain());

const breakable = new waveCube(1, 1, 1);
breakable.setColor(PALETTE.CORAL);
breakable.moveTo({ x: 3, y: 1, z: 0 });
scene.add(breakable);

breakable.whenClickedOn(() => {
  if (breakable.isDestroyed) return;
  breakable.exploding().intoPieces(10).withStrength(5).apply();
});
```

## Lesson 14: Persistent Scene Data

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

## Lesson 15: A Complete Mini Project

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
scene.add(scoreLabel);

const floor = new waveGround(20, 20, 4);
floor.setColor(PALETTE.CHARCOAL);
floor.useStaticBody();
floor.addTag("floor");
scene.add(floor);

const player = new waveCube(1, 1, 1);
player.setColor(PALETTE.TURQUOISE);
player.moveTo({ x: 0, y: 0.5, z: 0 });
player.useKinematicBody();
scene.add(player);

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
  pickup.moveTo({ x: i * 2 - 4, y: 0.75, z: -3 });
  pickup.addTag("pickup");
  scene.add(pickup);

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

## Boss Project 1: Recursive Click Explosions

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

scene.add(target);

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

scene.add(root);

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
  piece.moveTo({ x, y, z });
  piece.addTag("recursive-piece");
  scene.add(piece);

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

## Advanced Gallery: Boss Projects

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
scene.add(car);

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
terrain
  .paint()
  .layer(ROAD_LAYER)
  .path()
  .along([startTerrainPoint, endTerrainPoint])
  .width(4)
  .strength(0.7)
  .falloff("smooth")
  .apply();

terrain.author().lowerCircle(impactPoint, 2.5, 0.4, {
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
const gripper = roboticArm.getPart(GRIPPER_PART_NAME);

if (gripper) {
  gripper
    .moveTo(targetCube.getWorldPosition(), Animate)
    .over(TARGET_IK_MOVE_SECONDS, Seconds)
    .play()
    .onComplete(() => tryPickUpTarget(targetCube));
}
```

**APIs to steal:** model parts, part metadata, the source demo's end-effector
helper, `getPart`, `moveTo`, `onComplete`, click target registries.

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
const recognition = scene.director.sensing
  .speechCommands()
  .withCommands(speechCommandSpecs)
  .withLanguage("en-US")
  .withContinuousListening()
  .withInterimTranscripts()
  .withMinScore(0.68)
  .onCommand(handleSpeechCommand)
  .startListening();

const handTracking = await scene.director.sensing
  .webcamHands()
  .withFacingMode("user")
  .withGestureRecognition()
  .withMaxHands(1)
  .onGesture((data) => handleGestureCommand(data.name))
  .startTracking();
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

## Demo Atlas: 30 Project Seeds from `wave-engine`

The boss projects above teach a few patterns in depth. The atlas below is the
next layer: every extracted demo becomes a project prompt. Treat each row as a
small product idea you can build toward, not just a file to read.

| Demo | Project seed | APIs and ideas to study | Remix challenge |
| --- | --- | --- | --- |
| `amy-vat-crowd` | **Dance-Crowd Stress Test** | `createVertexAnimation`, `useVertexAnimation`, `waveInstanceMesh`, procedural GPU animation. | Build a concert crowd where rows react to music or keyboard cues. |
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
targetBrick.whenClickedOn(() => {
  const points = brickGrid
    .pathfind()
    .fromCell(startCell.row, startCell.col, 0)
    .to(targetBrick)
    .asWorldPoints();

  sheep.animator
    .followPath(points)
    .atSpeed(4)
    .turnToFace()
    .start();
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

scene.add(projector);
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

This is the learning shape behind `amy-vat-crowd`: bake one animation, then
reuse it across many instances instead of animating every model independently.

```ts
const vatAssetName = leader.createVertexAnimation("walking-vat", {
  animation: animations.Walking,
  range: "Walking",
  fps: "source",
  rootMotion: "lock",
});

leader.useVertexAnimation(vatAssetName, "Walking");

for (const performer of crowd) {
  performer.useVertexAnimation(vatAssetName, "Walking");
}
```

Remix it into a parade, dance floor, stadium, school hallway, or background
population system.

### Atlas Starter: Voice and Gesture Stage Director

This is the learning shape behind `amy-voice-gesture`: browser permissions
become part of the creative interface. Speech can trigger animation, hands can
steer focus, and microphone analysis can drive stage effects.

```ts
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
      if (name === "open_palm") amy.playAnimation(ASSET_WAREHOUSE.animations.Waving);
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
  .anchorAt(0, 0, 0)
  .asSphere(24)
  .showHelper();

terrain.sculpt()
  .noise()
  .inRange(craterRange)
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
  .inRange(craterRange)
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
  .onActuate(() => audio.playNote(60, { velocity: 1 }));
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

car.kinematicController.autoFitCapsule();
car.camera.possess().followFrom(car.backward, 10, 25).activate();
car.showDirection({ length: 2 });
car.showPosition();

myScene.director.whenPress(Keyboard.Escape, () => {
  car.camera.deactivate();
});
```

Remix it into a driving game, stealth patrol, chase camera lab, VFX car toy,
aircraft controller, or keyboard-input lesson.

## Complete Scene-Facing API Catalog

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
ring.moveTo({ x: 0, y: 2, z: 0 });
scene.add(ring);

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
scene.add(block);

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
| `scene` / `myScene` | Active `WaveScene`; add entities, groups, ranges, fields, spawners, camera, lighting, weather, terrain, data, and screen helpers. |
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

Create and add:

```ts
const object = new waveCube(1, 1, 1);
object.moveTo({ x: 0, y: 1, z: 0 });
scene.add(object);
```

Find by tag:

```ts
for (const pickup of scene.getByTag("pickup")) {
  pickup.showBoundingBox();
}
```

Run every frame:

```ts
object.onTick((_object, deltaTime) => {
  object.turnRight(45 * deltaTime);
});
```

Run every second:

```ts
object.onTick(() => {
  scene.print("One second passed");
}, every(1, Seconds));
```

Handle a click:

```ts
object.whenClickedOn(() => {
  object.setColor(PALETTE.FUCHSIA);
});
```

Make physics:

```ts
floor.useStaticBody();
ball.useDynamicBody();
player.useKinematicBody();
```

Save JSON:

```ts
const record = await myCloud.openRecord("game");
myCloud.json.set(record, "lastPlayedAt", Date.now());
await myCloud.saveRecord("game", record);
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

- `scene.add(entity)`
- `scene.remove(entityOrId)`
- `scene.getByTag(tag)`
- `scene.getFirstByName(name)`
- `scene.find(predicate)`
- `scene.createGroup(name)`
- `scene.spawnInGrid(input, options)`
- `scene.print(text)`

Core entity methods:

- `moveTo`, `moveBy`, `moveForward`, `moveUp`
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
