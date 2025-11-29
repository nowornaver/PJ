# PJ — 개인 프로젝트 모음

이 저장소는 개인으로 진행한 여러 임베디드/컴퓨터비전/제어 관련 프로젝트들을 브랜치별로 관리하고 있습니다. 포트폴리오 링크로 사용하기 좋게 각 프로젝트에 대한 요약과 실행법을 정리해두었습니다.

🔗 프로젝트 브랜치 (각 항목을 클릭하면 해당 브랜치로 이동합니다)
- LaneDetection — 차선 인식(Computer Vision)  
  https://github.com/nowornaver/PJ/tree/LaneDetection
- MotorController — 모터 제어 관련 프로젝트  
  https://github.com/nowornaver/PJ/tree/MotorController
- TC275-Multicore-with-CANoe — TC275 멀티코어 + CANoe 통합 실습/검증  
  https://github.com/nowornaver/PJ/tree/TC275-Multicore-with-CANoe

요약
- 목적: 각 프로젝트는 임베디드 제어/차량 관련 SW 개발 역량을 보여주기 위한 실습 및 포트폴리오용 샘플입니다.
- 핵심 기술: C/C++, Python, OpenCV, CAN 통신, AUTOSAR/MCU(프로젝트별 상이)

프로젝트별 상세 안내(브랜치별 README 권장)
- 각 브랜치 폴더에 README.md를 두고 다음을 담는 것을 권장합니다:
  - 프로젝트 한 줄 소개
  - 핵심 기능 / 역할
  - 사용 기술 스택 (언어, 라이브러리, 툴)
  - 빌드/플래시/실행 방법 (예: 도구 버전, 컴파일 명령, 시뮬레이션 절차)
  - 주요 파일/아키텍처 개요 (예: src 구조, 인터페이스 설명)
  - 테스트 방법 및 결과 (짧은 예시 로그, 캡처 이미지)
  - 라이선스 및 연락처

빠른 시작(예시 템플릿)
1. 저장소 클론
   git clone https://github.com/nowornaver/PJ.git
2. 브랜치 선택
   git checkout LaneDetection
3. (프로젝트별) README의 설치/실행 절차를 따르세요.

추천 저장소 Description (GitHub 상단 한 줄)
- "임베디드/제어/컴퓨터비전 관련 개인 프로젝트 모음 — Lane Detection, Motor Control, TC275 + CANoe 실습"

추천 Topics
- embedded, motor-control, computer-vision, opencv, can, autosar, portfolio

문의 및 다음 단계
- 원하시면 각 브랜치 내부를 제가 순회하면서(코드/파일 확인) 브랜치별 README를 자동 생성해드릴 수 있습니다. 그렇게 진행해도 될까요?
- 또는 지금 드린 루트 README를 저장소에 추가/교체해 드릴 수 있습니다. 저장소에 적용 방식 선택을 알려주세요:
  1) 새 브랜치(e.g. enhance/readme) 생성 → README 추가 → PR 생성(권장)  
  2) 바로 main에 README 커밋(직접 반영)

어떤 방식으로 진행할까요? 또한 각 브랜치 중 우선적으로 상세 README가 필요하신 브랜치가 있으면 알려주세요.
