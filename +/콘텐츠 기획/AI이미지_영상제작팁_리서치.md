# AI 이미지·영상 제작 팁 리서치 종합

조사 기준일: 2026-06-28  
목표: AI 캐릭터/숏폼 계정 운영에 바로 쓸 수 있는 이미지·영상 제작 팁, 워크플로우, 템플릿, 리스크 체크리스트 정리

## 1. 핵심 결론

AI 캐릭터 숏폼은 “좋은 프롬프트 한 번”이 아니라 “반복 가능한 제작 시스템”으로 만들어야 안정적입니다.

가장 신뢰도 높은 패턴은 다음입니다.

1. 캐릭터 바이블을 먼저 만든다.
2. 기준 얼굴/의상/표정/포즈 이미지를 자산 라이브러리로 고정한다.
3. 이미지는 `정체성`, `스타일`, `구도`를 분리해서 제어한다.
4. 영상은 안정된 스틸 이미지를 먼저 만든 뒤 image-to-video로 짧게 움직인다.
5. 한 클립에는 하나의 동작만 넣고, 2-5초 단위로 잘라 편집한다.
6. 음성, 자막, 효과음, 컷 편집이 최종 품질을 좌우한다.
7. 플랫폼 정책과 저작권, 초상권, 음원 권리는 제작 단계에서 같이 체크한다.

## 2. 캐릭터 제작 시스템

### 캐릭터 바이블

최소 항목:

- 이름, 나이대, 직업, 말투, 세계관
- 얼굴 특징 3-5개
- 헤어스타일, 의상 규칙, 액세서리
- 표정 범위: 기본, 놀람, 짜증, 기쁨, 피곤함
- 금지 요소: 바뀌면 안 되는 머리색, 피부톤, 체형, 소품
- 말투 규칙: 문장 길이, 자주 쓰는 표현, 금지어
- 광고/협찬/민감 소재 대응 원칙

실전 팁:

- 얼굴 특징은 너무 길게 쓰지 않는다. 예: “짧은 검은 단발, 얇은 금테 안경, 왼쪽 볼 작은 점”.
- 매 프롬프트의 정체성 문장은 동일하게 유지한다.
- 세계관보다 먼저 얼굴, 의상, 실루엣을 고정한다.
- “직장인 AI 캐릭터”라면 책상, 노트북, 사원증, 지하철, 회의실 같은 반복 배경을 함께 만든다.

### 기준 이미지 세트

먼저 만들어둘 자산:

- 정면 얼굴 1장
- 3/4 얼굴 2장
- 측면 1장
- 상반신 2장
- 전신 1장
- 표정 시트 1장
- 기본 의상 2-3벌
- 반복 배경 3-5개
- 썸네일용 1:1 크롭
- 쇼츠용 9:16 마스터 이미지

이 세트가 있어야 이후 이미지, 영상, 썸네일, 릴스 커버가 같은 인물로 보입니다.

## 3. 이미지 생성 팁

### 공통 프롬프트 구조

```text
[정체성]
same character as reference, Korean office worker woman, short black bob haircut, thin gold glasses, small mole on left cheek

[상황]
sitting at a desk late at night, laptop open, coffee cup, tired expression

[카메라]
vertical 9:16, medium close-up, eye-level camera

[조명/스타일]
soft office lighting, realistic social media portrait, clean background

[품질/제외]
natural hands, readable face, no extra fingers, no distorted glasses, no random text
```

### 정체성, 스타일, 구도를 분리한다

- 정체성: 얼굴, 헤어, 체형, 의상, 액세서리
- 스타일: 실사, 웹툰, 시네마틱, 광고 사진, 앱 썸네일
- 구도: 전신, 상반신, 클로즈업, 로우앵글, 오버숄더

정체성과 스타일을 한 문장에 섞으면 캐릭터가 흔들립니다. 특히 스타일 프롬프트가 강하면 얼굴도 같이 바뀌는 경우가 많습니다.

### Midjourney 팁

- `--cref`로 캐릭터 레퍼런스를 넣는다.
- `--cw`로 캐릭터 반영 강도를 조절한다.
- `--sref`로 스타일 레퍼런스를 넣는다.
- `--sw`로 스타일 강도를 조절한다.
- 캐릭터 유지가 목표면 `--cref`와 `--sref`를 분리해서 쓴다.
- `--cw 100`은 가능한 많은 캐릭터 디테일을 유지하고, `--cw 0`은 주로 얼굴 중심으로 반영한다.

