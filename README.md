# 딥히어링 노이즈 제거 SDK

## WebAssembly & AudioWorklet API를 기반으로 한 노이즈 제거 SDK 입니다.

SDK는 아래 파일로 구성됩니다. 
> 1. dh_module.js
    >>.wasm 파일과 glue 코드가 합쳐진 WebAssembly module 파일입니다.
> 2. wasm-audio-helper.js
    >>RingBuffer를 사용하기 위한 코드입니다. 구글 샘플에서 가져왔습니다
> 3. wasm-worklet-processor.js
    >>AudioWorklet을 이용해 오디오를 process하기 위한 코드입니다.

<hr>

## 샘플레이트(SampleRate)
샘플레이트는 48000 고정입니다.<br>
브라우저에서 제공하는 리샘플링 기능이 더 안정적이기 때문에 샘플레이트는 <code>48000</code>으로 고정하여<br>
다른 샘플레이트로 들어오는 마이크 입력에 대해 자연스럽게 리샘플링이 되도록 하였습니다.

<hr>

## 프레임 크기(Frame Size)
노이즈 제거를 위해 매번 처리되는 버퍼의 크기는 <code>384개</code> 입니다.

<hr>

## 채널(Channel)
현재 노이즈 제거는 <code>1개</code> 채널(<code>mono</code>)로 처리됩니다. bypass하는 경우는 입력 채널 수 그대로 출력 됩니다.

<hr>

## 데이터 타입(Data Type)
데이터 타입은 Float32이며 <code>-1.0~1.0</code> 사이의 값을 가집니다.

<hr>

## 예제 코드(Code Example)
getUserMedia를 통해 마이크 스트림을 받은 이후 AudioWorklet Node를 연결하는 부분에 대한 코드 입니다.

```javascript 
const DEFAULT_SAMPLE_RATE = 48000;
const BASE_AUDIO_CONSTRAINTS = {
  autoGainControl: true,
  googAutoGainControl: true,
  googAutoGainControl2: true,
  echoCancellation: true,
  googEchoCancellation: true,
  noiseSuppression: false,
  googNoiseSuppression: false,
  googHighpassFilter: false,
  googTypingNoiseDetection: false,
};

const audioSource = audioInputSelect.value;
navigator.mediaDevices
  .getUserMedia({
    audio: Object.assign(Object.assign({}, BASE_AUDIO_CONSTRAINTS), {
      deviceId: audioSource ? { exact: audioSource } : "default",
    }),
    video: true,
  })
  .then(async (stream) => {
    // Display your local video in #localVideo element
    localVideo.srcObject = stream;
    const videoTrack = stream.getVideoTracks()[0];

    const options = {};
    if (navigator.mediaDevices.getSupportedConstraints().sampleRate) {
      options.sampleRate = DEFAULT_SAMPLE_RATE;
    }

    const audioContext = new (window.AudioContext || window.webkitAudioContext)(
      options
    );
    processedStream = audioContext.createMediaStreamDestination();
    source = audioContext.createMediaStreamSource(stream);

    audioContext.audioWorklet
      .addModule("js/wasm-worklet-processor.js")
      .then(() => {
        dhWorkletNode = new AudioWorkletNode(
          audioContext,
          "wasm-worklet-processor",
          {
            processorOptions: {
              sampleRate: audioContext.sampleRate,
            },
          }
        );
        source.connect(dhWorkletNode).connect(processedStream);
        paramDenoise = dhWorkletNode.parameters.get("denoise");
      });

    processedStream.stream.addTrack(videoTrack);
    // Add your stream to be sent to the conneting peer
    processedStream.stream
      .getTracks()
      .forEach((track) => pc.addTrack(track, processedStream.stream));
  }, onError);
```

노이즈 제거 기능을 토글 등으로 켜고 끄기위해 사용할 수 있습니다.<br>
paramDenoise를 이용해 값을 설정해 주시면 됩니다.
```javascript 
function denoise() {
  isDenoiseOn = !isDenoiseOn;
  paramDenoise.setValueAtTime(Number(isDenoiseOn), 0);
}
```

더 이상 dhModule을 사용하지 않게 되는 경우 메모리 할당 해제를 위해 destroy함수를 호출해야 합니다.<br>
workletnode를 통해 호출할 수 있습니다.
```javascript 
dhWorkletNode.port.postMessage("stop");
```

<hr>

<h2><a href="https://aws.github.io/amazon-chime-sdk-js/modules/amazonvoice_focus.html#browser-compatibility">브라우저 지원 여부</a></h2>

|Browser| Minimum supported version | Preferred version | Notes |
|-------|---------------------------|-------------------|-------|
|Firefox|76|83+||
|Chromium-based<br>browsers and environments,<br>including Edge and Electron|78|87+||
|Safari|14.1|-||
|Android browser|78*|87*||
|IOS Safari|IOS14|-||
|IOS Chrome|IOS14|-||
|IOS Firefox|IOS14|-||

