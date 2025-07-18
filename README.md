# Rust Ticker

A Rust-based timer and stopwatch library compiled to WebAssembly for use in JavaScript and TypeScript applications.

## Features

- **Timer**: Countdown from a specified duration
- **Stopwatch**: Measure elapsed time
- **WebAssembly**: High-performance timing functionality
- **JavaScript Fallback**: Works even in environments without WebAssembly support
- **Multiple Formats**: Support for bundlers, Node.js, and direct browser usage

## Installation

```bash
npm install rust-ticker
```

## Usage

### Timer

The Timer provides a countdown functionality from a specified time.

```javascript
import { Timer } from 'rust-ticker';

// Create a timer for 1 hour, 30 minutes, 15 seconds
const timer = new Timer(1, 30, 15);

// Access timer properties
console.log(`Total duration: ${timer.duration} seconds`);
console.log(`Hours: ${timer.hours}`);
console.log(`Minutes: ${timer.minutes}`);
console.log(`Seconds: ${timer.seconds}`);

// Start the timer - returns a Promise that resolves when timer completes
timer.start().then(() => {
  console.log('Timer completed!');
});
```

### Stopwatch

The Stopwatch provides timing functionality to track elapsed time.

```javascript
import { Stopwatch } from 'rust-ticker';

// Create a new stopwatch
const stopwatch = new Stopwatch();

// Start the stopwatch
stopwatch.start();

// Check if it's running
console.log(`Stopwatch running: ${stopwatch.is_running}`);

// After some time, stop the stopwatch
setTimeout(() => {
  const elapsed = stopwatch.stop();
  console.log(`Elapsed time: ${elapsed} seconds`);

  // Reset if needed
  stopwatch.reset();
}, 5000);
```

## Different Module Formats

This package supports multiple JavaScript environments:

### ES Modules (Bundlers like webpack, Rollup, etc.)

```javascript
import { Timer, Stopwatch } from 'rust-ticker';

// No need to initialize - just use directly!
const timer = new Timer(0, 0, 10);
// Use the timer...
```

### Node.js

```javascript
const { Timer, Stopwatch } = require('rust-ticker');

// No need to initialize - just use directly!
const timer = new Timer(0, 1, 30);
// Use the timer...
```

### Direct Browser Usage

```html
<script type="module">
  import { Timer, Stopwatch } from './node_modules/rust-ticker/dist/web/clockjs.js';

  // No need to initialize - just use directly!
  const timer = new Timer(0, 1, 30);
  // Use the timer...
</script>
```

## Browser Example

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Clock Timer Demo</title>
</head>
<body>
  <h1>Clock Timer Demo</h1>

  <div>
    <h2>Timer</h2>
    <div id="timer-display">0:0:0</div>
    <button id="start-timer">Start 10s Timer</button>
  </div>

  <div>
    <h2>Stopwatch</h2>
    <div id="stopwatch-display">0:0:0</div>
    <button id="start-stopwatch">Start</button>
    <button id="stop-stopwatch">Stop</button>
    <button id="reset-stopwatch">Reset</button>
  </div>

  <script type="module">
    import { Timer, Stopwatch } from 'rust-ticker';

    // Timer example
    const timerDisplay = document.getElementById('timer-display');
    const startTimerBtn = document.getElementById('start-timer');

    startTimerBtn.addEventListener('click', () => {
      const timer = new Timer(0, 0, 10); // 10 second timer

      // Update display every second
      const interval = setInterval(() => {
        const remaining = timer.duration - Math.floor((Date.now() - startTime) / 1000);
        if (remaining >= 0) {
          const h = Math.floor(remaining / 3600);
          const m = Math.floor((remaining % 3600) / 60);
          const s = remaining % 60;
          timerDisplay.textContent = `${h}:${m}:${s}`;
        }
      }, 100);

      const startTime = Date.now();
      timer.start().then(() => {
        clearInterval(interval);
        timerDisplay.textContent = '0:0:0';
        console.log('Timer completed!');
      });
    });

    // Stopwatch example
    const stopwatchDisplay = document.getElementById('stopwatch-display');
    const startStopwatchBtn = document.getElementById('start-stopwatch');
    const stopStopwatchBtn = document.getElementById('stop-stopwatch');
    const resetStopwatchBtn = document.getElementById('reset-stopwatch');

    const stopwatch = new Stopwatch();
    let displayInterval;

    startStopwatchBtn.addEventListener('click', () => {
      stopwatch.start();

      // Update display every 100ms for smooth UI
      displayInterval = setInterval(() => {
        const time = stopwatch.current_time;
        const h = Math.floor(time / 3600);
        const m = Math.floor((time % 3600) / 60);
        const s = time % 60;
        stopwatchDisplay.textContent = `${h}:${m}:${s}`;
      }, 100);
    });

    stopStopwatchBtn.addEventListener('click', () => {
      const elapsed = stopwatch.stop();
      clearInterval(displayInterval);
      console.log(`Elapsed time: ${elapsed} seconds`);
    });

    resetStopwatchBtn.addEventListener('click', () => {
      stopwatch.reset();
      stopwatchDisplay.textContent = '0:0:0';
    });
  </script>
</body>
</html>
```

## Development

### Building from Source

If you want to build this library from source:

1. Install Rust and wasm-pack:
   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
   ```

2. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/clock-timer.git
   cd clock-timer
   ```

3. Build the WebAssembly modules:
   ```bash
   wasm-pack build --target bundler --out-dir dist/bundler
   wasm-pack build --target web --out-dir dist/web
   wasm-pack build --target nodejs --out-dir dist/node
   ```


## License

MIT
