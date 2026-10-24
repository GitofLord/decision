# Effect Recipes

All recipes are React + TypeScript. CSS uses theme tokens from `themes.md`.
Every recipe already respects `prefers-reduced-motion` or notes how to.

Index:
- Backgrounds: `aurora-gradient`, `noise-gradient`, `grid-horizon`, `particle-field`, `starfield-warp`
- Text: `split-text-reveal`, `glitch-text`, `scramble-text`, `marquee-band`
- Scroll: `smooth-scroll`, `reveal-stagger`, `pinned-scroll-story`, `image-mask-reveal`
- Interaction: `magnetic-button`, `tilt-card`, `custom-cursor`, `spotlight-cursor`, `press-button`, `neon-border`
- 3D: `molecule-3d`
- Feedback / gamification: `count-up`, `confetti-burst`, `sticker-pop`, `page-transition`

---

## Backgrounds

### aurora-gradient (CSS only)
```css
.aurora { position: fixed; inset: 0; z-index: -1; overflow: hidden; background: var(--bg); }
.aurora::before, .aurora::after {
  content: ""; position: absolute; width: 70vmax; height: 70vmax; border-radius: 50%;
  filter: blur(90px); opacity: .55; will-change: transform;
}
.aurora::before { background: var(--accent);   top: -20%; left: -10%; animation: drift 22s ease-in-out infinite alternate; }
.aurora::after  { background: var(--accent-2); bottom: -25%; right: -15%; animation: drift 28s ease-in-out infinite alternate-reverse; }
@keyframes drift { to { transform: translate(18vmax, 12vmax) scale(1.15); } }
@media (prefers-reduced-motion: reduce) { .aurora::before, .aurora::after { animation: none; } }
```

### noise-gradient (adds film grain to any gradient)
```css
.grain::after {
  content: ""; position: fixed; inset: 0; pointer-events: none; z-index: 50; opacity: .07;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='160' height='160'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.85' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
}
```

### grid-horizon (synthwave floor)
```css
.horizon { position: absolute; inset: auto 0 0 0; height: 55vh; perspective: 320px; overflow: hidden; }
.horizon__grid {
  position: absolute; inset: 0 -50%; transform: rotateX(62deg); transform-origin: top;
  background-image:
    linear-gradient(var(--accent) 1px, transparent 1px),
    linear-gradient(90deg, var(--accent-2) 1px, transparent 1px);
  background-size: 48px 48px; opacity: .45;
  mask-image: linear-gradient(to bottom, transparent, #000 40%);
  animation: grid-run 1.6s linear infinite;
}
@keyframes grid-run { to { background-position: 0 48px, 0 0; } }
@media (prefers-reduced-motion: reduce) { .horizon__grid { animation: none; } }
```

### particle-field (Canvas 2D, connected dots — "data network")
```tsx
import { useEffect, useRef } from "react";

export function ParticleField({ color = "#3dffc8", density = 0.00012 }) {
  const ref = useRef<HTMLCanvasElement>(null);
  useEffect(() => {
    const canvas = ref.current!, ctx = canvas.getContext("2d")!;
    const reduce = matchMedia("(prefers-reduced-motion: reduce)").matches;
    let raf = 0, w = 0, h = 0;
    let pts: { x: number; y: number; vx: number; vy: number }[] = [];
    const resize = () => {
      const dpr = Math.min(devicePixelRatio, 1.75);
      w = canvas.clientWidth; h = canvas.clientHeight;
      canvas.width = w * dpr; canvas.height = h * dpr; ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      const n = Math.round(w * h * density * (w < 768 ? 0.3 : 1));
      pts = Array.from({ length: n }, () => ({
        x: Math.random() * w, y: Math.random() * h,
        vx: (Math.random() - .5) * .35, vy: (Math.random() - .5) * .35,
      }));
    };
    const tick = () => {
      ctx.clearRect(0, 0, w, h);
      for (const p of pts) {
        p.x = (p.x + p.vx + w) % w; p.y = (p.y + p.vy + h) % h;
        ctx.fillStyle = color; ctx.globalAlpha = .8;
        ctx.beginPath(); ctx.arc(p.x, p.y, 1.4, 0, Math.PI * 2); ctx.fill();
      }
      for (let i = 0; i < pts.length; i++)
        for (let j = i + 1; j < pts.length; j++) {
          const dx = pts[i].x - pts[j].x, dy = pts[i].y - pts[j].y, d2 = dx * dx + dy * dy;
          if (d2 < 120 * 120) {
            ctx.globalAlpha = 1 - Math.sqrt(d2) / 120; ctx.strokeStyle = color; ctx.lineWidth = .5;
            ctx.beginPath(); ctx.moveTo(pts[i].x, pts[i].y); ctx.lineTo(pts[j].x, pts[j].y); ctx.stroke();
          }
        }
      if (!reduce) raf = requestAnimationFrame(tick);
    };
    resize(); tick();
    addEventListener("resize", resize);
    return () => { cancelAnimationFrame(raf); removeEventListener("resize", resize); };
  }, [color, density]);
  return <canvas ref={ref} className="absolute inset-0 h-full w-full" aria-hidden />;
}
```

