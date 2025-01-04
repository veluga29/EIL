---
title: 
tags: 
date: 
thumbnail:
---

## 동시성이슈 해결방법
- 멀티스레드 작업을 하다보면, 공유 자원에 대한 Race Condition으로 인해 **동시성 이슈가 발생**한다
- 이에 대한 다양한 해결방법을 정리해보자
	- 데이터에 1개의 스레드만 접근 가능하도록 하기

## Synchronized (거의 사용 X)
- 데이터에 1개의 스레드만 접근 가능하도록 하기 
- 문제점
	- **여러 프로세스 동작** 시, **여전히 Race Condition 발생**
		- `synchronized`는 하나의 프로세스 안에서만 1개의 스레드 접근 보장
		- **다른 프로세스의 스레드가 접근**하면, 여전히 여러 스레드가 접근 가능해짐
			- 서버가 1대일 때는 괜찮지만, **2~3대**부터는 데이터 접근을 여러 곳에서 할 수 있음
		- **실제 운영 중인 서비스**는 **대부분 2대 이상의 서버**를 사용 -> **`synchronized`는 거의 사용 X**
	- 추가로, **`@Transactional`** 사용 시 **`synchronized` 적용이 어려움**
		- `@Transactional`은 스프링 AOP 사용으로 **트랜잭션 프록시 객체**를 생성
			- 내부 동작
				- `startTransaction();`
				- **`stockService.decrease(id, quantity);`** (`target` 객체 호출)
				- `endTransaction();`
		- **실제 DB 업데이트(`endTransaction()`) 전**에 **다른 스레드가 `decrease()` 메서드 호출**할 수 있음
		- 이렇게 되면, 다른 스레드는 갱신되기 전 값을 가져가 여전히 동시성 문제 발생
		- 즉, 서비스 객체 메서드가 아닌, AOP 객체 메서드에 `synchronized`를 걸어야 하는데 **어려움**

