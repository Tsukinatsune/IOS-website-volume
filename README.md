### Report: JavaScript `volume` Property Now Affects Web Audio Playback in Safari & Chrome on iPadOS 26.2

**Date:** January 16, 2026  
**Device/OS:** iPad running iPadOS 26.2 (build 23C55)  
**Browser:** Safari & Chrome (version tied to iPadOS 26.2)    

#### Summary
In prior iOS/iPadOS versions, the `volume` property of `<audio>` and `<video>` elements could not be effectively controlled via JavaScript. Setting `element.volume` had no impact on actual playback loudness — volume was strictly tied to the device's hardware/system volume controls. Reading `element.volume` typically returned 1.0, and changes were ignored for user experience and data safety reasons.

In iPadOS 26.2, setting `element.volume` in JavaScript now successfully adjusts the actual playback volume. This is a significant behavior change that enables web apps to implement custom volume sliders.

No official mention of this change appears in Safari 26.2 release notes or WebKit documentation searched as of January 2026. It may be an intentional new feature or an undocumented regression.

#### Steps to Reproduce / Test Case
1. Open Safari on iPadOS 26.2.
2. Load the test page below.
3. Set device system volume to ~50% using hardware buttons.
4. Tap the audio controls to start playback (required due to autoplay policies).
5. Use the buttons to set different volume levels.
6. Listen for changes in actual loudness.
7. Additionally test: Change system volume while web volume is set to 0.5 — does output scale with system volume (multiplicative) or override it (independent)?

**Test HTML Page** (save as .html or paste into a code playground like CodePen and open in Safari & Google chrome):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>iPadOS 26.2 Web Audio Volume Test</title>
    <style>
        body { font-family: sans-serif; padding: 20px; background: #f0f0f0; }
        button { padding: 10px 20px; margin: 10px; font-size: 18px; }
    </style>
</head>
<body>
    <h1>Web Audio Volume Test (iPadOS 26.2)</h1>
    <p>Use this to confirm if JavaScript volume control now works.</p>
    
    <audio id="audio" controls preload="auto">
        <!-- Free test audio from SoundHelix (public domain) -->
        <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" type="audio/mpeg">
        Your browser does not support audio.
    </audio>
    
    <br><br>
    <button onclick="setVol(0.1)">Set to 10% (very quiet)</button>
    <button onclick="setVol(0.5)">Set to 50% (half)</button>
    <button onclick="setVol(1.0)">Set to 100% (full)</button>
    <button onclick="setVol(0)">Mute</button>
    
    <script>
        const audio = document.getElementById('audio');
        
        function setVol(level) {
            audio.volume = Math.max(0, Math.min(1, level)); // Clamp 0–1
            console.log('Volume set to:', audio.volume);
            alert('JS volume set to: ' + audio.volume + '\nCheck if actual sound changed!');
        }
        
        // Auto-play not allowed; user must tap play first
    </script>
    
    <h2>Expected in older iPadOS:</h2>
    <ul>
        <li>Actual loudness unchanged (tied to system volume only).</li>
        <li>audio.volume often reads as 1.0 regardless of setting.</li>
    </ul>
    
    <h2>Observed in 26.2:</h2>
    <ul>
        <li>Actual playback volume adjusts per JS setting.</li>
    </ul>
</body>
</html>
```
