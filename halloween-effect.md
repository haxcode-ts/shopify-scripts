# Halloween Decoration Effect for Shopify

A lightweight Halloween decoration effect for **Best Of Fragrances**
Shopify storefronts. It adds floating bat and pumpkin decorations over
the storefront, with a Pause/Resume control.

## What the effect does

-   Displays floating bat and pumpkin decorations.
-   Uses an amber-orange glow accent (`#E8821A` by default).
-   Uses different decoration counts for desktop and mobile.
-   Animates decorations without blocking clicks or other page
    interactions.
-   Adds a Pause/Resume button in the bottom-right corner.
-   Respects the visitor's `prefers-reduced-motion` accessibility
    setting by hiding the decorations and pause button.

## Installation

> **Before editing:** Duplicate your Shopify theme so you can restore
> the original if needed.

1.  Sign in to your **Shopify Admin**.
2.  Go to **Online Store → Themes**.
3.  Find the theme you want to edit. Open the **...** menu and select
    **Edit code**.
4.  In the **Layout** folder, open `theme.liquid` (or the layout file
    used by your theme).
5.  Find the closing `</body>` tag near the bottom of the file.
6.  Paste the complete code below immediately **before** `</body>`.
7.  Click **Save**.
8.  Preview the storefront on desktop and mobile. Confirm that the
    decorations appear and that the Pause button works.

### Placement example

Paste the code between the end of the page content and the closing body
tag:

``` liquid
  <!-- Paste the complete Halloween effect code here -->

</body>
</html>
```

If your theme uses a separate `footer.liquid` section, do not assume it
is the correct location: Shopify themes commonly render the footer
section before `</body>`. The safest location is in the main layout file
immediately before `</body>`.

## Configuration

At the top of the JavaScript, edit the `SETTINGS` object to adjust the
effect:

  -------------------------------------------------------------------------
  Setting                                     Default Description
  ---------------------- ---------------------------- ---------------------
  `enabled`                                    `true` Master switch. Set to
                                                      `false` to disable
                                                      and remove the effect
                                                      elements.

  `accentColor`                           `'#E8821A'` Colour used for the
                                                      glow and Pause button
                                                      border/text.

  `speedMultiplier`                             `1.0` Animation speed.
                                                      `0.5` is slower;
                                                      `2.0` is faster.

  `desktopBats`                                   `6` Number of bats on
                                                      desktop.

  `desktopPumpkins`                               `4` Number of pumpkins on
                                                      desktop.

  `mobileBats`                                    `3` Number of bats on
                                                      mobile.

  `mobilePumpkins`                                `2` Number of pumpkins on
                                                      mobile.

  `edgePadding`                                  `80` Reserved setting; it
                                                      is not currently used
                                                      by the active
                                                      positioning logic.

  `centerClearPercent`                          `0.6` Reserved for edge
                                                      positioning; the
                                                      current code uses
                                                      random horizontal
                                                      positions instead.
  -------------------------------------------------------------------------

Example: to show fewer decorations and slow the animation down:

``` js
const SETTINGS = {
  enabled: true,
  accentColor: '#E8821A',
  speedMultiplier: 0.7,
  desktopBats: 3,
  desktopPumpkins: 2,
  mobileBats: 2,
  mobilePumpkins: 1,
  edgePadding: 80,
  centerClearPercent: 0.6
};
```

## Disable or remove the effect

**Temporarily disable:** change `enabled: true` to `enabled: false` in
the `SETTINGS` object, then save.

**Remove completely:** delete the Halloween effect code that you pasted
into the theme layout, then save.

## Notes and troubleshooting

-   **Decorations do not appear:** Check that the code is placed before
    the closing `</body>` tag, that it was saved, and that `enabled` is
    `true`.
-   **Pause button does not appear:** Check that the full HTML and
    JavaScript were pasted, including the element with
    `id="halloween-pause-btn"`.
-   **Changes are not visible:** Refresh the storefront. If necessary,
    test in a private/incognito window.
