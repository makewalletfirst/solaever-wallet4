# SolaEver Wallet (SLE)

SolaEver 블록체인 전용 안드로이드 지갑 어플리케이션입니다. 

## 🚀 주요 기능
- **지갑 생성 및 복구**: 12단어 니모닉(Mnemonic)을 통한 지갑 생성 및 복구 지원.
- **보안 저장**: `Expo SecureStore`를 사용하여 기기 내에 암호화된 상태로 키(Mnemonic)를 안전하게 저장.
- **실시간 잔고 조회**: SolaEver RPC (`https://rpc-sola.ever-chain.xyz`) 연동을 통한 SLE 잔액 확인.
- **SLE 전송**: 솔라나 표준 트랜잭션을 기반으로 한 자산 전송 기능.
- **네트워크 최적화**: 안드로이드의 까다로운 보안 정책 대응 및 커스텀 폴리필(Shim)을 통한 통신 안정화.

## 🛠 기술 스택
- **Framework**: React Native (Expo SDK 51+)
- **Language**: TypeScript
- **Blockchain SDK**: `@solana/web3.js`, `@solana/spl-token`
- **Crypto & Polyfills**: `bip39`, `ed25519-hd-key`, `buffer`, `react-native-get-random-values`, `react-native-crypto`, `text-encoding`
- **Navigation**: React Navigation (Stack)
- **Security**: `expo-secure-store`

## 🏗 구현 및 트러블슈팅 과정
1. **환경 설정 (Polyfills)**: Node.js API가 누락된 React Native 환경에서 Solana SDK를 구동하기 위해 `Buffer`, `Crypto`, `Process` 등을 전역에 주입하는 `src/lib/shim.js` 구축 및 `metro.config.js` 별칭(Alias) 설정.
2. **지갑 로직**: `bip39`를 이용한 12단어 니모닉 생성 및 Solana 표준 유도 경로(`m/44'/501'/0'/0'`) 기반의 키페어 추출 로직 구현.
3. **네트워크 보안 대응**: 안드로이드 9(Pie) 이상에서 발생하는 `Network request failed` 에러를 해결하기 위해 `network_security_config.xml`을 도입하고 모든 트래픽(Cleartext)을 허용하도록 매니페스트 수정.
4. **통신 안정화**: 모바일 환경에서 발생할 수 있는 `web3.js` 통신 타임아웃을 대비하여 `fetch`를 이용한 직접 JSON-RPC 잔고 조회 로직(`getBalance`)을 병행 적용.
5. **빌드 최적화**: 서버 리소스(2코어 4GB -> 4코어 12GB 업그레이드 과정 반영)를 고려하여 Gradle 힙 메모리 및 CPU 코어 사용량을 최적화하여 빌드 성공.

## 📦 빌드 방법 (Local Build)

### 1. 의존성 설치
```bash
npm install
```

### 2. 안드로이드 빌드 준비 (Prebuild)
```bash
npx expo prebuild --platform android
```

### 3. APK 빌드
서버 리소스가 제한적인 환경에서는 아래와 같이 자원을 제한하여 빌드하는 것이 권장됩니다.

```bash
cd android
# 환경에 맞는 ANDROID_HOME 설정 필요
export ANDROID_HOME=/your/android/sdk/path
./gradlew assembleDebug --no-daemon --max-workers=2
```

빌드 완료 후 APK 위치: `android/app/build/outputs/apk/debug/app-debug.apk`

---
Developed for **SolaEver Network**.