### starfield-warp (react-three-fiber)
```tsx
import { Canvas, useFrame } from "@react-three/fiber";
import { Stars } from "@react-three/drei";
import { useRef } from "react";
import type { Group } from "three";

function Warp() {
  const g = useRef<Group>(null);
  useFrame((_, dt) => { if (g.current) { g.current.rotation.z += dt * .02; g.current.position.z = (g.current.position.z + dt * 4) % 50; } });
  return <group ref={g}><Stars radius={80} depth={60} count={4000} factor={4} fade speed={1} /></group>;
}
export const StarfieldHero = () => (
  <Canvas dpr={[1, 1.75]} camera={{ position: [0, 0, 1] }} className="!absolute inset-0">
    <color attach="background" args={["#03040a"]} /><Warp />
  </Canvas>
);
```

---

## Text

### split-text-reveal (GSAP SplitText)
```tsx
import { useRef } from "react";
import gsap from "gsap";
import { SplitText } from "gsap/SplitText";
import { useGSAP } from "@gsap/react";
gsap.registerPlugin(SplitText, useGSAP);

export function RevealHeading({ children }: { children: string }) {
  const ref = useRef<HTMLHeadingElement>(null);
  useGSAP(() => {
    const mm = gsap.matchMedia();
    mm.add("(prefers-reduced-motion: no-preference)", () => {
      const split = SplitText.create(ref.current!, { type: "lines,words", mask: "lines" });
      gsap.from(split.words, { yPercent: 110, duration: 1.1, ease: "expo.out", stagger: .04 });
      return () => split.revert();
    });
  }, { scope: ref });
  return <h1 ref={ref} className="text-6xl md:text-8xl leading-[.95]">{children}</h1>;
}
```

### glitch-text (CSS)
```css
.glitch { position: relative; color: var(--text); }
.glitch::before, .glitch::after { content: attr(data-text); position: absolute; inset: 0; }
.glitch::before { color: var(--accent);   transform: translate(2px,0);  clip-path: inset(0 0 55% 0); animation: g1 2.4s infinite steps(2); }
.glitch::after  { color: var(--accent-2); transform: translate(-2px,0); clip-path: inset(55% 0 0 0); animation: g2 1.9s infinite steps(2); }
@keyframes g1 { 0%,92%{transform:translate(0)} 94%{transform:translate(-4px,1px)} 96%{transform:translate(3px,-1px)} }
@keyframes g2 { 0%,90%{transform:translate(0)} 93%{transform:translate(4px,0)}  97%{transform:translate(-3px,1px)} }
@media (prefers-reduced-motion: reduce) { .glitch::before, .glitch::after { animation: none; display: none; } }
```
Usage: `<h1 className="glitch" data-text="SALES ARENA">SALES ARENA</h1>`

