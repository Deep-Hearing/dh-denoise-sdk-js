# Deep Hearing Denoise SDK for Javascript
WebAssembly & AudioWorklet API를 기반으로 한 노이즈 제거 SDK. <br />

아래의 두 파일로 구성됩니다.
- dh-denoise.js
- dh-denoise-processor.js (Audio Worklet Processor 파일)

## Supported Audio Format

### 샘플레이트(SampleRate)
현재 지원되는 샘플레이트는 `16KHz, 48KHz` 입니다.

### 프레임 크기(Frame Size)
`8ms` 프레임 단위로 노이즈 제거 처리를 수행합니다. <br />
16KHz 기준 `128`개, 48KHz 기준 `384`개 입니다.


### 채널(Channel)
현재 노이즈 제거는 <code>1개</code> 채널(<code>mono</code>)로 처리됩니다. bypass하면 입력 채널 수 그대로 출력 됩니다.

### 데이터 타입(Data Type)
데이터 타입은 <code>Float32</code>이며 <code>-1.0 ~ 1.0</code> 범위의 값을 가집니다.

<hr>

## 모듈 적용 방법
<code>dh-denoise.js</code> 를 로드하게 되면 <code>DeepHearing.Denoise</code> 모듈을 통해 노이즈 제거 기능을 사용할 수 있습니다.

### How to initialize
<code>.html</code>
```html
<script src="dh-denoise.js"></script>
```
<code>.js</code>
```javascript
const denoise = window.DeepHearing.Denoise;
```
## API
### async appendDenoiserToStream(stream: MediaStream, processorPath: string) : MediaStream

```javascript 
const streamWithDenoiser = await denoise.appendDenoiserToStream(stream, processorPath);
```

### setDenoiseState(isEnabled: boolean) : void

```javascript 
denoise.setDenoiseState(true/false);
```

### getConstraints(void) : void
권장되는 오디오 `constraints`를 반환합니다.
```javascript 
denoise.getConstraints();
```

### getVersion(void) : void
```javascript 
denoise.getVersion();
```
## Example
```js
const processorPath = './dh-denoise-processor.js';
const audioConstraints = denoise.getConstraints();

const stream = await navigator.mediaDevices.getUserMedia(audioConstraints);
const streamWithDenoiser = await denoise.appendDenoiserToStream(stream, processorPath);

...

denoise.setDenoiseState(true);
```
