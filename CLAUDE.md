# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

VoiceInputWidget is a reusable JavaScript widget that enables voice input into text fields via mouse hover. It uses the Web Speech API for speech recognition and Kuromoji.js for Japanese morphological analysis to extract noun phrases.

## Core Architecture

### Main Component: `VoiceInputWidget` class (voice-input-widget.js)

**State Management:**
- `recognition`: Web Speech API instance
- `isRecording`: Current recording status
- `currentIndex`: Cyclic input index for multiple target fields
- `kuromojiTokenizer`: Japanese morphological analyzer instance
- `speechRecognitionAvailable`: Browser capability flag
- `targetInputs`: Array of DOM input elements to populate

### Initialization Flow
1. Constructor validates `targetIds` and retrieves DOM elements
2. `initKuromoji()` loads Kuromoji tokenizer asynchronously if `extractNoun` is enabled
3. `createUI()` creates DOM structure with trigger block and speech bubble
4. `initSpeechRecognition()` tests Speech API availability and sets up event handlers

### Text Processing Modes

**Mode 1: Direct Input (≤ maxLength characters)**
- Recognized text is inserted directly into target input

**Mode 2: Noun Extraction (> maxLength characters)**
- Triggered when `extractNoun: true` AND text exceeds `maxLength`
- `extractFirstNoun()` uses Kuromoji to extract first continuous noun phrase
- Accepts tokens with POS tags: 名詞 (noun), 接頭詞 (prefix), 接尾辞 (suffix)
- Example: "今日は1000円のチャーシューを買いました" → "1000円チャーシュー"

**Mode 3: Noun Phrase Only (extractNounPhraseOnly)**
- Always extracts noun phrases regardless of text length
- Set via `setExtractNounPhraseOnly(true)`

### UI Positioning Modes

**Fixed Mode (default):**
- Positioned at bottom-left of viewport (position: fixed)
- Large trigger block (200px × 200px)

**Inline Mode:**
- Positioned inside parent element of first target input
- Small trigger button (40px × 40px)
- Set via `options.position: 'inline'`

## Key Implementation Details

### Speech Recognition Event Flow
1. `mouseenter` on trigger → `startRecognition()`
2. `onresult` fires continuously with interim and final results
3. Final results trigger `processRecognizedText()`
4. `mouseleave` → `stopRecognition()`

### Cyclic Input Behavior
After each successful recognition, `currentIndex` increments:
```javascript
this.currentIndex = (this.currentIndex + 1) % this.targetInputs.length;
```

### Browser Compatibility Handling
- `initSpeechRecognition()` tests API availability before use
- Falls back to `showUnsupportedMessage()` for unsupported browsers (Brave, Firefox, Tor, LibreWolf)
- Displays compatibility notice with browser recommendations

### Kuromoji Integration
- Dictionary path configurable via `kuromojiDicPath` option (default: `node_modules/kuromoji/dict/`)
- Tokenizer initialized asynchronously during widget initialization
- If Kuromoji fails to load, widget falls back to direct text input

## Configuration Options

```javascript
{
  targetIds: [],              // Required: IDs of input elements
  maxLength: 20,              // Character threshold for noun extraction
  extractNoun: true,          // Enable noun extraction mode
  extractNounPhraseOnly: false, // Always extract nouns (ignore maxLength)
  triggerText: 'ホバーで音声入力',
  activeText: '音声入力中...',
  unsupportedText: '音声認識が利用できません',
  kuromojiDicPath: 'node_modules/kuromoji/dict/',
  position: 'fixed',          // 'fixed' or 'inline'
  onWordExtracted: null       // Callback function(word)
}
```

## Dependencies

- **Web Speech API**: Chrome, Edge, Safari (requires HTTPS or localhost)
- **Kuromoji.js**: Optional, only for noun extraction feature
- **No build system**: Pure vanilla JavaScript (ES6 classes)

## Testing Considerations

- Test on Chrome/Edge for full Web Speech API support
- Safari has limited support (test carefully)
- HTTPS or localhost required for microphone permissions
- Ensure Kuromoji dictionary files are accessible at runtime
