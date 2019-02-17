# 친절한 SQL 튜닝 (2019/2/17 ~)

- [1장 SQL 파싱과 최적화](#Part1)
  - 1.1 SQL 파싱과 최적화
  - 1.2 SQL 공유 및 재사용
  - 1.3 데이터 저장 구조 및 I/O 메커니즘
- [2장 인덱스 기본](#Part2)
  - 2.1 인덱스 구조 및 탐색
  - 2.2 인덱스 기본 사용법
  - 2.3 인덱스 확장
- [3장 인덱스 튜닝](#Part3)
- [4장 조인 튜닝](#Part4)
- [5장 소트 튜닝](#Part5)

## Part1
### 1장 SQL 파싱과 최적화
1.1 SQL 파싱과 최적화
- 구조적, 집합적, 선언적 질의 언어
  - SQL(Structured Query Language)
  ~~~
  에제) 부서번호(deptno)로 조인해서 사원명 순으로 정렬
  SELECT    *
  FROM      EMP E,
            DEPT D
  WHERE     E.DEPTNO = D.DEPTNO
  ORDER BY  ENAME
  ~~~
  - SQL 최적화 : DBMS 내부에서 프로시저를 작성하고 컴파일해서 실행 가능한 상태로 만드는 전 과정
- SQL 최적화 과정
  1. SQL 파싱 : SQL을 전달받으면, 가장 먼저 SQL 파서가 파싱을 진행
      - 파싱 트리 생성 : SQL 문을 이루는 개별 구성요소를 분석해서 파싱 트리 생성
      - Syntax 체크 : 문법적 오류 체크 ex) 누락된 키워드 및 사용할 수 없는 키워드
      - Semantic 체크 : 의미상 오류가 없는지 체크 ex) 존재하지 않는 테이블, 컬럼, 권한
  2. SQL 최적화 : SQL 옵티마이저는 미리 수집한 시스템 및 오브젝트 통계정보를 바탕으로 다양한 실행경로를 생성해서 비교한 후, 가장 효율적인 하나를 선택
  --> 데이터 베이스 성능을 결정하는 가장 핵심적인 엔진 (옵티마이저가 이 역할을 맡음)
  3. 로우 소스 생성 : SQL 옵티마이저가 선택한 실행경로를 실제 실행 가능한 코드 또는 프로시저 형태로 포맷팅하는 단계(로우 소스 생성기가 이 역할을 맡음)
- SQL 옵티마이저
: SQL 옵티마이저는 사용자가 원하는 작업을 가장 효율적으로 수행할 수 있는 최적의 데이터 액세스 경로를 선택해 주는 DBMS 핵심 엔진
  - 옵티마이저 최적화 단계
    1. 전달받은 쿼리를 수행하는 데 후보군이 될만한 실행계획을 찾음
    2. 데이터 딕셔너리에 미리 수집해 둔 오브젝트 통계 및 시스템 통계정보를 이용해 각 실행계획 및 예상비용을 산정
    3. 최저 비용을 나타내는 실행계획을 선택
  - 실행계획과 비용
    - SQL 옵티마이저는 자동차 내비게이션과 비슷 (경로 요약, 모의 주행)
    - DBMS의 'SQL 실행경로 미리보기' = 실행계획(Execution Plan)
    - 옵티마이저가 특정 실행계획을 선택하는 근거?
      ~~~
      예제) 테이블과 인덱스를 생성해본 후, 테스트
      CREATE TABLE EMP (
        EMPNO NUMBER(5) NOT NULL,
        ENAME VARCHAR2(20) NOT NULL,
        JOBS VARCHAR2(20),
        DEPTNO NUMBER(5) NOT NULL
      );

      CREATE TABLE T
      AS
      SELECT D.NO, E.*
      FROM EMP E, (SELECT ROWNUM NO FROM DUAL CONNECT BY LEVEL <= 1000) D;

      CREATE INDEX t_x01 on t(deptno, no);
      CREATE INDEX t_x02 on t(deptno, jobs, no);

      * 생성한 T 테이블에 통계정보를 수집하는 명령어
      exec dbms_stats.gather_table_stats(user, 't');

      * AutoTrace를 활성하고 SQL을 실행하면, 실행계획을 확인할 수 있음
      set autotrace on ;

      각 인덱스 스캔 및 TABLE FULL SCAN 확인해보면 t_x01의 cost가 가장 낮음
      ~~~
    - 옵티마이저가 t_x01 인덱스를 선택한 근거가 비용임을 알 수 있음
    - 비용(cost)는 쿼리를 수행하는 동안 발생한 것으로 예상하는 I/O 횟수 또는 예상 소요시간을 표현
    - Cost는 옵티마이저가 여러 통계정보를 활용해서 계산해 낸 예상값
  - 옵티마이저 힌트
    - SQL 옵티마이저는 대부분 좋은 선택을 하지만, 완벽하지 않음
    - 옵티마이저 힌트를 이용해 데이터 액세스 경로를 바꿀 수 있음
    - /*+ INDEX(A 고객_PK) */
  
1.2 SQL 공유 및 재사용
  - 소프트 파싱 vs 하드 파싱

  - 바인드 변수의 중요성

1.3 데이터 저장 구조 및 I/O 메커니즘
  - SQL이 느린 이유
  - 데이터베이스 저장 구조
  - 블록 단위 I/O
  - 시퀀셜 액세스 vs 랜덤 액세스 
  - 논리적 I/O vs 물리적 I/O
  - Single Block I/O vs Multiblock I/O
  - Table Full Scan vs Index Range Scan
  - 캐시 탐색 메커니즘
  











  
