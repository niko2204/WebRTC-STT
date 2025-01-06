### WebRTC STT Example 문서

이 HTML 문서는 **WebRTC**와 **Speech-to-Text(STT)** 기술을 사용하여 실시간 음성 인식 기능을 제공하는 예제입니다. 사용자가 마이크를 통해 말하는 내용을 텍스트로 변환하고, 변환된 텍스트는 화면에 표시됩니다. 특정 단어(예: '혈압')가 인식되면 해당 단어의 색상이 빨간색으로 강조됩니다.

#### 1. HTML 구조
```html
<!DOCTYPE html>
<html>
<head>
    <title>WebRTC STT Example</title>
    <style>
        /* 스타일 정의 */
    </style>
</head>
<body>
    <h1>WebRTC STT Example</h1>
    <div id="videos">
        <video id="localVideo" autoplay></video>
    </div>
    <div id="transcript"></div>

    <script>
        // JavaScript 코드
    </script>
</body>
</html>
```

- `<h1>`: 페이지의 제목을 나타냅니다.
- `<div id="videos">`: 비디오 스트림을 보여줄 컨테이너입니다. 이 안에 사용자 비디오가 표시됩니다.
- `<video id="localVideo">`: 로컬 비디오를 보여줄 비디오 요소입니다. 사용자의 카메라에서 스트리밍되는 비디오를 표시합니다.
- `<div id="transcript">`: 음성 인식 결과로 변환된 텍스트를 표시하는 영역입니다.

#### 2. CSS 스타일
```css
.highlight {
    color: red;
    font-weight: bold;
}
.container {
    display: flex; /* flexbox 레이아웃 사용 */
}

#videos {
    width: 50%;
    height: 50vh;
    overflow: hidden;
    background: #000;
}

#transcript {
    width: 50%;
    color: black;
    background-color: white;
    padding: 10px;
    font-size: 20px;
    white-space: pre-wrap;
    overflow: auto;
    height: 70px;
    direction: rtl;
    text-align: left;
    border: 2px solid red;
    z-index: 1;
}

#localVideo {
    position: relative;
    right: 0;
    top: 0;
    width: 30%;
    height: auto;
    border: 3px solid #fff;
    z-index: 1;
}
```

- `.highlight`: 텍스트에 빨간색과 굵은 글씨를 적용하여 강조 표시합니다.
- `.container`: Flexbox 레이아웃을 사용하여 자식 요소들을 나란히 배치할 수 있게 합니다.
- `#videos`: 비디오 스트림을 보여주는 영역의 스타일을 설정합니다. 비디오 영역은 화면의 50%를 차지하며, 배경색은 검은색입니다.
- `#transcript`: 음성 인식된 텍스트를 표시할 영역의 스타일을 설정합니다. 배경색은 흰색이고, 텍스트가 흐르지 않도록 자동으로 스크롤됩니다.
- `#localVideo`: 사용자의 카메라 비디오를 표시하는 영역입니다. 화면의 30%를 차지하고, 흰색 테두리가 있습니다.

#### 3. JavaScript 코드
```javascript
const localVideo = document.getElementById('localVideo');

// Get user media
navigator.mediaDevices.getUserMedia({ video: true, audio: true })
    .then(stream => {
        localVideo.srcObject = stream;

        const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition || window.mozSpeechRecognition || window.msSpeechRecognition)();
        recognition.lang = 'ko-KR'; // 언어를 한국어로 설정
        recognition.interimResults = false; // 중간 결과 반환 안함
        recognition.maxAlternatives = 1; // 최대 대체 결과 수

        recognition.start();

        recognition.onresult = function(event) {
            const transcriptDiv = document.getElementById('transcript');
            let transcript = 'local: ' + event.results[0][0].transcript + '\n';

            // "혈압" 문자가 발견되면 강조 표시
            transcript = transcript.replace(/혈압/g, '<span class="highlight">혈압</span>');
            transcriptDiv.innerHTML += transcript;

            // 스크롤 위치를 아래로 설정
            transcriptDiv.scrollTop = transcriptDiv.scrollHeight;
        };

        recognition.onend = function() {
            recognition.start(); // 인식이 끝나면 다시 시작
        };
    })
    .catch(error => console.error('getUserMedia error:', error));
```

1. **`navigator.mediaDevices.getUserMedia()`**: 사용자의 마이크와 카메라에 접근하여 스트림을 가져옵니다. 성공적으로 스트림을 가져오면 `localVideo` 요소에 스트림을 연결하여 사용자의 비디오를 표시합니다.
   
2. **`SpeechRecognition` 객체**: Web Speech API를 사용하여 음성 인식을 설정합니다.
   - `recognition.lang = 'ko-KR';`: 음성 인식 언어를 한국어로 설정합니다.
   - `recognition.interimResults = false;`: 중간 결과는 표시하지 않도록 설정합니다.
   - `recognition.maxAlternatives = 1;`: 인식된 텍스트의 최대 대체 결과 수를 1로 설정합니다.

3. **음성 인식 결과 처리**:
   - `recognition.onresult`: 음성이 텍스트로 변환될 때마다 호출됩니다. 결과 텍스트는 `#transcript` 요소에 추가되고, 특정 단어(예: '혈압')는 강조됩니다.
   - `transcript.replace(/혈압/g, '<span class="highlight">혈압</span>');`: '혈압'이라는 단어를 강조 표시합니다.
   - `transcriptDiv.scrollTop = transcriptDiv.scrollHeight;`: 텍스트가 추가될 때마다 스크롤을 맨 아래로 이동시켜 최신 텍스트가 항상 보이도록 합니다.

4. **`recognition.onend`**: 음성 인식이 끝나면 인식을 재시작하여 계속해서 음성을 처리하도록 설정합니다.

#### 4. 결론
이 예제는 WebRTC와 Speech-to-Text API를 활용하여 실시간 음성 인식 기능을 구현한 간단한 웹 애플리케이션입니다. 사용자의 음성을 텍스트로 변환하고, 변환된 텍스트는 화면에 실시간으로 표시됩니다. 특정 단어를 강조하는 기능도 포함되어 있어, 음성 데이터 분석에 유용하게 사용될 수 있습니다.
