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
## 아키텍처

FaceInn은 **UIKit 기반의 기능 중심 MVC 아키텍처**로 구성되어 있습니다.

화면 UI는 대부분 `UIViewController`와 커스텀 `UIView`, `UITableViewCell`을 중심으로 구성되어 있으며, 로그인/회원가입/마이페이지처럼 입력과 상태 처리가 필요한 화면은 `View`, `ViewController`, `Model`을 분리해 UI 구성과 Firebase 요청 로직이 한 파일에 과도하게 섞이지 않도록 설계했습니다.

앱의 주요 데이터 저장과 인증은 Firebase 기반으로 처리합니다. `FirebaseAuth`는 로그인/회원가입 및 사용자 세션 확인에 사용되고, `Firestore`는 사용자, 숙소, 객실, 예약, 얼굴 인증 관련 데이터를 저장하는 역할을 담당합니다. 이미지 리소스는 `FirebaseStorage`와 `Kingfisher`를 활용해 업로드 및 로딩 흐름을 구성했습니다.

FaceInn의 핵심 기능인 얼굴 등록 및 체크인 인증은 `FaceId`와 `HostMainView/CheckIn` 영역으로 분리되어 있습니다. 얼굴 촬영 화면은 `ARKit` 기반 카메라 흐름을 사용하고, `Vision`과 `CoreML`을 통해 얼굴 검출 및 MobileFaceNet 임베딩 벡터 추출을 수행합니다. 추출된 얼굴 벡터는 Firestore에 저장되며, 체크인 시 저장된 벡터와 현재 촬영된 얼굴 벡터를 비교해 게스트 인증 흐름을 처리합니다.

프로젝트 생성과 의존성 관리는 Tuist를 통해 관리합니다. `Project.swift`에서 앱 타겟, 테스트 타겟, Firebase, FSCalendar, Kingfisher, ARKit, SceneKit 의존성을 선언하고, `tuist generate`를 통해 Xcode 프로젝트를 생성하는 구조입니다.

