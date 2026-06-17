<script lang="ts">
  import { onMount } from 'svelte';

  let cursor: HTMLDivElement;

  const cores = typeof navigator !== 'undefined' ? (navigator.hardwareConcurrency || 4) : 4;
  const DOT_WIDTH   = 26;
  const AMOUNT      = cores <= 4 ? 12 : 16;
  const SINE_DOTS   = Math.floor(AMOUNT * 0.3);
  const IDLE_MS     = 150;
  const LERP        = 0.35;

  // ─── Color gradient (Sky 400 → Indigo 500) ─────────────────────────────────
  // Interpolate between two RGB values at ratio t ∈ [0,1]
  function lerpColor(
    r1: number, g1: number, b1: number,
    r2: number, g2: number, b2: number,
    t: number
  ): string {
    const r = Math.round(r1 + (r2 - r1) * t);
    const g = Math.round(g1 + (g2 - g1) * t);
    const b = Math.round(b1 + (b2 - b1) * t);
    return `rgb(${r},${g},${b})`;
  }
  // Sky 400:   56, 189, 248
  // Indigo 500: 99, 102, 241

  // ─── Dot data (plain objects, no class) ────────────────────────────────────
  interface DotState {
    x: number; y: number;
    lockX: number; lockY: number;
    angleX: number; angleY: number;
    scale: number;
    range: number;
    anglespeed: number;
    el: HTMLSpanElement;
  }

  let dots: DotState[] = [];
  let mouseX = 0;
  let mouseY = 0;
  let idle   = false;
  let active = false;
  let rafId  = 0;
  let idleTimer = 0;
  let needsRender = false;

  // ─── Build dot elements ─────────────────────────────────────────────────────
  function buildDots() {
    if (!cursor) return;
    cursor.innerHTML = '';
    dots = [];

    for (let i = 0; i < AMOUNT; i++) {
      const scale = 1 - 0.05 * i;
      const range = DOT_WIDTH / 2 - (DOT_WIDTH / 2) * scale + 2;
      const el    = document.createElement('span');

      el.style.backgroundColor = lerpColor(56, 189, 248, 99, 102, 241, i / AMOUNT);
      el.style.transform = `translate(-50%, -50%) scale(${scale})`;
      if (i < 6) el.style.willChange = 'transform';

      cursor.appendChild(el);

      dots.push({
        x: 0, y: 0,
        lockX: 0, lockY: 0,
        angleX: 0, angleY: 0,
        scale,
        range,
        anglespeed: 0.05,
        el,
      });
    }
  }

  // ─── Lock dots for idle sine drift ─────────────────────────────────────────
  function lockDots() {
    for (const d of dots) {
      d.lockX  = d.x;
      d.lockY  = d.y;
      d.angleX = Math.PI * 2 * Math.random();
      d.angleY = Math.PI * 2 * Math.random();
    }
  }

  // ─── Idle management ───────────────────────────────────────────────────────
  function startIdleTimer() {
    idle = false;
    clearTimeout(idleTimer);
    idleTimer = window.setTimeout(() => {
      idle = true;
      setActive(false);
      lockDots();
    }, IDLE_MS);
  }

  // ─── Cursor active state ───────────────────────────────────────────────────
  function setActive(on: boolean) {
    if (on === active) return;
    active = on;
    cursor?.classList.toggle('is-active', on);
  }

  // ─── RAF render loop ────────────────────────────────────────────────────────
  function tick() {
    rafId = requestAnimationFrame(tick);
    if (!needsRender && !idle) return;

    let x = mouseX;
    let y = mouseY;

    dots.forEach((dot, i) => {
      const next = dots[i + 1] || dots[0];

      if (!idle || i <= SINE_DOTS) {
        dot.x = x;
        dot.y = y;
        dot.el.style.transform =
          `translate(calc(${dot.x}px - 50%), calc(${dot.y}px - 50%)) scale(${dot.scale})`;

        const dx = (next.x - dot.x) * LERP;
        const dy = (next.y - dot.y) * LERP;
        x += dx;
        y += dy;
      } else {
        dot.angleX += dot.anglespeed;
        dot.angleY += dot.anglespeed;
        dot.x = dot.lockX + Math.sin(dot.angleX) * dot.range;
        dot.y = dot.lockY + Math.sin(dot.angleY) * dot.range;
        dot.el.style.transform =
          `translate(calc(${dot.x}px - 50%), calc(${dot.y}px - 50%)) scale(${dot.scale})`;
      }
    });

    needsRender = false;
  }

  // ─── Event handlers ─────────────────────────────────────────────────────────
  function onMouseMove(e: MouseEvent) {
    mouseX = e.clientX - DOT_WIDTH / 2;
    mouseY = e.clientY - DOT_WIDTH / 2;
    needsRender = true;
    setActive(true);
    startIdleTimer();
  }

  function onTouchMove(e: TouchEvent) {
    mouseX = e.touches[0].clientX - DOT_WIDTH / 2;
    mouseY = e.touches[0].clientY - DOT_WIDTH / 2;
    needsRender = true;
    setActive(true);
    startIdleTimer();
  }

  function onPointerOver(e: PointerEvent) {
    if ((e.target as HTMLElement).closest('button,[role="button"],a,.clickable,input,textarea,select')) {
      cursor?.classList.add('is-hover');
    }
  }

  function onPointerOut(e: PointerEvent) {
    if ((e.target as HTMLElement).closest('button,[role="button"],a,.clickable,input,textarea,select')) {
      cursor?.classList.remove('is-hover');
    }
  }

  // ─── Lifecycle ──────────────────────────────────────────────────────────────
  onMount(() => {
    buildDots();
    startIdleTimer();
    rafId = requestAnimationFrame(tick);

    window.addEventListener('mousemove',  onMouseMove);
    window.addEventListener('touchmove',  onTouchMove, { passive: true });
    document.addEventListener('pointerover', onPointerOver);
    document.addEventListener('pointerout',  onPointerOut);

    return () => {
      cancelAnimationFrame(rafId);
      clearTimeout(idleTimer);
      window.removeEventListener('mousemove',  onMouseMove);
      window.removeEventListener('touchmove',  onTouchMove);
      document.removeEventListener('pointerover', onPointerOver);
      document.removeEventListener('pointerout',  onPointerOut);
    };
  });
