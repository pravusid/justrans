# 개인정보 처리방침

[English](./PRIVACY.md)

시행일: 2026-10-25

justrans는 사용자가 요청한 웹페이지·선택 텍스트 번역과 웹페이지·YouTube 자막 요약을 제공하는 브라우저 확장 프로그램이다. 확장 프로그램 운영자는 별도 backend, 광고 또는 analytics 서비스를 운영하지 않으며, 아래 데이터는 사용자의 브라우저와 사용자가 선택한 서비스 사이에서 직접 처리된다.

## 처리하는 데이터

| 데이터                                                                                                  | 처리 목적                                                                                 | 로컬 보관                                                                                                                                                                          | 전송 대상                                                                                                                             |
| ------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| 선택 텍스트, 몰입형 번역 대상·주변 문맥 블록, 페이지 본문, YouTube 자막                                 | 사용자가 요청한 번역·요약 생성                                                            | 번역 원문과 결과는 `browser.storage.session`의 번역 cache에 최대 1시간 보관(아래 "보관과 삭제"). 요약 원문과 결과는 실행 중 메모리에서만 처리                                      | 기능별로 선택한 번역·요약 서비스(아래 "외부 서비스 전송"). Chrome Translator는 기기 안에서 처리. YouTube 자막 조회에는 YouTube도 사용 |
| 사용자가 작성한 번역·요약 프롬프트                                                                      | 번역·요약 요청에 사용자의 지시 반영                                                       | 확장 전용 IndexedDB에 사용자가 바꾸거나 확장 프로그램을 제거할 때까지 보관                                                                                                         | LLM 서비스를 사용하는 번역·요약 요청에 포함                                                                                           |
| LLM 서비스 API key                                                                                      | 모델 조회와 번역·요약 요청 인증                                                           | 기본값은 `browser.storage.session`. 사용자가 지속 보관을 선택하면 확장 프로그램 전용 암호화 key로 암호화해 확장 전용 IndexedDB에 보관. 선택적 비밀번호로 암호화 key를 잠글 수 있음 | key를 저장한 서비스의 endpoint에만 전송                                                                                               |
| 서비스·model·endpoint, 대상 언어, UI·몰입형 번역 설정과 사용자가 입력한 site domain 목록                | 기능 구성과 사용자 설정 복원                                                              | 확장 전용 IndexedDB에 사용자가 바꾸거나 확장 프로그램을 제거할 때까지 보관                                                                                                         | 선택한 model·대상 언어 등 요청에 필요한 값만 해당 서비스에 전송                                                                       |
| 현재 tab URL·title, page origin, YouTube video ID, document ID                                          | 기능 지원 여부와 대상 tab 표시, 같은 origin의 몰입형 번역 session 복원, YouTube 자막 조회 | URL·title·video ID는 실행 중에만 처리하고, 활성 session의 tab ID·origin·document ID는 `browser.storage.session`에 보관                                                             | video ID와 caption 요청만 YouTube에 전송                                                                                              |
| 서비스 요청 시각·횟수·예상 token 수·cooldown, credential 생성·마지막 성공 시각, 보관함 마지막 사용 시각 | 과도한 요청 제한, 장애 복구, key 수명주기 표시, 보관함 자동 잠금                          | 요청 제한 정보와 보관함 마지막 사용 시각은 `browser.storage.session`, credential metadata는 확장 전용 IndexedDB에 보관                                                             | 외부로 전송하지 않음                                                                                                                  |

설정 변경 알림은 변경된 key와 알림 식별자만 `browser.storage.session`에 기록하며, 설정값이나 API key는 알림에 포함하지 않고 외부로 전송하지 않는다.

페이지나 선택 텍스트에는 개인정보나 개인 통신 내용이 포함될 수 있다. justrans는 이를 별도로 분류하거나 프로파일링하지 않지만, 사용자가 기능을 실행하면 해당 내용도 위 표와 같이 처리될 수 있다.

## 외부 서비스 전송

번역은 선택 번역과 몰입형 번역마다, 요약은 요약 기능에 사용자가 고른 서비스로만 전송된다. 사용자가 선택하지 않은 서비스로 대신 보내지 않는다. 선택한 서비스를 현재 브라우저에서 쓸 수 없으면 요청을 보내지 않고 오류를 표시한다.