출처: [Midjourney Character Reference](https://docs.midjourney.com/hc/en-us/articles/32162917505293-Character-Reference), [Midjourney Style Reference](https://docs.midjourney.com/hc/en-us/articles/32180011136653-Style-Reference)

### Stable Diffusion / FLUX 계열 팁

반복 제작이 중요하면 다음 조합이 가장 강합니다.

- 캐릭터 LoRA: 장기적인 인물 고정
- IP-Adapter FaceID: 얼굴 정체성 유지
- ControlNet OpenPose: 포즈 고정
- ControlNet Depth/Canny: 구도와 구조 고정
- 스타일 LoRA: 그림체/브랜드 톤 고정

실전 규칙:

- 얼굴이 흔들리면 스타일 강도를 먼저 낮춘다.
- 포즈가 흔들리면 ControlNet 강도를 올린다.
- 너무 뻣뻣하면 참조 강도를 낮춘다.
- 손, 안경, 사원증, 노트북 키보드처럼 깨지기 쉬운 요소는 후보를 많이 뽑아 고른다.

출처: [Hugging Face Diffusers IP-Adapter](https://huggingface.co/docs/diffusers/main/en/using-diffusers/ip_adapter), [Hugging Face Diffusers ControlNet](https://huggingface.co/docs/diffusers/main/en/using-diffusers/controlnet)

### OpenAI 이미지 입력/편집 팁

- 텍스트와 이미지 입력을 함께 쓰는 편집형 워크플로우에 적합하다.
- 여러 이미지를 함께 넣어 기준 얼굴, 의상, 배경을 설명할 수 있다.
- 캐릭터 일관성을 원하면 깨끗한 정면/3/4 기준 이미지를 사용한다.
- 파노라마, 어안, 극단적 크롭은 피한다.

출처: [OpenAI Images and Vision guide](https://developers.openai.com/api/docs/guides/images-vision)

## 4. 영상 생성 팁

### 기본 원칙

영상은 처음부터 텍스트만으로 만들기보다, 잘 나온 스틸 이미지를 먼저 만들고 image-to-video로 움직이는 편이 안정적입니다.

가장 안전한 구조:

1. 캐릭터 기준 이미지 생성
2. 장면별 마스터 스틸 생성
3. 각 스틸을 2-5초 영상으로 변환
4. 컷 편집으로 연결
5. 자막, 보이스, 효과음 추가

### image-to-video 프롬프트 구조

```text
subtle head turn to camera, gentle breathing, slight tired smile, slow push-in camera, soft office light flicker, natural micro expression, no scene change
```

중요한 점:

- 이미지에 이미 있는 인물 설명을 다시 길게 쓰지 않는다.
- 프롬프트는 움직임, 카메라, 타이밍 중심으로 쓴다.
- 한 클립에 하나의 핵심 동작만 넣는다.

Runway Gen-4.5 문서는 image-to-video 프롬프트가 장면의 움직임에 집중해야 한다고 안내합니다. 또한 2-10초 클립, 24/25fps, 9:16 등 여러 비율을 지원합니다.  
출처: [Runway Gen-4.5 docs](https://help.runwayml.com/hc/en-us/articles/46974685288467-Creating-with-Gen-4-5)

### 안전한 모션 목록

성공률 높은 동작:

- 살짝 고개 돌리기
- 눈 깜빡임
- 숨 쉬는 듯한 미세 움직임
- 살짝 미소
- 카메라 slow push-in
- 약한 좌우 패닝
- 고정된 인물 + 배경의 미세한 조명 변화

실패율 높은 동작:

- 빠르게 걷기
- 손가락으로 작은 물건 조작
- 컵 들고 마시기
- 복잡한 춤
- 옆모습에서 정면으로 크게 회전
- 여러 인물이 서로 상호작용
- 화면 밖에서 소품이 들어오거나 나가는 동작

### 쇼트 길이

- 안정성 우선: 2-5초
- 단순한 장면: 5-10초
- 대사 중심: 짧은 문장 단위로 분할
- 15초 쇼츠라면 3-6개 쇼트로 나눈다.

긴 한 번의 생성보다 짧은 클립 여러 개를 편집하는 편이 얼굴 유지와 리듬 모두에서 유리합니다.

### 립싱크 팁

- 음성을 먼저 확정한다.
- 얼굴은 정면 또는 약한 3/4 구도로 둔다.
- 긴 문장보다 짧은 문장 단위로 만든다.
- 말이 많은 구간에서는 고개 회전을 줄인다.
- 입 주변이 가려지는 컵, 손, 마스크는 피한다.

HeyGen은 사진/영상/프롬프트 기반 아바타와 자연스러운 립싱크, 다국어 번역을 공개적으로 강조합니다.  
출처: [HeyGen](https://www.heygen.com/)

## 5. 음성, 자막, 편집 팁

### 음성 제작

순서:

1. 대본 작성
2. TTS 또는 직접 녹음
3. 노이즈 제거
4. 문장 사이 pause 조절
5. 영상 컷에 맞춰 재배치
6. 효과음과 배경음을 마지막에 추가

팁:

- 캐릭터마다 하나의 고정 음색을 둔다.
- 감정 태그를 짧게 쓴다. 예: “피곤하지만 웃긴 톤”, “속삭이는 불만”.
- 대사가 빠르면 자막이 읽히지 않는다.
- 짧은 침묵은 코미디 타이밍을 만든다.

ElevenLabs는 TTS, 음성 복제, 효과음, 더빙, 음성 분리 기능을 제공합니다.  
출처: [ElevenLabs](https://elevenlabs.io/)

### 자막

좋은 숏폼 자막:

- 한 줄 10-16자 안팎으로 짧게 쪼갠다.
- 핵심 단어만 강조색을 쓴다.
- 플랫폼 UI에 가리지 않도록 가장자리와 하단을 피한다.
- 말소리보다 살짝 빠르게 뜨고, 늦게 사라지지 않게 한다.
- 모든 단어를 강조하지 않는다.

### 한국어 제작 도구

한국형 워크플로우에서 가장 실용적인 조합:

- Vrew: 한국어 자동자막, AI 목소리, 무음 구간 제거, 쇼츠 템플릿
- CapCut: 빠른 컷 편집, 자막 스타일, TTS, 템플릿, 노이즈 감소
- MiriCanvas: 썸네일, 템플릿 기반 이미지/영상, 디자인 자산

Vrew는 한국어 페이지에서 자동 자막, 500개 이상 AI 목소리, 텍스트 기반 영상 제작, 쇼츠 템플릿, 무음 구간 제거, 번역 자막 등을 강조합니다.  
출처: [Vrew 한국어 사이트](https://vrew.ai/ko/), [Vrew 튜토리얼](https://vrew.imweb.me/tutorial), [Vrew FAQ](https://vrew.imweb.me/faq)

CapCut은 자동 자막, TTS, 커스텀 보이스, 노이즈 감소, 템플릿, HD 내보내기 등을 제공합니다.  
출처: [CapCut](https://www.capcut.com/), [CapCut Text to Speech](https://www.capcut.com/tools/text-to-speech)

MiriCanvas는 텍스트/이미지 기반 영상, AI 이미지 생성, 템플릿, 애니메이션 효과를 제공합니다.  
출처: [MiriCanvas](https://www.miricanvas.com/en)

## 6. 숏폼 플랫폼 제작 팁

### 공통

- 첫 1초에 결과, 문제, 반전, 손해를 보여준다.
- 로고 인트로는 넣지 않는다.
- 한 영상은 하나의 약속만 한다.
- 첫 프레임만 봐도 주제를 알 수 있어야 한다.
- 9:16, 1080x1920을 기본 마스터로 둔다.
- 썸네일/커버 텍스트는 짧게 쓴다.
- CTA는 가치 제공 뒤에 한 번만 넣는다.

### YouTube Shorts

- YouTube Shorts는 세로 또는 정사각형 영상으로 최대 3분까지 지원됩니다.
- 제목과 썸네일은 발견성에 중요합니다.
- YouTube는 영상 비율과 해상도 가이드를 제공합니다.

출처: [YouTube Shorts 시작하기](https://support.google.com/youtube/answer/10059070), [3분 Shorts 안내](https://support.google.com/youtube/answer/15424877), [Shorts 업로드](https://support.google.com/youtube/answer/12779649), [YouTube 비율/해상도](https://support.google.com/youtube/answer/6375112), [썸네일/제목 팁](https://support.google.com/youtube/answer/12340300)

### Instagram Reels / TikTok

- Instagram과 TikTok의 세부 추천 규칙은 자주 바뀌므로 업로드 시점의 앱/공식 도움말을 확인해야 합니다.
- 실무적으로는 짧은 훅, 큰 자막, 안전영역, 반복 시청 가능한 컷 구성이 중요합니다.
- Reels는 커버 이미지가 피드에서 중요하므로 첫 프레임과 커버를 따로 설계하는 편이 좋습니다.

참고 보도: [The Verge - Instagram best practices](https://www.theverge.com/2024/10/1/24259462/instagram-best-practices-business-profiles-tips-reach), [Wired - TikTok length guidance](https://www.wired.com/story/tiktok-wants-longer-videos-like-not)

## 7. 제작 파이프라인

### 1일 1개 숏폼 파이프라인

```text
아이디어 10개 수집
-> 훅 3개 작성
-> 20-35초 대본 작성
-> 캐릭터 기준 이미지 선택
-> 장면별 스틸 3-5장 생성
-> 각 스틸을 2-5초 영상화
-> TTS/녹음 생성
-> 컷 편집
-> 자동 자막
-> SFX/음악
-> 커버 이미지
-> 업로드 후 유지율 확인
```

### 배치 제작 파이프라인

월요일:

- 주제 20개
- 훅 40개
- 대본 10개

화요일:

- 기준 이미지와 장면 스틸 생성
- 썸네일/커버 후보 생성

수요일:

- image-to-video 생성
- 실패 컷 재생성

목요일:

- 음성, 자막, 효과음, 컷 편집

금요일:

- 예약 업로드
- 성과 분석
- 다음 주 반복 소재 선정

## 8. 바로 쓰는 쇼트 레시피

### 직장인 시간 절약 팁

```text
0-1초: "이거 모르면 매일 20분 버립니다"
1-4초: 캐릭터가 지친 표정으로 노트북 앞에 있음
4-10초: Before 화면, 반복 작업 보여주기
10-20초: After 화면, 자동화/템플릿 적용
20-27초: 3단계 요약
27-30초: "저장해두고 내일 해보세요"
```

### 돈 절약 팁

```text
0-1초: "월급에서 새는 돈, 여기서 나갑니다"
1-6초: 흔한 실수 1개
6-15초: 실제 계산 예시
15-24초: 대안 2개
24-30초: 체크리스트 3개
```

### AI 캐릭터 리액션형

```text
0-2초: 캐릭터 표정 리액션
2-7초: 시청자 공감 상황
7-17초: 해결 팁
17-25초: 전/후 비교
25-30초: 저장/댓글 유도
```

## 9. 프롬프트 템플릿

### 캐릭터 기준 이미지

```text
vertical 9:16 realistic portrait of [character identity],
[fixed face traits],
[fixed hairstyle],
[fixed outfit],
standing in [repeatable environment],
clean social media lighting,
medium close-up,
natural expression,
no text, no logo, no distorted hands
```

### 장면 스틸

```text
same character as reference,
[specific emotion],
[specific work/life situation],
[one clear prop],
vertical 9:16,
medium shot,
clean background,
space at top for captions,
realistic lighting,
consistent outfit and hairstyle
```

### image-to-video

```text
[one motion only],
subtle facial expression change,
gentle breathing,
slow camera push-in,
natural micro movement,
keep identity, outfit, lighting, and background unchanged,
no new objects, no scene transition
```

### 립싱크 아바타

```text
frontal talking-head shot,
calm office background,
natural mouth movement,
small head nods only,
stable eye contact,
no large head turn during speech
```

## 10. 품질 체크리스트

이미지:

- 얼굴이 기준 캐릭터와 같은가
- 머리색, 안경, 점, 액세서리가 유지됐는가
- 손과 팔이 이상하지 않은가
- 옷 색이 바뀌지 않았는가
- 배경이 너무 복잡하지 않은가
- 자막 넣을 공간이 있는가

영상:

- 2-5초 안에 하나의 동작만 있는가
- 얼굴이 중간에 바뀌지 않는가
- 입 모양이 음성과 맞는가
- 손/소품이 녹거나 사라지지 않는가
- 컷 전환이 어색하지 않은가
- 플랫폼 UI에 자막이 가리지 않는가

오디오:

- 음성이 너무 빠르지 않은가
- 배경음이 목소리를 덮지 않는가
- 효과음이 과하지 않은가
- 침묵 구간이 의도적으로 남아 있는가

업로드:

- 9:16 세로 마스터인가
- 첫 프레임이 훅으로 작동하는가
- 제목/커버가 주제를 말하는가
- AI/협찬/제휴 표시가 필요한가
- 음악, 폰트, 이미지, 목소리 권리가 확인됐는가

## 11. 정책·저작권·상업 이용 리스크

기본 규칙:

- 실제 사람의 얼굴, 목소리, 이름, 말투를 허락 없이 쓰지 않는다.
- 유명인/인플루언서/직장 상사/동료처럼 오인될 수 있는 인물은 피한다.
- 광고, 협찬, 제휴 링크는 명확하게 표시한다.
- AI 생성 또는 크게 변형된 사실적 영상은 플랫폼의 disclosure 기능을 사용한다.
- AI 이미지 라이선스와 음악/SFX 라이선스는 별개로 확인한다.
- 생성 원본, 프롬프트, 사용 모델, 편집본을 보관한다.

주요 출처:

- [OpenAI Usage Policies](https://openai.com/policies/usage-policies/)
- [OpenAI Terms of Use](https://openai.com/policies/terms-of-use/)
- [Meta - Labeling AI Generated Images](https://about.fb.com/news/2024/02/labeling-ai-generated-images-on-facebook-instagram-and-threads/)
- [Midjourney Terms of Service](https://docs.midjourney.com/hc/en-us/articles/32083055291277-Terms-of-Service)
- [Stability AI Community License Agreement](https://stability.ai/community-license-agreement)
- [FTC Disclosures 101](https://www.ftc.gov/business-guidance/resources/disclosures-101-social-media-influencers)
- [YouTube channel monetization policies](https://support.google.com/youtube/answer/1311392)

YouTube는 반복적이거나 대량생산된 콘텐츠, 일반적인 AI 템플릿으로 만든 콘텐츠가 독창적 해설이나 교육적 가치 없이 반복될 경우 수익화에 문제가 될 수 있다고 안내합니다. 따라서 AI 캐릭터 계정은 템플릿을 쓰더라도 매 영상마다 고유한 관점, 사례, 비교, 해설을 넣어야 합니다.

## 12. 추천 도구 스택

### 빠른 노코드형

- 이미지: Midjourney 또는 OpenAI 이미지
- 영상: Runway / Luma / Pika
- 음성: ElevenLabs 또는 CapCut TTS
- 편집: CapCut / Vrew
- 디자인: MiriCanvas

### 일관성 중시형

- 이미지: FLUX 또는 SDXL
- 정체성: LoRA + IP-Adapter FaceID
- 구도: ControlNet OpenPose/Depth/Canny
- 영상: Runway image-to-video
- 편집: CapCut / Premiere / DaVinci

### 한국어 숏폼 대량 제작형

- 대본: ChatGPT
- 음성/자막/무음 제거: Vrew
- 빠른 편집/템플릿: CapCut
- 썸네일/커버: MiriCanvas
- 이미지/캐릭터: Midjourney 또는 OpenAI
- 영상화: Runway / Luma / Pika

## 13. 출처 신뢰도

높음:

- OpenAI, Midjourney, Hugging Face, Runway, YouTube, FTC, Meta, Stability AI 공식 문서
- Vrew, CapCut, MiriCanvas 공식 제품/도움말 페이지

중간:

- The Verge, Wired, Business Insider, Vogue, Guardian 등 보도
- arXiv 논문. 기술 개념 설명에는 강하지만 개별 플랫폼 운영 팁으로 바로 등치하면 안 됨

낮음:

- 접근이 막힌 커뮤니티/카페/블로그의 검색 스니펫
- 특정 크리에이터의 경험담. 실험 아이디어로는 유용하지만 일반 법칙으로 쓰면 안 됨

## 14. 다음 액션

이 계정 아이디어에 바로 적용하려면 다음 5개를 먼저 만들면 됩니다.

1. 직장인 AI 캐릭터 바이블 1개
2. 기준 이미지 10장
3. 쇼츠 대본 템플릿 5개
4. CapCut/Vrew 자막 스타일 프리셋 1개
5. 30개 소재 스프레드시트

가장 먼저 검증할 포맷은 “직장인이 하루 10분/1만원 아끼는 팁”입니다. 제작 난도가 낮고, 이미지·자막·대본 중심으로도 충분히 전달되며, 캐릭터의 반복 노출로 계정 정체성을 만들기 쉽습니다.