</script>

<!-- SVG goo filter (hidden) -->
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="800" style="display:none;" aria-hidden="true">
  <defs>
    <filter id="goo">
      <feGaussianBlur in="SourceGraphic" stdDeviation="6" result="blur" />
      <feColorMatrix in="blur" mode="matrix"
        values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 35 -15"
        result="goo" />
      <feComposite in="SourceGraphic" in2="goo" operator="atop" />
    </filter>
  </defs>
</svg>

<div id="cursor" class="Cursor" bind:this={cursor}></div>

<style>
  :global(body) {
    cursor: none;
  }

  .Cursor {
    pointer-events: none !important;
    position: fixed;
    display: block;
    border-radius: 0;
    transform-origin: center center;
    mix-blend-mode: normal;
    top: 0;
    left: 0;
    z-index: 2147483647;
    filter: none;
    will-change: transform;
    transition: filter 0.2s cubic-bezier(.4, 0, .2, 1);
  }

  :global(.Cursor span) {
    position: absolute;
    display: block;
    width: 26px;
    height: 26px;
    border-radius: 20px;
    transform-origin: center center;
  }

  :global(button),
  :global([role="button"]),
  :global(a),
  :global(.clickable),
  :global(input),
  :global(textarea),
  :global(select) {
    cursor: none !important;
  }

  @media (hover: none) {
    .Cursor {
      display: none;
    }
    :global(body) {
      cursor: auto;
    }
  }
</style>