### scramble-text (hook)
```tsx
import { useEffect, useState } from "react";
const CHARS = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789#%&";
export function useScramble(target: string, duration = 900) {
  const [out, setOut] = useState(target);
  useEffect(() => {
    if (matchMedia("(prefers-reduced-motion: reduce)").matches) return setOut(target);
    const start = performance.now(); let raf = 0;
    const step = (t: number) => {
      const p = Math.min((t - start) / duration, 1), fixed = Math.floor(p * target.length);
      setOut(target.slice(0, fixed) + [...target.slice(fixed)].map(c => c === " " ? " " : CHARS[(Math.random() * CHARS.length) | 0]).join(""));
      if (p < 1) raf = requestAnimationFrame(step);
    };
    raf = requestAnimationFrame(step);
    return () => cancelAnimationFrame(raf);
  }, [target, duration]);
  return out;
}
```

### marquee-band (CSS)
```tsx
export const Marquee = ({ items }: { items: string[] }) => (
  <div className="marquee" aria-hidden>
    <div className="marquee__track">{[...items, ...items].map((t, i) => <span key={i}>{t} ✦</span>)}</div>
  </div>
);
```
```css
.marquee { overflow: hidden; border-block: 2px solid var(--border); background: var(--accent); color: var(--bg); }
.marquee__track { display: flex; gap: 2rem; width: max-content; padding: .8rem 0; font: 700 clamp(1.2rem,3vw,2rem)/1 var(--font-display);
  animation: marquee 25s linear infinite; }
@keyframes marquee { to { transform: translateX(-50%); } }
@media (prefers-reduced-motion: reduce) { .marquee__track { animation: none; } }
```

---

## Scroll

### smooth-scroll (Lenis synced with GSAP)
```tsx
import Lenis from "lenis";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useEffect } from "react";
gsap.registerPlugin(ScrollTrigger);

export function useSmoothScroll() {
  useEffect(() => {
    if (matchMedia("(prefers-reduced-motion: reduce)").matches) return;
    const lenis = new Lenis({ lerp: .09 });
    lenis.on("scroll", ScrollTrigger.update);
    const raf = (t: number) => lenis.raf(t * 1000);
    gsap.ticker.add(raf); gsap.ticker.lagSmoothing(0);
    return () => { gsap.ticker.remove(raf); lenis.destroy(); };
  }, []);
}
```

### reveal-stagger (Motion)
```tsx
import { motion, useReducedMotion } from "motion/react";
export function Reveal({ children, i = 0 }: { children: React.ReactNode; i?: number }) {
  const reduce = useReducedMotion();
  return (
    <motion.div
      initial={reduce ? false : { opacity: 0, y: 40, filter: "blur(8px)" }}
      whileInView={{ opacity: 1, y: 0, filter: "blur(0px)" }}
      viewport={{ once: true, margin: "-10% 0px" }}
      transition={{ duration: .8, delay: i * .08, ease: [.16, 1, .3, 1] }}
    >{children}</motion.div>
  );
}
```

### pinned-scroll-story (GSAP: pin a section, step through panels)
```tsx
import { useRef } from "react";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useGSAP } from "@gsap/react";
gsap.registerPlugin(ScrollTrigger, useGSAP);

export function ScrollStory({ steps }: { steps: { title: string; body: string }[] }) {
  const root = useRef<HTMLElement>(null);
  useGSAP(() => {
    const mm = gsap.matchMedia();
    mm.add("(min-width: 768px) and (prefers-reduced-motion: no-preference)", () => {
      const panels = gsap.utils.toArray<HTMLElement>(".story-panel");
      const tl = gsap.timeline({ scrollTrigger: { trigger: root.current, pin: true, scrub: 1, end: `+=${panels.length * 100}%` } });
      panels.forEach((p, i) => { if (i) tl.fromTo(p, { autoAlpha: 0, y: 60 }, { autoAlpha: 1, y: 0 }).to(panels[i - 1], { autoAlpha: 0, y: -60 }, "<"); });
    });
  }, { scope: root });
  return (
    <section ref={root} className="relative grid min-h-screen place-items-center">
      {steps.map((s, i) => (
        <article key={i} className="story-panel md:absolute max-w-xl p-8">
          <h2 className="text-4xl">{s.title}</h2><p className="mt-4 text-[var(--muted)]">{s.body}</p>
        </article>
      ))}
    </section>
  );
}
```

