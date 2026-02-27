# lessOnRing - C4 Architecture Documentation

## 1. Overview

lessOnRing은 학원 운영, 강사 매칭, 방문 수업 배차 및 셔틀 경로 관리를 통합 제공하는 SaaS 플랫폼입니다.

본 문서는 C4 모델(Context, Container, Component, Code)을 기반으로 시스템 아키텍처를 단계적으로 설명합니다.

---

# 2. C4 Level 1 - System Context

## 목적
lessOnRing이 외부 사용자 및 외부 시스템과 어떻게 상호작용하는지 설명합니다.

## 사용자

- **Member**: 모바일 앱을 통해 수업 예약 및 방문 수업 이용
- **Instructor**: 배차된 수업 수행
- **Academy Admin**: 학원 운영 및 관리

## 외부 시스템

- **Kakao Login API**: OAuth 기반 인증
- **Kakao Mobility Directions API**: 실제 도로 경로 계산
- **Kakao Map SDK**: 모바일 지도 렌더링

## 시스템 정의

**lessOnRing Platform**
- 학원 운영 관리
- 강사 매칭
- 방문 수업 배차
- 셔틀 경로 관리
- 통합 SaaS 서비스

---

# 3. C4 Level 2 - Container Diagram

## 목적
플랫폼을 구성하는 주요 실행 단위(컨테이너)를 설명합니다.

## Client Layer

### Mobile App
- React Native / Flutter
- Kakao Login 연동
- 지도 기반 경로 표시
- 수업 예약 및 배차 확인

### Admin Web
- Next.js
- 회원 관리
- 수강권 관리
- 배차 대시보드

---

## Application Layer

### Spring Boot API
- JWT 인증
- 예약 서비스
- 배차 서비스
- 경로 생성 서비스
- 이벤트 발행

### Kafka
- lesson-events
- dispatch-events
- notification-events (확장 예정)

---

## Data Layer

### PostgreSQL
- users
- reservations
- passes
- dispatch_job
- dispatch_route

### Redis
- 예약 동시성 제어 (Lock)
- 배차 충돌 방지
- 캐시 저장

---

## External Integration

- Kakao Login API
- Kakao Mobility API
- Kakao Map SDK

---

# 4. C4 Level 3 - Component Diagram (API 내부 구조)

## 목적
Spring Boot API 내부 모듈 구조를 설명합니다.

### Auth Module
- Kakao OAuth 인증
- JWT 발급 및 검증

### Member Module
- 회원 정보 관리
- 수강권 관리

### Reservation Module
- 수업 정원 체크
- 수강권 차감
- 트랜잭션 처리

### Dispatch Module
- 방문 수업 배차 생성
- 강사 할당
- 상태 전이 관리

### Route Module
- Kakao Directions API 호출
- Polyline 저장
- 거리 및 소요시간 계산

### Event Publisher
- Kafka 이벤트 발행

### Event Consumer
- 비동기 배차/경로 처리

---

# 5. C4 Level 4 - Code Level (선택)

코드 레벨에서는 다음과 같은 클래스 단위 구조를 가집니다.

- ReservationService
- DispatchService
- RouteService
- RedisLockService
- AuthController
- DispatchController

---

# 6. Architecture Principles

- 모놀리식 API 기반 설계 (확장 가능 구조)
- 이벤트 기반 처리 (Kafka)
- 동시성 제어를 위한 Redis 분산락
- 실제 도로 경로 기반 배차 시스템
- SaaS 확장을 고려한 모듈 분리 설계

---

# 7. Future Scalability

- Kubernetes 기반 컨테이너 오케스트레이션
- Dispatch Service 분리 (Microservice 전환 가능)
- 알림 서비스 독립 분리
- Analytics 서비스 추가

---

# 8. Summary

lessOnRing은 운영 부담을 줄이고(Less On),
연결 가치를 강화하는(Ring) 플랫폼으로 설계되었습니다.

C4 모델 기반 설계를 통해 확장성과 유지보수성을 고려한 구조를 갖추고 있습니다.