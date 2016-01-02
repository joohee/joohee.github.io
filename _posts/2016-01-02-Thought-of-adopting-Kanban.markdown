---
layout: post
title:  "Thought of adopting Kanban"
date:   2016-01-02 18:12:00
categories: kanban
---

### Kanban 도입을 고민하다. 

1. Kanban 이란? 
	- 효율적인 소프트웨어 개발을 위해 나온 방법론이다. 
	- 원래 Toyota의 Just-In-Time(JIT)에 기원을 두고 있다. 
	- 요구사항 분석 -> 개발 (코딩) -> 테스트 및 배포 순서를 통해 업무를 파악할 수 있다. 

2. Kanban의 기대효과
	- 병목(bottleneck)을 줄일 수 있다. 기획/개발/테스트 등의 단계에서 처리 가능한 총량을 분석하고 그에 맞게 다른 사람들에게 병목이 되는 업무에 대해 분배 및 처리하도록 하면 훨씬 효율적으로 일을 수행할 수 있다. 
	- 칸반 보드를 활용하여, 진행 절차 및 해당 업무가 무엇이 있는지 한 눈에 확인할 수 있다. 
	- pending(대기), analysis(분석) 진행 중/완료, development(개발) 진행 중 / 완료, Test(테스트), Deploy(배포) 의 절차를 놓고 해당 단계의 업무를 배열한다. 
![http://kanbanblog.com/explained/image/kanban-board-1.png](http://kanbanblog.com/explained/image/kanban-board-1.png)

	- 물론 보드의 레이아웃을 배열하는 것은 여러가지 방법이 존재한다. 단지 세부 단계로 나누는 것은 진행 중/완료 인 것이다. 
	- 예를 들어 테스터가 어떤 feature에 대해 테스트를 완료하면 Test 컬럼의 slot이 하나 비게 되는 것이다. 그리고 Test 의 빈 slot에는 Development의 완료에 있는 업무 중에 가져오면 되는 것이다. 
![http://kanbanblog.com/explained/image/kanban-board-3.png](http://kanbanblog.com/explained/image/kanban-board-3.png)

3. Kanban을 어떻게 시작할 것인가? 
1) 개발 절차를 나열해보자. 
	- feature idea는 어디에서 오는가?
	- idea가 end-user까지 전달되기 위한 단계들은 무엇인가?
	
2) Kanban 시스템을 위한 시작점/끝점을 정의해보자.
	- 이 부분은 정책 또한 고려되어야 한다. 
	- 범위 밖에 있는 사람들이 참여하기 위해 곧 질문할 것과 같은, 너무 작은 부분을 가지고 시작하는 것에 대해선 걱정하지 말아라.
	
3) 동의를 구해야 한다. 
	- 초반의 WIP(Work In Process) 한계와 변경 사항 및 임시로 깨잘 수도 있는 정책.
	- 우선순위와 feature를 선택하기 위한 프로세스
	- 분류가 다른 서비스들에 대한 정책 (e.g. "표준", "촉박", "배포일정 fix"). 견적을 뽑아야 하는가? 작업을 선택할 때 무엇을 맨 처음으로 선택할 것인가?
	- 리뷰 주기
	
4) Kanban 보드에 그려라.
	- 필요한 것은 화이트보드와 포스트잇 이다. 예쁘게 만들려고 노력하지 말아라. 분명히 이것들은 진화할 것이다. 
	
5) 이를 이용하여 일을 시작하라. 
6) 경험하면서 조절하라. 

reference: http://kanbanblog.com/explained/