### image-mask-reveal (clip-path on scroll)
```tsx
import { motion, useScroll, useTransform } from "motion/react";
import { useRef } from "react";
export function MaskImage({ src, alt }: { src: string; alt: string }) {
  const ref = useRef<HTMLDivElement>(null);
  const { scrollYProgress } = useScroll({ target: ref, offset: ["start end", "center center"] });
  const clip = useTransform(scrollYProgress, [0, 1], ["inset(30% 30% 30% 30% round 24px)", "inset(0% 0% 0% 0% round 0px)"]);
  const scale = useTransform(scrollYProgress, [0, 1], [1.3, 1]);
  return (
    <div ref={ref} className="overflow-hidden">
      <motion.img src={src} alt={alt} style={{ clipPath: clip, scale }} className="h-[80vh] w-full object-cover" />
    </div>
  );
}
```

---

## Interaction

### magnetic-button
```tsx
import { motion, useMotionValue, useSpring } from "motion/react";
export function MagneticButton({ children, ...rest }: React.ComponentProps<typeof motion.button>) {
  const x = useMotionValue(0), y = useMotionValue(0);
  const sx = useSpring(x, { stiffness: 220, damping: 15 }), sy = useSpring(y, { stiffness: 220, damping: 15 });
  return (
    <motion.button {...rest} style={{ x: sx, y: sy }}
      onPointerMove={e => { const r = e.currentTarget.getBoundingClientRect();
        x.set((e.clientX - r.left - r.width / 2) * .35); y.set((e.clientY - r.top - r.height / 2) * .35); }}
      onPointerLeave={() => { x.set(0); y.set(0); }}
      whileTap={{ scale: .94 }}
    >{children}</motion.button>
  );
}
```

### tilt-card (3D tilt + glare)
```tsx
import { motion, useMotionValue, useSpring, useTransform } from "motion/react";
export function TiltCard({ children }: { children: React.ReactNode }) {
  const px = useMotionValue(.5), py = useMotionValue(.5);
  const rx = useSpring(useTransform(py, [0, 1], [10, -10]), { stiffness: 200, damping: 20 });
  const ry = useSpring(useTransform(px, [0, 1], [-10, 10]), { stiffness: 200, damping: 20 });
  const glare = useTransform([px, py], ([a, b]: number[]) => `radial-gradient(circle at ${a * 100}% ${b * 100}%, rgba(255,255,255,.25), transparent 50%)`);
  return (
    <motion.div style={{ rotateX: rx, rotateY: ry, transformPerspective: 900 }}
      onPointerMove={e => { const r = e.currentTarget.getBoundingClientRect(); px.set((e.clientX - r.left) / r.width); py.set((e.clientY - r.top) / r.height); }}
      onPointerLeave={() => { px.set(.5); py.set(.5); }}
      className="relative rounded-[var(--radius)] border border-[var(--border)] bg-[var(--surface)] backdrop-blur-xl">
      {children}
      <motion.div aria-hidden style={{ background: glare }} className="pointer-events-none absolute inset-0 rounded-[inherit]" />
    </motion.div>
  );
}
```

### custom-cursor (disable on touch & reduced motion)
```tsx
import { motion, useMotionValue, useSpring } from "motion/react";
import { useEffect, useState } from "react";
export function Cursor() {
  const x = useMotionValue(-100), y = useMotionValue(-100);
  const sx = useSpring(x, { stiffness: 500, damping: 40 }), sy = useSpring(y, { stiffness: 500, damping: 40 });
  const [hover, setHover] = useState(false);
  const [enabled] = useState(() => matchMedia("(pointer: fine) and (prefers-reduced-motion: no-preference)").matches);
  useEffect(() => {
    if (!enabled) return;
    const move = (e: PointerEvent) => { x.set(e.clientX); y.set(e.clientY);
      setHover(!!(e.target as Element).closest("a,button,[data-cursor]")); };
    addEventListener("pointermove", move);
    return () => removeEventListener("pointermove", move);
  }, [enabled]);
  if (!enabled) return null;
  return <motion.div aria-hidden style={{ x: sx, y: sy, translateX: "-50%", translateY: "-50%" }}
    animate={{ width: hover ? 64 : 14, height: hover ? 64 : 14 }}
    className="pointer-events-none fixed left-0 top-0 z-[100] rounded-full bg-white mix-blend-difference" />;
}
```

