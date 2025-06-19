<template>
    <svg viewBox="0 0 500 320" class="pi-board">
      <!-- Board -->
      <rect x="50" y="50" width="400" height="200" rx="10" class="board" />

      <!-- Mounting holes -->
      <circle v-for="(pos, i) in holePos" :key="i" :cx="pos.x" :cy="pos.y" r="6" class="hole" />

      <!-- GPIO pins -->
      <g class="gpio">
        <rect v-for="i in 20" :key="i"
          :x="60 + ((i - 1) % 2) * 10"
          :y="60 + Math.floor((i - 1) / 2) * 10"
          width="8"
          height="8"
          rx="2"
          class="pin" />
      </g>

      <!-- CPU + RAM -->
      <rect x="200" y="130" width="40" height="40" rx="4" class="chip" />
      <rect x="245" y="130" width="25" height="40" rx="2" class="ram" />

      <!-- Ethernet -->
      <rect x="390" y="60" width="40" height="30" class="ethernet" />
      <rect x="395" y="65" width="30" height="20" class="ethernet-inner" />

      <!-- USB -->
      <g class="usb">
        <rect v-for="i in 2" :key="i" :x="390" :y="100 + i * 25" width="40" height="20" class="usb-port" />
      </g>

      <!-- microSD slot -->
      <rect x="70" y="230" width="60" height="8" rx="2" class="microsd" />

      <!-- Fake traces -->
      <g class="traces">
        <!-- GPIO to chip -->
        <path d="M80 80 H140 V130 H200" />
        <path d="M90 100 H150 V140 H200" />
        <path d="M70 110 H130 V150 H200" />

        <!-- Chip to RAM -->
        <line x1="240" y1="150" x2="245" y2="150" />
        <line x1="240" y1="160" x2="245" y2="160" />

        <!-- RAM to USB -->
        <path d="M270 150 H320 V125 H390" />
        <path d="M270 170 H330 V180 H390" />

        <!-- Chip to Ethernet -->
        <path d="M240 140 H280 V80 H390" />

        <!-- USB to SD -->
        <path d="M390 130 H320 V240 H130" />

        <!-- Decorative traces -->
        <path d="M120 220 H200" />
        <path d="M150 210 V180 H220" />
        <path d="M180 220 V190 H200" />
        <path d="M250 190 H300" />
        <path d="M300 190 V220 H350" />
        <path d="M350 220 H370" />
      </g>

      <!-- Flow paths -->
      <path id="path1" d="M60 60 C130 100, 180 130, 200 150" class="flow" />
      <path id="path2" d="M270 150 C310 170, 360 180, 430 180" class="flow" />

      <!-- Flow particles -->
      <circle r="4" class="particle">
        <animateMotion dur="2s" repeatCount="indefinite">
          <mpath xlink:href="#path1" />
        </animateMotion>
      </circle>
      <circle r="4" class="particle">
        <animateMotion dur="2s" repeatCount="indefinite" begin="1s">
          <mpath xlink:href="#path2" />
        </animateMotion>
      </circle>
    </svg>
</template>

<script>
export default {
  name: "RaspberryAiLoader",
  data() {
    return {
      holePos: [
        { x: 60, y: 60 },
        { x: 440, y: 60 },
        { x: 60, y: 240 },
        { x: 440, y: 240 },
      ],
    };
  },
};
</script>

<style scoped>
.loader {
  background-color: #0b0f13;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.pi-board {
  width: 500px;
  height: 320px;
}

.board {
  fill: #1a2a30;
  stroke: #00ffcc55;
  stroke-width: 1.5;
  filter: drop-shadow(0 0 10px #00ffee33);
}

.hole {
  fill: none;
  stroke: #00ffcc88;
  stroke-width: 1.5;
}

.gpio .pin {
  fill: #00ffcc;
  animation: blink 2s infinite alternate;
}
.gpio .pin:nth-child(even) {
  animation-delay: 1s;
}

.chip {
  fill: #0e1111;
  stroke: #33ff99;
  stroke-width: 2;
  filter: drop-shadow(0 0 6px #33ff99aa);
  animation: pulse 2s ease-in-out infinite alternate;
}

.ram {
  fill: #223;
  stroke: #66ffee88;
  stroke-width: 1;
  filter: drop-shadow(0 0 4px #66ffee88);
}

.ethernet {
  fill: #333;
  stroke: #00ffeeaa;
  stroke-width: 1;
}
.ethernet-inner {
  fill: #0ff;
  opacity: 0.2;
}

.usb-port {
  fill: #0ff2;
  stroke: #33ffff;
  stroke-width: 1;
}

.microsd {
  fill: #222;
  stroke: #44ffaa;
  stroke-width: 1;
}

.traces path,
.traces line {
  stroke: #00ffee33;
  stroke-width: 1.2;
  fill: none;
  stroke-dasharray: 3 2;
  animation: dashflow 4s linear infinite;
}

.flow {
  fill: none;
  stroke: #00ffee;
  stroke-width: 2;
  stroke-dasharray: 5 5;
  animation: dashflow 1.5s linear infinite;
}

.particle {
  fill: #33ffaa;
  filter: drop-shadow(0 0 5px #33ffaa);
}

.text {
  margin-top: 20px;
  font-family: 'Orbitron', monospace;
  color: #00ffcc;
  font-size: 1.2rem;
  letter-spacing: 1.5px;
}

@keyframes dashflow {
  to {
    stroke-dashoffset: -20;
  }
}

@keyframes blink {
  0% { opacity: 0.3; }
  100% { opacity: 1; }
}

@keyframes pulse {
  0% {
    stroke: #33ff99;
    filter: drop-shadow(0 0 8px #33ff99aa);
  }
  100% {
    stroke: #66ffe0;
    filter: drop-shadow(0 0 14px #66ffe0);
  }
}
</style>