-   **Mobile appearance:** The script switches to the mobile decoration
    counts when the viewport is narrower than 768 pixels.
-   **Performance:** The script uses `requestAnimationFrame` for
    animation. Avoid setting very high decoration counts, especially on
    mobile.
-   **Accessibility:** Visitors whose device requests reduced motion
    will not see the decorations or Pause button.
-   **Theme updates:** A theme update or theme replacement may overwrite
    code added directly to the layout. Keep a copy of this README and
    the snippet.

------------------------------------------------------------------------

## Complete Shopify code

Copy the entire snippet below and paste it into the theme layout
immediately before `</body>`.

``` liquid
{% comment %} Halloween Decoration Effect - Haxcode Technology Solutions (www.haxcode.com) {% endcomment %}
{% comment %} Add to theme.liquid before closing </body> tag {% endcomment %}

<style>
  :root {
    --halloween-accent: #E8821A;
    --halloween-glow: rgba(232, 130, 26, 0.4);
    --halloween-bat: #1a1a2e;
    --halloween-speed: 1;
    --halloween-density: 1;
    --halloween-enabled: 1;
  }

  .halloween-canvas {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 9999;
    overflow: hidden;
  }

  .halloween-deco {
    position: absolute;
    pointer-events: none;
    user-select: none;
    will-change: transform;
  }

  .hween-bat {
    font-size: 26px;
    opacity: 0.75;
    filter: drop-shadow(0 0 4px var(--halloween-glow));
  }

  .hween-pumpkin {
    font-size: 32px;
    opacity: 0.7;
    filter: drop-shadow(0 0 8px var(--halloween-glow)) drop-shadow(0 0 16px var(--halloween-glow));
  }

  .halloween-pause-btn {
    position: fixed;
    bottom: 20px;
    right: 20px;
    z-index: 10000;
    background: rgba(20, 10, 5, 0.85);
    border: 1px solid var(--halloween-accent);
    color: var(--halloween-accent);
    padding: 6px 12px;
    font-size: 12px;
    cursor: pointer;
    border-radius: 4px;
    pointer-events: auto;
    transition: background 0.2s;
    font-family: inherit;
    backdrop-filter: blur(4px);
  }

  .halloween-pause-btn:hover {
    background: rgba(232, 130, 26, 0.15);
  }

  .halloween-pause-btn:focus-visible {
    outline: 2px solid var(--halloween-accent);
    outline-offset: 2px;
  }

  @media (prefers-reduced-motion: reduce) {
    .halloween-deco { display: none !important; }
    .halloween-pause-btn { display: none !important; }
  }
</style>

<div class="halloween-canvas" id="halloween-canvas" aria-hidden="true"></div>
<button class="halloween-pause-btn" id="halloween-pause-btn" aria-label="Pause Halloween animations">🦇 Pause</button>

<script>
(function() {
  // ─── EDITABLE SETTINGS ────────────────────────────────────────────
  const SETTINGS = {
    enabled: true,           // true/false — master switch
    accentColor: '#E8821A',  // amber-orange accent for glow
    speedMultiplier: 1.0,    // 0.5 = slow, 1.0 = normal, 2.0 = fast
    desktopBats: 6,          // number of bats on desktop
    desktopPumpkins: 4,      // number of pumpkins on desktop
    mobileBats: 3,           // bats on mobile
    mobilePumpkins: 2,       // pumpkins on mobile
    edgePadding: 80,         // px — reserved setting; not used by current positioning
    centerClearPercent: 0.6  // reserved setting; current positioning is random
  };
  // ──────────────────────────────────────────────────────────────────

  if (!SETTINGS.enabled) {
    document.getElementById('halloween-canvas')?.remove();
    document.getElementById('halloween-pause-btn')?.remove();
    return;
  }

  // Apply accent color
  document.documentElement.style.setProperty('--halloween-accent', SETTINGS.accentColor);
  document.documentElement.style.setProperty('--halloween-glow', SETTINGS.accentColor + '66');

  const canvas = document.getElementById('halloween-canvas');
  const pauseBtn = document.getElementById('halloween-pause-btn');
  const isMobile = () => window.innerWidth < 768;
  let paused = false;
  let items = [];

  function isEdgeZone(x, pct) {
    const w = window.innerWidth;
    const clearStart = w * (1 - SETTINGS.centerClearPercent) / 2;
    const clearEnd = w - clearStart;
    return x < clearStart || x > clearEnd;
  }

  function edgeX(size) {
    const w = window.innerWidth;
    const clearStart = w * (1 - SETTINGS.centerClearPercent) / 2;
    if (Math.random() < 0.5) {
      return Math.random() * (clearStart - size);
    } else {
      return clearStart + SETTINGS.centerClearPercent * w + Math.random() * (clearStart - size);
    }
  }

  function createDeco(type) {
    const el = document.createElement('div');
    el.className = `halloween-deco hween-${type}`;
    el.textContent = type === 'bat' ? '🦇' : '🎃';
    el.setAttribute('role', 'presentation');

    const startY = type === 'bat'
      ? Math.random() * window.innerHeight * 0.7
      : window.innerHeight * 0.6 + Math.random() * window.innerHeight * 0.35;

    // Random horizontal positioning across the viewport
    const startX = Math.random() * (window.innerWidth - 50);

    el.style.left = startX + 'px';
    el.style.top = startY + 'px';

    const duration = (type === 'bat' ? (8 + Math.random() * 6) : (12 + Math.random() * 8)) / SETTINGS.speedMultiplier;
    const driftX = (Math.random() - 0.5) * 60;
    const driftY = type === 'bat' ? (Math.random() - 0.5) * 80 : (Math.random() - 0.5) * 20;
    const delay = Math.random() * -duration;

    canvas.appendChild(el);

    let start = null;
    let animFrame = null;

    function animate(ts) {
      if (paused) {
        animFrame = requestAnimationFrame(animate);
        return;
      }

      if (!start) start = ts + delay * 1000;
      const elapsed = (ts - start) / 1000;
      const t = ((elapsed % duration) + duration) % duration / duration;
      const wave = Math.sin(t * Math.PI * 2);

      const tx = driftX * wave;
      const ty = driftY * Math.sin(t * Math.PI * 4);
      const rotate = type === 'bat' ? wave * 8 : wave * 3;
      const scale = type === 'pumpkin' ? 1 + Math.abs(wave) * 0.04 : 1;
      const opacity = type === 'pumpkin' ? 0.55 + Math.abs(wave) * 0.2 : 0.65 + Math.abs(wave) * 0.1;

      el.style.transform = `translate(${tx}px, ${ty}px) rotate(${rotate}deg) scale(${scale})`;
      el.style.opacity = opacity;

      animFrame = requestAnimationFrame(animate);
    }

    animFrame = requestAnimationFrame(animate);
    items.push({ el, cancel: () => cancelAnimationFrame(animFrame) });
  }

  function init() {
    items.forEach(i => i.cancel());
    items = [];
    canvas.innerHTML = '';

    const batCount = isMobile() ? SETTINGS.mobileBats : SETTINGS.desktopBats;
    const pumpkinCount = isMobile() ? SETTINGS.mobilePumpkins : SETTINGS.desktopPumpkins;

    for (let i = 0; i < batCount; i++) createDeco('bat');
    for (let i = 0; i < pumpkinCount; i++) createDeco('pumpkin');
  }

  pauseBtn.addEventListener('click', () => {
    paused = !paused;
    pauseBtn.textContent = paused ? '🦇 Resume' : '🦇 Pause';
    pauseBtn.setAttribute('aria-label', paused ? 'Resume Halloween animations' : 'Pause Halloween animations');
  });

  let resizeTimer;
  window.addEventListener('resize', () => {
    clearTimeout(resizeTimer);
    resizeTimer = setTimeout(init, 300);
  });

  init();
})();
</script>
```