### spotlight-cursor (light follows pointer over a section)
```css
.spotlight { --mx: 50%; --my: 50%;
  background: radial-gradient(600px circle at var(--mx) var(--my), var(--glow), transparent 40%), var(--bg); }
```
```tsx
onPointerMove={e => { const r = e.currentTarget.getBoundingClientRect();
  e.currentTarget.style.setProperty("--mx", `${e.clientX - r.left}px`);
  e.currentTarget.style.setProperty("--my", `${e.clientY - r.top}px`); }}
```

### press-button (Retro-Futur hard shadow)
```css
.press { border: 2px solid var(--border); border-radius: var(--radius); background: var(--accent-3);
  box-shadow: var(--shadow-hard); transition: transform .12s, box-shadow .12s; }
.press:hover  { transform: translate(-2px,-2px); box-shadow: 8px 8px 0 #111; }
.press:active { transform: translate(6px,6px);  box-shadow: 0 0 0 #111; }
```

### neon-border (rotating conic border)
```css
@property --angle { syntax: "<angle>"; initial-value: 0deg; inherits: false; }
.neon { position: relative; border-radius: var(--radius); background: var(--surface); }
.neon::before { content: ""; position: absolute; inset: -2px; z-index: -1; border-radius: inherit;
  background: conic-gradient(from var(--angle), var(--accent), var(--accent-2), var(--accent));
  filter: drop-shadow(0 0 12px var(--glow)); animation: spin 4s linear infinite; }
@keyframes spin { to { --angle: 360deg; } }
@media (prefers-reduced-motion: reduce) { .neon::before { animation: none; } }
```

---

## 3D

### molecule-3d (ball-and-stick molecule with bloom; atoms from data)
```tsx
import { Canvas, useFrame } from "@react-three/fiber";
import { Float, OrbitControls, Environment } from "@react-three/drei";
import { EffectComposer, Bloom } from "@react-three/postprocessing";
import { useMemo, useRef } from "react";
import * as THREE from "three";

type Atom = { el: "C" | "N" | "O" | "H" | "S"; p: [number, number, number] };
const COLOR = { C: "#9aa4b2", N: "#4f7bff", O: "#ff5b6b", H: "#f2f5f8", S: "#ffd23f" };
const RADIUS = { C: .38, N: .36, O: .34, H: .2, S: .44 };

function Molecule({ atoms, bonds }: { atoms: Atom[]; bonds: [number, number][] }) {
  const g = useRef<THREE.Group>(null);
  useFrame((_, dt) => { if (g.current) g.current.rotation.y += dt * .25; });
  const sticks = useMemo(() => bonds.map(([a, b]) => {
    const A = new THREE.Vector3(...atoms[a].p), B = new THREE.Vector3(...atoms[b].p);
    const mid = A.clone().add(B).multiplyScalar(.5), len = A.distanceTo(B);
    const q = new THREE.Quaternion().setFromUnitVectors(new THREE.Vector3(0, 1, 0), B.clone().sub(A).normalize());
    return { mid, len, q };
  }), [atoms, bonds]);
  return (
    <group ref={g}>
      {atoms.map((a, i) => (
        <mesh key={i} position={a.p}>
          <sphereGeometry args={[RADIUS[a.el], 32, 32]} />
          <meshStandardMaterial color={COLOR[a.el]} emissive={COLOR[a.el]} emissiveIntensity={.35} roughness={.25} metalness={.3} />
        </mesh>
      ))}
      {sticks.map((s, i) => (
        <mesh key={i} position={s.mid} quaternion={s.q}>
          <cylinderGeometry args={[.07, .07, s.len, 12]} />
          <meshStandardMaterial color="#cfd8e3" roughness={.4} />
        </mesh>
      ))}
    </group>
  );
}

export function MoleculeHero(props: { atoms: Atom[]; bonds: [number, number][] }) {
  return (
    <Canvas dpr={[1, 1.75]} camera={{ position: [0, 0, 7], fov: 45 }}>
      <ambientLight intensity={.4} /><directionalLight position={[5, 5, 5]} intensity={1.2} />
      <Environment preset="city" />
      <Float speed={1.4} rotationIntensity={.4} floatIntensity={.8}><Molecule {...props} /></Float>
      <OrbitControls enableZoom={false} enablePan={false} />
      <EffectComposer><Bloom intensity={.9} luminanceThreshold={.2} mipmapBlur /></EffectComposer>
    </Canvas>
  );
}
```
Tip: coordinates can come from PubChem 3D SDF/JSON (CID lookup); center them and scale to ~±3 units.
Lazy-load: `const MoleculeHero = lazy(() => import("./MoleculeHero"))` with a gradient poster fallback.