```text
FaceInn/
├── Configs
│   └── Signing.xcconfig                         # Debug/Release 공통 서명 설정
│
├── Tuist
│   └── Package.swift                            # Tuist 패키지 설정 파일
│
├── Derived
│   ├── InfoPlists
│   │   ├── FaceInn-Info.plist                   # Tuist가 생성한 앱 Info.plist
│   │   └── FaceInnTests-Info.plist              # Tuist가 생성한 테스트 Info.plist
│   └── Sources
│       ├── TuistAssets+FaceInn.swift            # Tuist 리소스 접근 코드
│       ├── TuistBundle+FaceInn.swift            # Tuist 번들 접근 코드
│       └── TuistPlists+FaceInn.swift            # Tuist plist 접근 코드
│
├── FaceInn
│   ├── Sources
│   │   ├── AppDelegate.swift                    # 앱 실행 진입점, Firebase 초기화, 전역 UI 설정
│   │   ├── SceneDelegate.swift                  # 로그인 상태와 사용자 타입에 따른 초기 화면 라우팅
│   │   ├── MobileFaceNet.mlmodel                # 얼굴 임베딩 추출용 CoreML 모델
│   │   │
│   │   ├── Auth
│   │   │   ├── Login
│   │   │   │   ├── LoginView.swift              # 게스트 로그인 UI 구성
│   │   │   │   ├── LoginViewController.swift    # 게스트 로그인 입력 검증 및 화면 전환 처리
│   │   │   │   ├── LoginModel.swift             # FirebaseAuth 로그인 및 Firestore 사용자 타입 확인
│   │   │   │   ├── HostLoginView.swift          # 호스트 로그인 UI 구성
│   │   │   │   └── HostLoginViewController.swift# 호스트 로그인 흐름 처리
│   │   │   │
│   │   │   └── SignUp
│   │   │       ├── SignUpView.swift             # 게스트 회원가입 UI 구성
│   │   │       ├── SignUpViewController.swift   # 게스트 회원가입 입력 검증 및 화면 처리
│   │   │       ├── SignUpModel.swift            # 게스트 회원가입 Firebase 요청 처리
│   │   │       ├── HostSignUpView.swift         # 호스트 회원가입 UI 구성
│   │   │       ├── HostSignUpViewController.swift# 호스트 회원가입 및 사업자 정보 검증 처리
│   │   │       └── HostSignUpModel.swift        # 호스트 회원가입 데이터 모델
│   │   │
│   │   ├── FaceId
│   │   │   ├── FaceCaptureViewController.swift  # ARKit 기반 얼굴 등록 촬영, 정면/측면 캡처 흐름 제어
│   │   │   ├── FaceGuideOverlayView.swift       # 얼굴 촬영 가이드 오버레이 UI
│   │   │   ├── FaceProcessor.swift              # Vision/CoreML 기반 얼굴 검출 및 임베딩 벡터 추출
│   │   │   └── UIImage+PixelBuffer.swift        # UIImage를 CoreML 입력용 CVPixelBuffer로 변환
│   │   │
│   │   ├── HostMainView
│   │   │   ├── ManageReservationViewController.swift # 호스트 예약 관리 화면
│   │   │   ├── ManageRoomViewController.swift        # 호스트 객실 관리 화면
│   │   │   │
│   │   │   └── CheckIn
│   │   │       ├── CheckInViewController.swift        # 호스트 체크인 메인 화면 및 통계 표시
│   │   │       ├── CheckInModel.swift                 # 체크인 관련 데이터 모델
│   │   │       ├── GuestCameraViewController.swift    # 게스트 체크인용 카메라 진입 화면
│   │   │       ├── GuestFaceRecognitionViewController.swift # 얼굴 인식 기반 게스트 체크인 인증 처리
│   │   │       ├── ReserveInfoViewController.swift    # 예약 상세 정보 확인 화면
│   │   │       └── RecentRecognitionCell.swift        # 최근 얼굴 인식 내역 셀
│   │   │
│   │   ├── ViewController
│   │   │   ├── HomeViewController.swift               # 게스트 홈 화면, 숙소 목록 조회
│   │   │   ├── AccommodationDetailViewController .swift# 숙소 상세 화면, 객실/예약 진입 처리
│   │   │   ├── AccommodationRegisterViewController.swift# 호스트 숙소 등록 화면
│   │   │   ├── EditAccommodationViewController.swift  # 숙소 정보 수정 화면
│   │   │   ├── RoomRegisterViewController.swift       # 객실 등록 화면
│   │   │   ├── EditRoomViewController.swift           # 객실 정보 수정 화면
│   │   │   ├── ReservationViewController.swift        # 예약 생성 및 결제 전 예약 정보 저장
│   │   │   ├── TripsViewController.swift              # 게스트 여행/예약 내역 화면
│   │   │   ├── WishlistViewController.swift           # 게스트 찜 목록 화면
│   │   │   ├── MessageViewController.swift            # 메시지 화면
│   │   │   ├── HostProfileViewControlle.swift         # 호스트 프로필 화면
│   │   │   │
│   │   │   └── ButtonVC
│   │   │       ├── DatePickerPopoverViewController.swift # FSCalendar 기반 날짜 선택 팝오버
│   │   │       ├── GuestSelectorViewController.swift     # 게스트 수 선택 팝오버
│   │   │       └── LocationSelectorViewController.swift  # 지역 선택 팝오버
│   │   │
│   │   ├── View
│   │   │   ├── AccommodationCardView.swift        # 숙소 카드 UI, 찜 상태 및 이미지 표시
│   │   │   ├── AccommodationRegisterView.swift    # 숙소 등록 입력 UI
│   │   │   ├── RoomCardView.swift                 # 객실 카드 UI
│   │   │   └── RoomRegisterView.swift             # 객실 등록 입력 UI
│   │   │
│   │   ├── Cell
│   │   │   ├── AccommodationCell.swift            # 숙소 목록 셀
│   │   │   ├── ImageGalleryCell.swift             # 숙소/객실 이미지 갤러리 셀
│   │   │   ├── ReservationCell.swift              # 예약 목록 셀 및 얼굴 등록 진입 처리
│   │   │   └── RoomTableViewCell.swift            # 객실 목록 셀
│   │   │
│   │   ├── Model
│   │   │   ├── Accommodation.swift                # 숙소 도메인 모델
│   │   │   ├── AccommodationRoom.swift            # 객실 도메인 모델
│   │   │   └── Reservation.swift                  # 예약 도메인 모델
│   │   │
│   │   ├── MyPage
│   │   │   ├── ProfileView.swift                  # 게스트 마이페이지 UI
│   │   │   ├── ProfileViewController.swift        # 게스트 프로필 조회 및 로그아웃 처리
│   │   │   ├── ProfileModel.swift                 # 프로필 Firebase 데이터 요청 처리
│   │   │   ├── HostPageView.swift                 # 호스트 마이페이지 UI
│   │   │   └── HostPageViewController.swift       # 호스트 프로필 조회 및 로그아웃 처리
│   │   │
│   │   └── TabBar
│   │       ├── MainTabBarController.swift         # 게스트용 탭바, 홈/찜/여행/프로필 구성
│   │       └── HostMainTabBarController.swift     # 호스트용 탭바, 예약관리/페이스인/객실관리/마이페이지 구성
│   │
│   ├── Resources
│   │   ├── Assets.xcassets                        # 앱 로고, 앱 아이콘, 얼굴 가이드 이미지 리소스
│   │   ├── GoogleService-Info.plist               # Firebase 앱 설정 파일
│   │   └── Preview Content                        # 미리보기용 리소스
│   │
│   └── Tests
│       └── FaceInnTests.swift                     # FaceInn 단위 테스트 타겟
│
├── FaceInn.xcodeproj                              # Tuist로 생성된 Xcode 프로젝트
├── FaceInn.xcworkspace                            # Tuist로 생성된 Xcode 워크스페이스
├── Project.swift                                  # Tuist 프로젝트/타겟/의존성 정의
├── Tuist.swift                                    # Tuist 전역 설정
├── Dependenciese.swift                            # 프로젝트 의존성 관련 파일
├── GoogleService-Info.plist                       # Firebase 설정 파일
├── .package.resolved                              # Swift Package 의존성 resolved 파일
├── .gitignore                                     # Git 제외 파일 설정
└── README.md                                      # 프로젝트 소개 및 실행 방법 문서
```
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

