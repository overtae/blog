---
title: "스케줄러"
date: 2023-01-20
categories:
  - Operating System
tags:
  - Computer Science
  - Scheduler
  - 스케줄러
excerpt: "스케줄러에 대해 알아보자"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## 스케줄러, Scheduler

스케줄러는 한정적인 메모리를 여러 프로세스가 효율적으로 사용할 수 있도록 **어떤 프로세스에게 메모리를 할당할 지 선택하는 역할**을 담당한다.

## 스케줄링 큐, Scheduling Queues

프로세스를 스케줄링하기 위한 큐에는 아래 세 가지가 있다.

▪️ **Job(batch) Queue** : 현재 시스템 내에 있는 모든 프로세스의 집합

▪️ **Ready Queue** : 현재 메모리 내에 있는 Ready 상태 프로세스의 집합

▪️ **Device Queue** : Device I/O 작업을 대기하고 있는 프로세스의 집합

다시 말해 스케줄러는 각각의 큐에 프로세스들을 넣고 빼주는 역할을 한다.

💡 **스케줄링?** 어떤 시점에 어떤 프로세스에게 자원을 할당할 지 결정하는 작업
{: .notice--success}

## 스케줄러 종류

스케줄러는 역할과 목적에 따라 단기, 중기, 장기 스케줄러로 구분된다.

### 장기 스케줄러, Long-term scheduler / 잡 스케줄러, Job scheduler

---

한정된 메모리에 많은 프로세스들이 한 번에 올라올 경우 (디스크와 같은) 대용량 메모리에 저장되는데, 이 중 어떤 프로세스에 메모리를 할당하여 Job Queue에서 Ready Queue로 보낼지 결정한다. 스케줄링이 발생하는 시간이 비교적 오래걸리기 때문에 장기 스케줄러라고 한다. 

▪️ 디스크와 메모리 사이의 스케줄링을 담당

▪️ 디스크 내의 작업을 어떤 순서로 메모리에 적재할지 결정

▪️ new(디스크) → ready(메모리) 상태 전이

▪️ degree of Multiprogramming (실행중인 프로세스 수) 제어

▪️ 연산 비중이 높은 프로세스와 입출력 비중이 높은 프로세스의 균형을 맞춤

### 단기 스케줄러, Short-term scheduler / CPU 스케줄러, CPU scheduler

---

스케줄링이 발생하는 시간이 매우 짧아 단기 스케줄러라고 하며 CPU가 점유하는 순서를 정해준다.

▪️ 메모리와 CPU 사이의 스케줄링을 담당

▪️ Ready Queue에 존재하는 프로세스 중 어떤 프로세스를 running 시킬지 결정

— 이때, 실제 프로세스에 자원을 할당하는 것은 Dispatcher가 수행한다.

▪️ ready → running 상태 전이

### 중기 스케줄러, Mid-term scheduler / 스와퍼, Swapper

---

장기 스케줄러보다 자주, 단기 스케줄러보다 덜 발생하는 스케줄러이다. 주기적으로 메인 메모리의 전체 프로세스를 검사하여 보조기억장치로 옮길 프로세스를 찾아 옮긴다.

▪️ 메모리에 너무 많은 프로그램이 동시에 올라가는 것을 조절

▪️ 메모리 여유 공간을 위해 프로세스를 메모리에서 디스크로 쫓아냄 (Swapping)

▪️ ready → suspended 상태 전이

▪️ 프로세스에게서 메모리 할당 해제

💡 **Swapping?** 프로세스를 메모리에서 디스크로 내리고(swap out) 디스크에서 메모리로 올리는(swap in) 기법
{: .notice--success}