# PySceneDetect 원리

PySceneDetect의 핵심 원리는 단순하다. 영상의 인접 프레임을 비교해서 변화 점수를 만들고, 그 점수가 임계값을 넘으면 장면이 바뀐 지점으로 본다.

```text
video
 -> frame 1
 -> frame 2
 -> frame 3
 -> ...
각 인접 프레임 비교
 -> 변화 점수 계산
 -> threshold 넘으면 cut / scene boundary
 -> scene list 생성
```

PySceneDetect는 화면의 의미를 이해하는 도구라기보다, 화면 변화 후보 timestamp를 뽑는 도구다. 이후 자막 구간과 결합해야 실제 지식 캡처 단위가 된다.

## Detector별 원리

### ContentDetector

가장 기본 detector다. 이전 프레임과 현재 프레임의 색상, 채도, 밝기, edge 차이를 점수화한다.

```text
이전 프레임 vs 현재 프레임
색상 hue 차이
채도 saturation 차이
밝기 luminance 차이
edge 차이
=> content_val 점수
```

슬라이드 전환, 화면 구성 변화, 컷 전환처럼 화면이 크게 바뀌는 경우에 잘 맞는다.

### AdaptiveDetector

ContentDetector의 약점은 카메라 움직임, 손짓, 줌 같은 일반 움직임도 큰 변화로 볼 수 있다는 점이다. AdaptiveDetector는 주변 프레임들의 평균 변화량과 비교해서 지금 순간이 유난히 튀는지를 본다.

```text
평소에도 많이 움직이는 영상인가?
그중에서도 지금 순간이 유난히 튀는가?
```

그래서 talking-head나 일반 영상에는 ContentDetector보다 안정적인 기본값이 될 수 있다.

### HashDetector

프레임을 perceptual hash 같은 작은 fingerprint로 바꾼 뒤, hash 사이의 거리를 비교한다.

```text
이미지 -> 저해상도/저주파 특징 -> hash
hash A vs hash B 거리 비교
```

사람 눈에 비슷한 화면이면 hash도 비슷하게 나온다. 슬라이드나 화면 캡처의 중복 제거에 좋다.

### HistogramDetector

이미지의 밝기/색 분포를 비교한다.

```text
프레임 A의 밝기 분포
프레임 B의 밝기 분포
분포가 많이 바뀌면 장면 전환
```

빠르고 단순하지만, 서로 다른 화면이어도 색 분포가 비슷하면 놓칠 수 있다.

### ThresholdDetector

화면 평균 밝기를 보고 fade-in/fade-out 같은 전환을 찾는다.

```text
화면이 점점 검게 변함
화면이 다시 밝아짐
=> fade boundary
```

## 내 영상 노트 스킬에서의 사용 방식

```text
PySceneDetect = 화면 후보 timestamp 생성기
자막 / VTT = 의미 구간 보정기
helper script = 사진 + 요약 + 주요내용 + 원본 연결기
```

좋은 기본 파이프라인은 다음과 같다.

```text
1. PySceneDetect로 후보 timestamp 추출
2. 00:00 시작 화면 강제 추가
3. min_scene_len으로 너무 촘촘한 후보 제거
4. HashDetector나 imagehash로 중복 제거
5. 각 timestamp 구간에 VTT 원본 자막 붙이기
```

중요한 점은 PySceneDetect가 “이 화면이 중요하다”고 판단하는 것이 아니라, “여기서 화면이 꽤 바뀌었다”고 알려준다는 것이다. 그 후보를 자막 구간과 결합하면 나중에 다시 볼 필요 없이 복원 가능한 영상 노트가 된다.
