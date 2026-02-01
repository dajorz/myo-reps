# Myo Reps Gym Tracker

A lightweight, elegant HTML-based training companion for tracking Myo Reps workouts at the gym.

**Available Languages:** English (default) | Español

## About Myo Reps

Myo Reps is a high-intensity resistance training technique that maximizes metabolic stress in minimal time:

1. **Activation Set**: Perform 15 reps to failure (RPE 10)
2. **Short Rest**: 15 seconds (~5 deep breaths)
3. **Mini-Sets**: Do 5 reps, rest 15 seconds, repeat
4. **Completion**: Continue the cycle until you can't complete 5 reps with good form

This page helps you track your mini-series count and rest periods during your Myo Reps training sessions.

## Features

- **Simple, Elegant UI**: Clean dark theme optimized for portrait orientation
- **Configurable Rest Timer**: Adjust rest duration (default: 15 seconds)
- **Visual Countdown**: Large countdown display during rest periods
- **Mini-Series Counter**: Track total mini-series completed (excluding activation set)
- **Session State Tracking**: Clear indication of current phase (Activation → Rest → Mini-Series)
- **Manual Controls**: Pause/resume rest timer, reset session
- **Local Storage**: Remembers your rest time preference between sessions
- **No Dependencies**: Pure HTML, CSS, and JavaScript - works offline

## How to Use

1. Open `index.html` in any modern web browser
2. Configure your rest time (default: 15 seconds)
3. Start with the activation set (15 reps to failure)
4. Press "Comenzar Descanso" (Start Rest) when ready
5. Watch the countdown timer during your rest period
6. Complete your mini-series (5 reps)
7. Press "Siguiente Descanso" (Next Rest) to continue the cycle
8. Use "Reiniciar" (Reset) to start a new session

## Features Breakdown

### Configuration Panel
- **Descanso (seg)**: Set your rest duration in seconds
- **Reiniciar**: Reset the session and mini-series counter

### Main Display
- Large prominent counter showing total mini-series completed
- Current phase indicator (Activation, Rest, Mini-Series)
- Visual countdown timer (only appears during rest)

### Controls
- **Comenzar Descanso**: Start the rest period after activation set
- **Pausar/Reanudar**: Pause and resume the timer during rest
- **Siguiente Descanso**: Complete current mini-series and start rest
- **Cancelar**: Cancel current rest and return to activation state

## Design Philosophy

- **Minimal**: No unnecessary features, focus on training
- **Tactile**: Large buttons for easy interaction while fatigued
- **Visual**: Clear state transitions and countdown feedback
- **Responsive**: Optimized for mobile devices held in portrait
- **Fast**: No server required, instant local operation

## Technical Details

- **No Framework**: Pure vanilla JavaScript, HTML5, CSS3
- **Offline First**: Works completely offline
- **Local Storage**: Persists rest time preference
- **Mobile Optimized**: Portrait-first responsive design
- **Dark Theme**: Easy on the eyes in gym lighting

## Browser Compatibility

Works in all modern browsers:
- Chrome/Edge 90+
- Firefox 88+
- Safari 14+
- Mobile browsers (iOS Safari, Chrome Mobile)

## Tips for the Gym

- Save the page to your phone's home screen for quick access
- Keep phone at arm's reach during rest periods
- The visual countdown eliminates the need to count manually
- Reset session for each new exercise or training day
- Your rest time preference is automatically saved

## License

Open source and free to use. Designed for personal fitness tracking.

---

**Myo Reps Gym Tracker** - Train smarter, not harder.
