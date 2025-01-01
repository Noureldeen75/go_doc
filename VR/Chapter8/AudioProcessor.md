# AudioProcessor Script

The **AudioProcessor** script is a complex Unity script designed to process audio data in real-time. It performs spectral analysis, detects beats, and calculates tempo and frequency information. The script also provides event handlers for custom behaviors like visualizing spectrum data or responding to beats.

---

## Full Code Overview
view in unity-vr github

### Features:
- **Real-Time Spectrum Analysis**: Processes audio data using Fourier Transform (`GetSpectrumData`).
- **Onset Detection**: Identifies changes in audio intensity for beat detection.
- **Autocorrelation**: Calculates tempo and periodicity in audio signals.
- **Custom Unity Events**: Provides hooks (`OnBeatEventHandler`, `OnSpectrumEventHandler`) for custom responses to beats or spectrum data.

---

### Key Components:

#### 1. **Variables and Initialization**

```csharp
public AudioSource audioSource;  // Audio source for processing.

public int bufferSize = 1024;  // FFT size.
private int samplingRate = 44100;  // Sampling frequency.
private int nBand = 12;  // Number of frequency bands.
public float gThresh = 0.1f;  // Sensitivity threshold for beat detection.
```

- **`audioSource`**: The `AudioSource` component to analyze.
- **`bufferSize`**: Number of samples processed in each FFT window.
- **`nBand`**: Number of frequency bands for analysis.
- **`gThresh`**: Sensitivity for beat detection.

---

#### 2. **Onset Detection and Autocorrelation**

```csharp
float onset = 0;  // Onset function value.
for (int i = 0; i < nBand; i++) {
    float specVal = Mathf.Max(-100.0f, 20.0f * Mathf.Log10(averages[i]) + 160);
    float dbInc = specVal - spec[i];
    spec[i] = specVal;
    onset += dbInc;
}

auco.newVal(onset);
```

- **Purpose**: Detects sudden changes in audio intensity (onsets).
- **Key Features**:
  - Converts spectrum data into decibel values.
  - Calculates the onset function by comparing the current and previous spectra.

---

#### 3. **Event Handlers**

```csharp
[Header("Events")]
public OnBeatEventHandler onBeat;  // Invoked when a beat is detected.
public OnSpectrumEventHandler onSpectrum;  // Invoked with spectrum data.
```

- **`onBeat`**: Triggers actions in response to beat detection.
- **`onSpectrum`**: Sends frequency band data for visualization or analysis.

---

#### 4. **Autocorrelation Class**

```csharp
private class Autoco
{
    private int del_length;
    private float decay;
    private float[] delays;
    private float[] outputs;

    public void newVal(float val)
    {
        delays[indx] = val;
        for (int i = 0; i < del_length; ++i) {
            outputs[i] += (1 - decay) * (delays[indx] * delays[delix] - outputs[i]);
        }
    }
}
```

- **Purpose**: Calculates tempo by finding periodicities in onset values.

---

## Potential Exam Questions

### True/False

1. **The script uses `GetSpectrumData` for real-time audio analysis.**  
   **Answer**: True  

2. **The `Autoco` class is used for onset detection.**  
   **Answer**: False (It is used for autocorrelation to calculate tempo).  

3. **The script works with any audio file, regardless of sampling rate.**  
   **Answer**: True  

---

### Multiple Choice

1. **What is the purpose of the `computeAverages` method?**  
   - A) Detect beats in the audio.  
   - B) Calculate average energy in frequency bands.  
   - C) Apply a low-pass filter to the audio.  
   - **Answer**: B  

2. **What does the `onBeat` event do?**  
   - A) Updates the background color of the camera.  
   - B) Detects periodicity in audio signals.  
   - C) Triggers a response when a beat is detected.  
   - **Answer**: C  

3. **Which class is responsible for tempo detection?**  
   - A) Autoco  
   - B) OnSpectrumEventHandler  
   - C) AudioSource  
   - **Answer**: A  

---

### Fill in the Blanks

1. The `_________` method analyzes the audio spectrum using Fourier Transform.  
   **Answer**: GetSpectrumData  

2. The `_________` class calculates tempo using autocorrelation.  
   **Answer**: Autoco  

3. The `_________` event is invoked when a beat is detected in the audio.  
   **Answer**: onBeat  

---

### Short Answer

1. **What is the purpose of the `gThresh` variable?**  
   **Answer**: It sets the sensitivity for detecting onsets in the audio, determining the threshold for beat detection.  

2. **How does the script calculate the onset function?**  
   **Answer**: By summing the differences in decibel levels between the current and previous spectrum values across all frequency bands.  

3. **What happens if the `AudioSource` is not attached?**  
   **Answer**: The script will throw a `NullReferenceException` when trying to access the `AudioSource` component.  

---

### Debugging Questions

1. **What happens if `bufferSize` is too large?**  
   **Answer**: The spectrum data will have higher resolution but slower performance, potentially causing latency in beat detection.  

2. **What happens if the `onSpectrum` event is not assigned in the Inspector?**  
   **Answer**: The script will throw a `NullReferenceException` when trying to invoke the event.  

3. **How can you test if beat detection is working correctly?**  
   **Answer**: Attach a script to the `onBeat` event that logs or visually indicates beat detections.  

---

### Code Modification Questions

#### 1. How would you log the detected tempo?

**Answer**: Add a `Debug.Log` statement in the `Update` method to print the tempo:  
```csharp
Debug.Log("Tempo: " + (60 / (tempopd * framePeriod)) + " bpm");
```

---

#### 2. How would you change the camera color on every beat?

**Answer**: Assign the `changeCameraColor` method to the `onBeat` event in the Inspector or programmatically:  
```csharp
onBeat.AddListener(changeCameraColor);
```

---

#### 3. How would you increase the number of frequency bands for analysis?

**Answer**: Modify the `nBand` variable and update the `computeAverages` method to calculate more bands:  
```csharp
public int nBand = 24;  // Increased number of bands.
```
