<img width="256" height="256" alt="image" src="https://github.com/user-attachments/assets/8690d688-f4e3-4c18-9e88-18373e19b516" />

# FaceInn

<aside>

### 앱 이름

- FaceInn(페이스 인)

---

### 앱 소개

- 얼굴 인식을 통한 비대면 호텔 체크인/체크아웃 앱

---

### 앱 목적

- 얼굴 인식을 통한 비대면 서비스를 제공함으로써 얼굴 대면의 어려움이나 해외에서의 언어 문제를 해소

---

### 개발기간
2025.05~2025.06 MVP 구현

---

### 개발인원
- 2명

<table>
  <tr>
    <td align="center">
      <img src="https://avatars.githubusercontent.com/ChanSolShin" width="100"/><br/>
      <b>ChanSolShin</b>
    </td>
    <td align="center">
      <img src="https://avatars.githubusercontent.com/HunCY5" width="100"/><br/>
      <b>HunCY5</b>
    </td>
  </tr>
</table>

---

### 사용기술

- **Language:** Swift
- **Framework:** UIKit
- **Architecture:** MVC
- **Backend:** Firebase (Firestore, Authentication)
- **Asynchronous:**  Firebase Firestore 클로저 기반 호출, GCD(DispatchQueue), DispatchGroup, Swift Concurrency(async/await)
- **Version Control:** Git & GitHub
- **Collaboration**: Notion
- **CNN Model:** MobileFaceNet

---

### 주요 기능

1. 얼굴 등록
    - 얼굴 정면, 측면을 촬영하여 얼굴 데이터 저장
    - MobileFaceNet 딥러닝 모델: 얼굴 사진을 데이터 값으로 변환
2. 숙소 예약
    - 등록한 얼굴 데이터 사용유무 선택하여 예약 진행
        - 얼굴 데이터 사용 시: 체크인/체크아웃 진행 때 얼굴 인식을 통해 진행 가능
        - 얼굴 데이터 사용 X: 비대면 서비스 이용 불가능
3. 체크인/체크아웃
    - 얼굴 데이터 사용을 선택 후 예약 진행했을 경우 비대면 서비스 이용 가능
    - 호텔 로비에 배치된 태블릿에 얼굴 등록과 동일한 방식으로 얼굴 인식 진행하여 체크인/체크아웃 진행
4. 객실/예약 관리
    - 호스트 측에서 등록되어 있는 객실/예약 관리할 수 있는 기능

---

## 배운점 및 성과

- Tuist  프로젝트 관리
- 비동기 처리 성능 개선
- 얼굴정보 테스트 계획 수립
- Kingfisher 라이브러리를 적용해 **비동기 이미지 처리**와 자동 캐싱 구조를 학습

---

<img width="796" height="484" alt="image" src="https://github.com/user-attachments/assets/e6c643d2-7abf-46c6-848d-9af78d3c2c56" />

<p align="center">
  <img src="https://github.com/user-attachments/assets/61a98ca2-e39e-42d9-bc18-e2387e1c0f76" width="200"/>
  <img src="https://github.com/user-attachments/assets/c6169eb1-3278-427b-96d1-fc7d4e3fc43d" width="200"/>
</p>