- **Google Translate(API key 불필요, 기본값):** 번역할 텍스트와 출발·도착 언어가 `https://translate.googleapis.com`으로 전송된다.
- **Chrome Translator(Chrome 전용):** 번역할 텍스트와 페이지가 선언한 언어는 Chrome에 내장된 번역 모델과 언어 감지로 기기 안에서 처리되며 외부로 전송되지 않는다. 번역 모델은 사용자가 설정 화면에서 받을 때 Chrome이 내려받는다.
- **LLM 서비스:** 번역·요약 원문, 주변 문맥, 사용자 프롬프트, model 정보와 API key가 아래 endpoint로 전송된다.

| 서비스                   | endpoint                                    | 수신 데이터 처리방침                                                                                                 |
| ------------------------ | ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Google Gemini            | `https://generativelanguage.googleapis.com` | [Google 개인정보처리방침](https://policies.google.com/privacy)                                                       |
| OpenAI                   | `https://api.openai.com`                    | [OpenAI 개인정보처리방침](https://openai.com/policies/privacy-policy/)                                               |
| Anthropic                | `https://api.anthropic.com`                 | [Anthropic 개인정보처리방침](https://www.anthropic.com/legal/privacy)                                                |
| Groq                     | `https://api.groq.com`                      | [Groq 개인정보처리방침](https://groq.com/privacy-policy)                                                             |
| OpenRouter               | `https://openrouter.ai`                     | [OpenRouter 개인정보처리방침](https://openrouter.ai/privacy). 사용자가 고른 model의 제공자에게 요청을 전달한다       |
| Vercel AI Gateway        | `https://ai-gateway.vercel.sh`              | [Vercel 개인정보처리방침](https://vercel.com/legal/privacy-notice). 사용자가 고른 model의 제공자에게 요청을 전달한다 |
| 사용자 지정(OpenAI 호환) | 사용자가 설정한 base URL                    | 해당 endpoint 운영자의 처리방침                                                                                      |

OpenRouter와 Vercel AI Gateway는 요청을 다른 model 제공자에게 중계하므로, 중계 서비스와 최종 model 제공자의 처리방침이 함께 적용된다.

- **YouTube:** 자막을 읽을 때 video ID와 caption 요청이 현재 YouTube origin 및 YouTube가 제공한 caption URL로 전송된다. 이 요청은 YouTube login cookie를 포함하지 않는다. 자막 요약을 실행할 때만 추출한 자막이 요약에 선택한 LLM 서비스로 전송된다.

### 몰입형 번역의 선번역 범위

몰입형 번역은 스크롤할 때 기다리지 않도록 화면에 아직 보이지 않은 문단도 미리 번역한다. Google Translate를 사용하면 현재 화면 위·아래로 각각 최대 8화면 분량의 원문을 미리 전송한다. LLM 서비스는 스크롤 속도에 따라 화면 위·아래 0.5~1화면 분량, Chrome Translator는 1화면 분량을 미리 번역한다. 선번역은 사용자가 몰입형 번역을 시작한 tab에서만 동작한다.

원격 서비스 통신은 HTTPS만 허용한다. HTTP는 같은 기기의 `localhost`, `127.0.0.1`, `[::1]` endpoint에만 허용한다. 각 서비스가 수신한 데이터의 보관과 처리는 위 표의 처리방침에 따른다. justrans는 서비스의 학습·log·보관 정책을 대신 통제하지 않는다. HTTPS 요청 과정에서 서비스가 IP 주소, 요청 시각, user agent 같은 network 정보를 관찰할 수 있다.

## 보관과 삭제

- 번역 원문과 결과는 같은 내용을 다시 서비스에 보내지 않도록 `browser.storage.session`의 번역 cache에 보관한다. 각 항목은 1시간이 지나거나 cache가 5,000개 또는 약 4 MiB를 넘으면 지워지며, 브라우저를 다시 시작하거나 확장 프로그램을 다시 로드하면 모두 제거된다. Chrome은 이 영역을 디스크에 쓰지 않는다.
- 선번역으로 받은 결과도 사용자가 보지 않았더라도 같은 cache에 남는다. 시크릿(사생활 보호) 창에서 번역한 내용도 창을 닫은 뒤 위 기간 동안 같은 확장 세션에 남는다.
- 요약 원문과 결과는 browser storage에 쓰지 않는다. 번역·요약 원문과 결과를 history, `storage.local`·`storage.sync`·IndexedDB 같은 영구 저장소에 쓰지 않는다.
- session 보관 API key, 잠금 해제한 보관함, 활성 몰입형 번역 session과 요청 제한 정보는 브라우저를 다시 시작하거나 확장 프로그램을 다시 로드하면 제거된다.
- 지속 보관 API key는 설정 화면의 `키 삭제`로 제거할 수 있다. 이 동작은 서비스가 보관한 데이터나 서비스 console의 key를 폐기하지 않는다. 보관함 비밀번호를 잊으면 보관함을 초기화해 지속 보관 key를 모두 지울 수 있다.
- 프롬프트와 일반 설정은 값을 바꾸거나 확장 프로그램을 제거해 삭제할 수 있다.
- 서비스가 이미 받은 데이터의 열람·삭제 요청은 해당 서비스 또는 사용자가 지정한 endpoint 운영자에게 해야 한다.

## 공유와 제한된 사용

justrans는 사용자 데이터를 판매하거나 광고, profiling, data broker, 신용평가 또는 대출 목적으로 사용하지 않는다. 사용자 데이터를 개발자 서버로 보내지 않으며, 위 기능 제공에 필요한 서비스 이외의 당사자와 공유하지 않는다. 운영자는 사용자 원문을 받거나 사람이 열람하는 절차를 운영하지 않는다. 서비스가 수신한 데이터에 접근하는 조건은 각 서비스의 처리방침에 따른다.

The use of information received from Google APIs will adhere to the Chrome Web Store User Data Policy, including the Limited Use requirements.

## 보안

justrans는 사용자가 선택한 일반 웹페이지에서 번역·요약하고 Google Translate·YouTube 및 사용자가 설정한 서비스 endpoint와 통신하기 위해 설치 시 모든 HTTP·HTTPS 사이트에 대한 host 권한(`http://*/*`, `https://*/*`)을 요청한다. Content runtime은 일반 HTTP·HTTPS 페이지에 로드되지만, 페이지 내용 처리와 외부 전송은 사용자가 번역·요약 기능을 실행한 뒤에만 수행한다. 웹페이지에 로드된 content runtime은 확장 프로그램의 설정 저장소와 `browser.storage.session`을 직접 읽지 않는다(Chrome은 session 영역 접근을 확장 프로그램 페이지로 제한하고, Firefox는 content script에 session 영역을 노출하지 않음).

서비스 endpoint는 설정 화면에서 확인하고 API key를 설정된 서비스·origin·base path에 결합해 다른 endpoint로의 전송을 거부한다. 지속 보관 암호화는 비밀번호를 설정하지 않으면 로컬 브라우저 profile 탈취를 막지 못하고, 비밀번호를 설정해도 잠금 해제 중에는 같은 확장 프로그램 권한이나 DevTools 접근을 막지 못한다. 보관함은 설정한 시간(기본 30분) 동안 쓰지 않으면 자동으로 잠긴다.

## Firefox의 데이터 수집 고지와 API 인증정보 권한

Firefox build는 설치 시 웹사이트 콘텐츠 처리(`websiteContent`)를 필수 데이터 수집으로, 인증정보 전송(`authenticationInfo`)을 선택 데이터 수집으로 고지한다. API key 입력·저장은 서비스에 요청을 보내지 않는다. API key는 설정된 서비스의 모델 조회·번역·요약 요청 인증에 사용된다. Firefox에서는 기존 API 접근 허용 버튼으로 브라우저 내장 인증정보 전송 권한을 요청한다. 권한을 거절하거나 Firefox 설정에서 철회하면 API key를 사용하는 후속 요청을 차단한다. 키 없는 요청에는 인증정보 권한을 요구하지 않는다.

## 변경과 문의

처리 데이터, 전송 대상 또는 보관 방식이 바뀌면 이 문서와 [영문판](./PRIVACY.md)의 시행일, Chrome Web Store·Firefox Add-ons 고지를 기능 배포 전에 갱신한다. 문의나 삭제 지원 요청은 [프로젝트 issue tracker](https://github.com/pravusid/justrans/issues)에 남길 수 있다.
