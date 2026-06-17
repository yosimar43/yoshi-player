<script>
    import CustomCursor from "../lib/components/Layout/CustomCursor.svelte";

</script>

<div class="app-container">
  <!-- Background effects layer -->
   <CustomCursor />
  <div class="background-effects" aria-hidden="true">
    <!-- Ambient orbs -->
    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>
    <div class="orb orb-3"></div>
    <!-- Amber orbs -->
    <div class="orb orb-amber-1"></div>
    <div class="orb orb-amber-2"></div>
    <div class="orb orb-amber-3"></div>
    <!-- Fuchsia orbs -->
    <div class="orb orb-fuchsia-1"></div>
    <div class="orb orb-fuchsia-2"></div>
    <div class="orb orb-fuchsia-3"></div>
    <!-- Grid lines subtle -->
    <div class="grid-overlay"></div>
    <!-- Noise texture -->
    <div class="noise-overlay"></div>
  </div>
 
  <!-- NavBar fija -->
  <div class="navbar">
  </div>
 
  <main class="main-content">
  </main>
 
  <footer class="footer">
  </footer>
 
 
</div>
 
<style>
  @reference 'tailwindcss';
 
  :global(body) {
    margin: 0;
    padding: 0;
    overflow: hidden;
  }
 
  .app-container {
    @apply relative flex min-h-screen flex-col pb-5 items-center;
    background:
      radial-gradient(ellipse 80% 50% at 50% -20%, rgba(56, 189, 248, 0.15), transparent),
      radial-gradient(ellipse 60% 40% at 100% 100%, rgba(14, 165, 233, 0.1), transparent),
      radial-gradient(ellipse 50% 30% at 0% 80%, rgba(99, 102, 241, 0.08), transparent),
      linear-gradient(to bottom, #0f172a 0%, #1e293b 50%, #334155 100%);
  }
 
  /* ═══════════════════════════════════════════════════════════════
     BACKGROUND EFFECTS
     ═══════════════════════════════════════════════════════════════ */
 
  .background-effects {
    position: fixed;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    overflow: hidden;
  }
 
  /* Base compartida */
  .orb {
    position: absolute;
    border-radius: 50%;
    filter: blur(55px);
    opacity: 0.75;
    will-change: transform, opacity;
    transform: translateZ(0);
  }
 
  /* ─── Ambient orbs ──────────────────────────────────────────── */
 
  .orb-1 {
    width: 500px;
    height: 500px;
    background: radial-gradient(circle, rgba(56, 189, 248, 1) 0%, rgba(56, 189, 248, 0.6) 30%, transparent 70%);
    top: -15%;
    left: -10%;
    animation: float-1 25s ease-in-out infinite alternate;
  }
 
  .orb-2 {
    width: 400px;
    height: 400px;
    background: radial-gradient(circle, rgba(99, 102, 241, 1) 0%, rgba(99, 102, 241, 0.6) 30%, transparent 70%);
    bottom: -10%;
    right: -5%;
    animation: float-2 22s ease-in-out infinite alternate;
    animation-delay: -8s;
  }
 
  .orb-3 {
    width: 300px;
    height: 300px;
    background: radial-gradient(circle, rgba(14, 165, 233, 1) 0%, rgba(14, 165, 233, 0.6) 30%, transparent 70%);
    top: 25%;
    left: 60%;
    animation: float-3 28s ease-in-out infinite alternate;
    animation-delay: -15s;
  }
 
  /* ─── Amber orbs ────────────────────────────────────────────── */
 
  .orb-amber-1 {
    width: 450px;
    height: 450px;
    background: radial-gradient(circle, rgba(245, 158, 11, 1) 0%, rgba(245, 158, 11, 0.6) 30%, transparent 70%);
    top: 10%;
    right: 15%;
    animation: float-4 30s ease-in-out infinite alternate;
    animation-delay: -5s;
  }
 
  .orb-amber-2 {
    width: 350px;
    height: 350px;
    background: radial-gradient(circle, rgba(251, 191, 36, 1) 0%, rgba(251, 191, 36, 0.6) 30%, transparent 70%);
    top: 35%;
    left: 20%;
    animation: float-5 26s ease-in-out infinite alternate;
    animation-delay: -12s;
  }
 
  .orb-amber-3 {
    width: 380px;
    height: 380px;
    background: radial-gradient(circle, rgba(245, 158, 11, 1) 0%, rgba(245, 158, 11, 0.55) 30%, transparent 70%);
    top: 45%;
    right: 40%;
    animation: float-6 24s ease-in-out infinite alternate;
    animation-delay: -18s;
  }
 
  /* ─── Fuchsia orbs ──────────────────────────────────────────── */
 
  .orb-fuchsia-1 {
    width: 420px;
    height: 420px;
    background: radial-gradient(circle, rgba(217, 70, 239, 1) 0%, rgba(217, 70, 239, 0.6) 30%, transparent 70%);
    top: 20%;
    left: 10%;
    animation: float-7 27s ease-in-out infinite alternate;
    animation-delay: -7s;
  }
 
  .orb-fuchsia-2 {
    width: 360px;
    height: 360px;
    background: radial-gradient(circle, rgba(232, 121, 249, 1) 0%, rgba(232, 121, 249, 0.6) 30%, transparent 70%);
    top: 55%;
    right: 25%;
    animation: float-8 29s ease-in-out infinite alternate;
    animation-delay: -14s;
  }
 
  .orb-fuchsia-3 {
    width: 390px;
    height: 390px;
    background: radial-gradient(circle, rgba(217, 70, 239, 1) 0%, rgba(217, 70, 239, 0.55) 30%, transparent 70%);
    top: 38%;
    right: 5%;
    animation: float-9 23s ease-in-out infinite alternate;
    animation-delay: -20s;
  }
 
  /* ─── Keyframes independientes por orbe ─────────────────────── */
  /* Cada uno tiene una trayectoria y escala distinta,             */
  /* replicando el movimiento orgánico aleatorio que hacía GSAP.  */
 
  @keyframes float-1 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.70; }
    30%  { transform: translateZ(0) translate( 28px, -18px) scale(1.05); opacity: 0.85; }
    60%  { transform: translateZ(0) translate(-15px,  28px) scale(0.96); opacity: 0.65; }
    100% { transform: translateZ(0) translate( 22px,  18px) scale(1.03); opacity: 0.80; }
  }
 
  @keyframes float-2 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.70; }
    25%  { transform: translateZ(0) translate(-32px,  22px) scale(1.04); opacity: 0.82; }
    65%  { transform: translateZ(0) translate( 18px, -30px) scale(0.95); opacity: 0.63; }
    100% { transform: translateZ(0) translate(-20px,  10px) scale(1.02); opacity: 0.78; }
  }
 
  @keyframes float-3 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.68; }
    40%  { transform: translateZ(0) translate( 35px, -25px) scale(1.06); opacity: 0.83; }
    70%  { transform: translateZ(0) translate(-25px,  15px) scale(0.94); opacity: 0.60; }
    100% { transform: translateZ(0) translate( 15px,  30px) scale(1.04); opacity: 0.77; }
  }
 
  @keyframes float-4 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.70; }
    35%  { transform: translateZ(0) translate(-28px, -20px) scale(1.05); opacity: 0.84; }
    65%  { transform: translateZ(0) translate( 20px,  32px) scale(0.96); opacity: 0.62; }
    100% { transform: translateZ(0) translate(-18px,  15px) scale(1.03); opacity: 0.79; }
  }
 
  @keyframes float-5 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.68; }
    30%  { transform: translateZ(0) translate( 25px,  22px) scale(1.04); opacity: 0.82; }
    60%  { transform: translateZ(0) translate(-30px, -18px) scale(0.97); opacity: 0.62; }
    100% { transform: translateZ(0) translate( 12px, -28px) scale(1.02); opacity: 0.78; }
  }
 
  @keyframes float-6 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.69; }
    45%  { transform: translateZ(0) translate(-22px,  30px) scale(1.06); opacity: 0.83; }
    75%  { transform: translateZ(0) translate( 30px, -22px) scale(0.95); opacity: 0.61; }
    100% { transform: translateZ(0) translate(-15px, -10px) scale(1.03); opacity: 0.78; }
  }
 
  @keyframes float-7 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.70; }
    25%  { transform: translateZ(0) translate( 32px, -28px) scale(1.05); opacity: 0.85; }
    55%  { transform: translateZ(0) translate(-20px,  20px) scale(0.96); opacity: 0.63; }
    100% { transform: translateZ(0) translate( 18px,  25px) scale(1.04); opacity: 0.80; }
  }
 
  @keyframes float-8 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.68; }
    40%  { transform: translateZ(0) translate(-30px, -15px) scale(1.04); opacity: 0.82; }
    70%  { transform: translateZ(0) translate( 25px,  28px) scale(0.95); opacity: 0.62; }
    100% { transform: translateZ(0) translate(-12px,  18px) scale(1.02); opacity: 0.77; }
  }
 
  @keyframes float-9 {
    0%   { transform: translateZ(0) translate(  0px,   0px) scale(1.00); opacity: 0.69; }
    30%  { transform: translateZ(0) translate( 20px,  30px) scale(1.06); opacity: 0.83; }
    60%  { transform: translateZ(0) translate(-28px, -20px) scale(0.94); opacity: 0.62; }
    100% { transform: translateZ(0) translate( 22px, -15px) scale(1.03); opacity: 0.79; }
  }
 
  /* ─── Grid overlay ──────────────────────────────────────────── */
 
  .grid-overlay {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(56, 189, 248, 0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(56, 189, 248, 0.03) 1px, transparent 1px);
    background-size: 60px 60px;
    mask-image: radial-gradient(ellipse 70% 50% at 50% 50%, black 20%, transparent 70%);
    opacity: 0.5;
  }
 
  /* ─── Noise overlay ─────────────────────────────────────────── */
 
  .noise-overlay {
    position: absolute;
    inset: 0;
    opacity: 0.035;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.8' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
    background-repeat: repeat;
    mix-blend-mode: overlay;
  }
 
  /* ─── Layout ────────────────────────────────────────────────── */
 
  .navbar {
    @apply top-0 right-0 left-0 z-50;
  }
 
  .main-content {
    @apply relative z-10 h-[80vh] pt-12 pb-32 w-3/4 overflow-hidden;
  }
 
  .footer {
    @apply fixed bottom-0 w-full flex justify-center pb-4 z-50;
  }
 
  /* ─── Reducir movimiento ────────────────────────────────────── */
 
  @media (prefers-reduced-motion: reduce) {
    .orb {
      animation: none;
    }
  }
</style>