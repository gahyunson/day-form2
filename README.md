# day_form — 실습 요약 (프레젠테이션 용)

목적: Flutter만 설치된 상태에서 시작하여 `gemini`(자동화 도구)를 활용해 UI 구현, 상태관리, JSON 데이터 처리, 모바일 에뮬레이터에서의 실행(위해 CocoaPods 설치 포함)까지 진행하려 했던 시도와 결과를 요약합니다.

## 시작 상태
- 사전 설치: Flutter만 설치되어 있는 상태
- 목표: MVP 관련 사전 안내를 바탕으로 gemini를 이용해 빠르게 프로토타입을 구성하고, 모바일 에뮬레이터에서 동작 확인

## 시도한 작업
- gemini를 이용해 UI 구현 및 상태관리(ViewModel/Provider) 구조로 리팩터링
- JSON 파일(`assets/questions.json`)을 이용해 데이터 로드/바인딩 구현
- 테스트 자동화/재현을 위해 `test.toml` 파일 생성하고 gemini에서 `/test` 경로로 실행 시도
- 모바일 에뮬레이터에서 실행하기 위해 iOS 의존성 설치(CocoaPods) 시도

## 발생한 문제와 결과
- `test.toml`을 만들어 gemini에서 실행하려 했으나 실행 실패
- iOS 빌드에서 `Module 'flutter_local_notifications' not found` 오류가 발생
	- 원인: iOS 네이티브 라이브러리(플러그인) 연결을 위한 CocoaPods가 설치 또는 실행되지 않음
	- 시도: `flutter clean`, `flutter pub get` 등 패키지 관련 정리 작업 수행
	- 실패: 로컬 환경에서 `pod` 명령이 없어 `pod install`을 실행하지 못함(또는 `sudo gem install cocoapods`에서 설치를 완료하지 못함)

결과적으로 모바일(실기기/시뮬레이터) 환경에서의 최종 실행 검증은 완료하지 못했습니다.

## 원인 분석
- 평소 자주 쓰는 짧은 커맨드만 사용하여 전체 툴체인(예: CocoaPods 설치, 권한/환경 설정 등)을 준비하지 않은 점이 주 원인입니다.
- Flutter 플러그인 중 iOS 네이티브 컴포넌트가 있는 경우에는 `pod install`을 통한 네이티브 의존성 설치가 필수입니다.

## 배운 점
1. gemini 같은 자동화 도구는 프롬프트를 파일로 만들어 실행하면 재현 가능한 작업 파이프라인을 만들 수 있다.
2. 모바일(iOS) 테스트를 위해서는 시스템 레벨 의존성(CocoaPods, Xcode 등)이 사전에 준비되어야 한다.
3. 짧은 커맨드 중심의 작업 방식은 빠르지만, 전체 프로젝트 구조/환경을 처음부터 만들 때는 필요한 초기화 단계(의존성 매니저 설치 등)를 누락하기 쉽다.

## 다음 단계(권장)
1. 로컬에 CocoaPods 설치

```bash
sudo gem install cocoapods
```

2. iOS 의존성 설치 및 빌드 재시도

```bash
cd ios
pod install
cd ..
flutter clean
flutter pub get
flutter run -d <device-id>
```

3. (옵션) Android AVD로 빠르게 테스트하려면 Android Studio에서 AVD를 만들고 아래처럼 실행

```bash
flutter emulators --launch <emulator-id>
flutter run -d <emulator-device-id>
```

4. gemini 자동화 재구성: 실패한 `test.toml`을 점검해 필요한 권한/환경 전제(예: `pod install` 수행 여부)를 추가하고 재실행

## 프레젠테이션 메모(말할 거리)
- 시도한 목표와 기대 결과를 간단히 설명
- 왜 실패했는지(환경 의존성 누락)와 해결 방법을 명확히 제시
- gemini를 통해 얻은 장점(프롬프트 파일화로 재현 가능)과 향후 적용 계획

---

필요하면 제가 `README` 내용에 더 많은 명령 예시나 단계별 스크린샷/결과 로그를 추가해 드리겠습니다.
