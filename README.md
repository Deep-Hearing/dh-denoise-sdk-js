# 딥히어링 노이즈 제거 SDK
<br>
<h2>WebAssembly & AudioWorklet API를 기반으로 한 노이즈 제거 SDK</h2>

### 샘플레이트(SampleRate)
샘플레이트는 <code>48000Hz</code>로 고정하는 것이 좋습니다.<br>
브라우저의 리샘플링 기능이 더 안정적이기 때문에, 샘플레이트는 <code>48000Hz</code>으로 고정하면<br>
샘플레이트가 다른 마이크 입력에 대해 자연스럽게 리샘플링이 될 수 있습니다.

### 프레임 크기(Frame Size)
노이즈 제거를 위해 매번 처리되는 샘플의 수는 <code>384개</code> 입니다.

### 채널(Channel)
현재 노이즈 제거는 <code>1개</code> 채널(<code>mono</code>)로 처리됩니다. bypass하는 경우는 입력 채널 수 그대로 출력 됩니다.

### 데이터 타입(Data Type)
데이터 타입은 <code>Float32</code>이며 <code>-1.0~1.0</code> 사이의 값을 가집니다.

<hr>

## API
<code>dhdenoise.bundle.js</code> 를 로드하게 되면 <code>DeepHearing.Denoise</code>를 통해 노이즈 제거 기능을 사용할 수 있습니다.

### How to initialize
<code>.html</code>
```html
<script src="https://sdk.deephearing.kr/<company>/dhdenoise.bundle.js"></script>
```
<code>.html</code>
```javascript
const denoise = window.DeepHearing.Denoise;
```

### async appendDenoiserToStream(stream: MediaStream) : MediaStream

```javascript 
const stream = await navigator.mediaDevices.getUserMedia(audioConstraints);
const streamWithDenoiser = await denoise.appendDenoiserToStream(stream);
```

### setDenoiseState(isEnabled: boolean) : void

```javascript 
denoise.setDenoiseState(true/false);
```

### getConstraints(void) : void
권장되는 오디오 constraints를 반환합니다.
```javascript 
denoise.getConstraints();
```

### getVersion(void) : void
```javascript 
denoise.getVersion();
```