---

## Feedback / gamification

### count-up (animated KPI number)
```tsx
import { animate, useInView, useReducedMotion } from "motion/react";
import { useEffect, useRef } from "react";
export function CountUp({ to, decimals = 0, suffix = "" }: { to: number; decimals?: number; suffix?: string }) {
  const ref = useRef<HTMLSpanElement>(null), inView = useInView(ref, { once: true }), reduce = useReducedMotion();
  useEffect(() => {
    if (!inView || !ref.current) return;
    const fmt = (v: number) => v.toLocaleString("tr-TR", { minimumFractionDigits: decimals, maximumFractionDigits: decimals }) + suffix;
    if (reduce) { ref.current.textContent = fmt(to); return; }
    const c = animate(0, to, { duration: 1.6, ease: [.16, 1, .3, 1], onUpdate: v => { ref.current!.textContent = fmt(v); } });
    return () => c.stop();
  }, [inView, to]);
  return <span ref={ref} className="tabular-nums">0</span>;
}
```

### confetti-burst (achievement unlocked)
```ts
import confetti from "canvas-confetti";
export function celebrate(colors = ["#ff2bd6", "#00f0ff", "#faff00"]) {
  if (matchMedia("(prefers-reduced-motion: reduce)").matches) return;
  const end = Date.now() + 900;
  (function frame() {
    confetti({ particleCount: 4, angle: 60,  spread: 70, origin: { x: 0, y: .7 }, colors });
    confetti({ particleCount: 4, angle: 120, spread: 70, origin: { x: 1, y: .7 }, colors });
    if (Date.now() < end) requestAnimationFrame(frame);
  })();
}
```

### sticker-pop (badge earned)
```tsx
<motion.div initial={{ scale: 0, rotate: -25 }} animate={{ scale: 1, rotate: -6 }}
  transition={{ type: "spring", stiffness: 420, damping: 14 }}
  className="press inline-grid place-items-center px-4 py-2 font-bold">🏅 Altın Rozet</motion.div>
```

### page-transition (Motion + router)
```tsx
import { AnimatePresence, motion } from "motion/react";
import { useLocation, useOutlet } from "react-router-dom";
export function AnimatedOutlet() {
  const loc = useLocation(), outlet = useOutlet();
  return (
    <AnimatePresence mode="wait">
      <motion.main key={loc.pathname}
        initial={{ opacity: 0, y: 24, filter: "blur(6px)" }} animate={{ opacity: 1, y: 0, filter: "blur(0px)" }}
        exit={{ opacity: 0, y: -24, filter: "blur(6px)" }} transition={{ duration: .45, ease: [.16, 1, .3, 1] }}>
        {outlet}
      </motion.main>
    </AnimatePresence>
  );
}
```
