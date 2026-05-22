<script lang="ts">
  import { onMount } from 'svelte';

  interface Particle {
    x: number;
    y: number;
    vx: number;
    vy: number;
    radius: number;
  }

  let container: HTMLDivElement;
  let canvas: HTMLCanvasElement;

  onMount(() => {
    if (!container || !canvas) return;

    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    let animationFrameId: number;
    let width = 0;
    let height = 0;

    let particles: Particle[] = [];
    const maxParticles = 80; // Balanced number of lines
    const connectionDist = 135;
    const mouse = { x: null as number | null, y: null as number | null, radius: 180 };

    const initParticles = (w: number, h: number) => {
      particles = [];
      const count = Math.min(maxParticles, Math.floor((w * h) / 15000)); // Adaptive count based on screen size
      for (let i = 0; i < count; i++) {
        particles.push({
          x: Math.random() * w,
          y: Math.random() * h,
          vx: (Math.random() - 0.5) * 0.35, // Slow speed for elegant look
          vy: (Math.random() - 0.5) * 0.35,
          radius: Math.random() * 1.2 + 0.8,
        });
      }
    };

    const handleResize = (entries: ResizeObserverEntry[]) => {
      for (const entry of entries) {
        const { width: w, height: h } = entry.contentRect;
        width = w;
        height = h;
        if (canvas) {
          canvas.width = w * window.devicePixelRatio;
          canvas.height = h * window.devicePixelRatio;
          ctx.scale(window.devicePixelRatio, window.devicePixelRatio);
          initParticles(w, h);
        }
      }
    };

    const resizeObserver = new ResizeObserver((entries) => {
      handleResize(entries);
    });
    resizeObserver.observe(container);

    const handleMouseMove = (e: MouseEvent) => {
      if (!canvas) return;
      const rect = canvas.getBoundingClientRect();
      mouse.x = e.clientX - rect.left;
      mouse.y = e.clientY - rect.top;
    };

    const handleMouseLeave = () => {
      mouse.x = null;
      mouse.y = null;
    };

    window.addEventListener('mousemove', handleMouseMove);
    if (container) {
      container.addEventListener('mouseleave', handleMouseLeave);
    }

    const animate = () => {
      ctx.clearRect(0, 0, width, height);

      // Draw lines & update particles
      for (let i = 0; i < particles.length; i++) {
        const p1 = particles[i];
        p1.x += p1.vx;
        p1.y += p1.vy;

        // Bounce off walls
        if (p1.x < 0 || p1.x > width) p1.vx *= -1;
        if (p1.y < 0 || p1.y > height) p1.vy *= -1;

        // Force bounds
        if (p1.x < 0) p1.x = 0;
        if (p1.x > width) p1.x = width;
        if (p1.y < 0) p1.y = 0;
        if (p1.y > height) p1.y = height;

        // Draw particle dot itself (faint, elegant)
        ctx.fillStyle = 'rgba(255, 255, 255, 0.15)';
        ctx.beginPath();
        ctx.arc(p1.x, p1.y, p1.radius, 0, Math.PI * 2);
        ctx.fill();

        // Connect lines between nearby particles
        for (let j = i + 1; j < particles.length; j++) {
          const p2 = particles[j];
          const dx = p1.x - p2.x;
          const dy = p1.y - p2.y;
          const dist = Math.sqrt(dx * dx + dy * dy);

          if (dist < connectionDist) {
            const opacity = (1 - dist / connectionDist) * 0.12; // Thin subtle lines
            ctx.strokeStyle = `rgba(255, 255, 255, ${opacity})`;
            ctx.lineWidth = 0.5;
            ctx.beginPath();
            ctx.moveTo(p1.x, p1.y);
            ctx.lineTo(p2.x, p2.y);
            ctx.stroke();
          }
        }

        // Active connection with mouse cursor (plexus effect)
        if (mouse.x !== null && mouse.y !== null) {
          const dx = p1.x - mouse.x;
          const dy = p1.y - mouse.y;
          const dist = Math.sqrt(dx * dx + dy * dy);

          if (dist < mouse.radius) {
            const opacity = (1 - dist / mouse.radius) * 0.18; // Slightly stronger highlighting
            ctx.strokeStyle = `rgba(255, 255, 255, ${opacity})`;
            ctx.lineWidth = 0.6;
            ctx.beginPath();
            ctx.moveTo(p1.x, p1.y);
            ctx.lineTo(mouse.x, mouse.y);
            ctx.stroke();
          }
        }
      }

      animationFrameId = requestAnimationFrame(animate);
    };

    // Run animation
    animate();

    return () => {
      cancelAnimationFrame(animationFrameId);
      resizeObserver.disconnect();
      window.removeEventListener('mousemove', handleMouseMove);
      if (container) {
        container.removeEventListener('mouseleave', handleMouseLeave);
      }
    };
  });
</script>

<div bind:this={container} class="absolute inset-0 w-full h-full overflow-hidden select-none pointer-events-none z-0">
  <canvas bind:this={canvas} class="w-full h-full block opacity-60" />
</div>
