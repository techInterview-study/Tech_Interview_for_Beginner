# 저장 프로시저(Stored PROCEDURE)

## 📌 **저장 프로시저 (SP, Stored Procedure)** ❓❓

`정의` 

- 일련의 쿼리를 마치 하나의 함수처럼 실행하기 위한 쿼리의 집합이다. [데이터베이스](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)에 대한 일련의 작업을 정리한 절차를 [관계형 데이터베이스 관리 시스템](https://ko.wikipedia.org/wiki/%EA%B4%80%EA%B3%84%ED%98%95_%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4_%EA%B4%80%EB%A6%AC_%EC%8B%9C%EC%8A%A4%ED%85%9C)에 저장한(지속성) 것으로, **영구저장모듈**(Persistent Storage Module)이라고도 불린다.
- 자바의 **함수**와 비슷한 개념 ❗
    
    저장 프로시저와 함수는 각각 다른 컨텍스트에서 사용되지만, `"편리하게 저장하고 가져다 쓰는" 측면`에서는 유사한 개념이다.
    

`용도`

- 실무에서는 프로그램에서 만들어 놓은 SQL문을 저장해 놓고, 필요할 때마다 호출해서 사용하는 방식으로 프로그램을 만든다.
- 저장 프로시저는 이러한 방식이 가능하도록하는 각 DBMS 에서 제공하는 프로그래밍 기능이다.
- Oracle, MySQL 등 대부분의 DBMS 에서 제공하고 있다.

<aside>

💡 `정리`: 저장 프로시저는 쿼리문들의 집합으로, 어떤 동작을 여러쿼리를 거쳐서 일괄적으로 처리할 때 사용한다.

</aside>


## 📌 **일반적인 SQL 문과 저장 프로시저 동작 방식 비교**
⚠️SQL Server에서 제공하는 저장 프로시저를 중심으로 설명
    
- 참고
    
    저장 프로시저(Stored Procedure)는 SQL Server뿐만 아니라 다양한 관계형 데이터베이스 관리 시스템(RDBMS)에서 지원되는 개념이다.
    
    아래와 같이 여러 RDBMS에서도 저장 프로시저 또는 유사한 개념이 제공되며, 각각의 시스템에서는 조금씩 다른 문법이나 특성을 가질 수 있다.
    
    1. **Microsoft SQL Server:** 저장 프로시저는 SQL Server에서 널리 사용되며, T-SQL(Transact-SQL)이라는 확장된 SQL 언어로 작성됨.
    2. **MySQL:** MySQL에서는 저장 프로시저를 "Stored Procedure"라고 부르며, 표준 SQL 문법을 따름. MySQL에서는 프로시저 작성 및 호출이 가능함.
    3. **Oracle Database:** Oracle에서는 저장 프로시저를 "PL/SQL(Procedural Language/Structured Query Language)"이라는 언어를 사용하여 작성함.
    4. **PostgreSQL:** PostgreSQL에서는 저장 프로시저를 "Stored Procedure"로 부르며, PL/pgSQL이라는 언어를 사용하여 작성함.
    5. **SQLite:** SQLite도 저장 프로시저 개념을 지원하며, 이를 "Stored Procedure" 또는 "Trigger"라고 부르기도 함.

<br><br>
📍저장 프로시저를 사용하면보통 일반적인 T-SQL 사용보다 시스템의 성능이 향상 된다고 한다.

### ⭐ **일반 T-SQL 동작 방식**

<image src="./img/db_stored_procedure_1.png" width = "500px" height="300px" alt = "T-SQL img" >

위의 그림은 일반적인 T-SQL 문을 처음으로 실행하면 위의 프로세스로 동작한다.

만약에 아래 쿼리를 실행한다고 가정하자.

```sql
SELECT nameFROM userTbl;
```

그러면 먼저 구문 분석단계에서 구문 자체에 오류가 없는지 분석을 할 것이다. 만약 오타가 있으면 여기서 오류가 발생되어 에러메시지를 띄울 것이다.

다음은 개체 이름 확인 단계에서 userTbl 이라는 테이블이 현재 데이터베이스에 있는지 확인을 한다. 만약에 userTbl이 있으면 그안에 name이라는 열이 있는지를 확인할 것이다.

그다음 사용권한 확인 단계에서 userTbl을 현재 접근중인 사용자가 권한이 있는지를 확인한다.

다음으로 최적화 단계에서 해당 쿼리문이 가장 좋은 성능을 낼 수 있는 경로를 결정한다. 인덱스 사용여부에 따라 경로가 결정된다고 보면된다. 위의 쿼리의 경우 전체 데이터를 가져오기 때문에 아마도 테이블 스캔이나 클러스터 인덱스 스캔이 될 것이다.

다음은 최적화된 결과를 바탕으로 컴파일 및 실행 계획 등록 단계에서 해당 실행계획 결과를 메모리(캐시)에 등록한다.

그리고 컴파일된 결과를 실행한다. 단 한 줄의 쿼리지만 이렇게 많은 절차를 거친다.

만약에 동일한 SQL문을 실행하면 아래와 같이 단순하게 동작하게 된다. 여러 과정이 생략되다 보니 시간이 단축될 것이다.

<image src="./img/db_stored_procedure_2.png" width = "500px" height="200px" alt = "T-SQL img" >

만약에 메모리(캐시)에 동일한 쿼리가 없다면 위의 전체 과정을 다시 반복할 것이다. 여기서 주의해야할 점은 쿼리 전체가 한글자도 틀리지 않고 같아야 한다는 것이다.

### ⭐ 저장 프로시저의 동작 방식


<image src="./img/db_stored_procedure_3.png" width = "500px" height="250px" alt = "SP img" >

먼저 저장 프로시저를 **정의** 했을때 작동방식을 알아보자.

일단 먼저 해당 저장 프로시저에서 구문 오류가 있는 지를 파악하는 과정을 거친다.

다음은 지연된 이름 확인(deferred name resolution) 과정을 거치게 되는데 이는 저장 프로시저의 특징중 하나이니 잘 기억해두자.

저장 프로시저의 경우에는 프로시저를 정의하는 시점에 테이블과 같은 해당 개체의 존재 여부와 상관없이 정의가 가능한데, 그 이유는 해당 테이블의 존재 여부를 프로시저의 실행 시점에 확인하기 때문이다. 그렇기에 해당 테이블의 존재 여부와 상관없이 프로시저는 정의할 수 있다. 그런데 테이블의 열이름이 틀리면 오류가 발생된다.

실무에서 없는 테이블을 프로시저 정의에 사용하는 등의 실수를 할 수 있으니 주의가 필요하다.

다음은 생성권한은 확인하는 단계인데 사용자가 저장 프로시저를 생성할 권한이 있는지를 확인하는 과정이다.

마지막으로 시스템 테이블 등록을 진행한다. 저장 프로시저의 이름과 코드가 관련 시스템 테이블에 등록되는 과정이다. 만약 관련 내용을 확인하고자 하면 카탈로그 뷰 sys.objects 및 sys.sql_modules 등을 확인하자.
<image src="./img/db_stored_procedure_4.png" width = "500px" height="350px" alt = "SP img" >

다음은 첫번째로 저장 프로시저를 실행했을때 벌어지는 일이다.

일반적인 쿼리를 수행하는 것과 비슷하다. 일단 정의 단계에서 구문분석은 끝났기때문에 따로 구문분석을 하지 않는다.

앞서 정의시에 지연된 이름 확인이란게 있었는데, 실제로 해당 개체가 유효한지를 개체이름 확인 단계에서 진행하게 된다. 다시 말해서 저장 프로시저의 실행 시에만 해당 개체가 존재하면 실행이 된다.

그리고 그 이후에 앞서 쿼리문을 돌렸을때와 같이 사용권한 확인, 최적화, 컴파일 및 실행계획 등록 단계를 거치 실행 되게 된다.

<image src="./img/db_stored_procedure_5.png" width = "500px" height="200px" alt = "SP img" >

이후에 두번째 실행 부터는 메모리(캐시)에 있는 것을 그대로 가져와 재사용하게 되어 수행시간이 많이 단축되게 된다.

이렇게만 보면 일반 sql문과 거의 차이가 없어보인다.

🔻하지만 아래 상황을 보자🔻

```sql
SELECT *FROM userTblWHERE name ='이승기';
SELECT *FROM userTblWHERE name ='성시경';
SELECT *FROM userTblWHERE name ='은지원';
```

해당 쿼리는 where 조건의 값만 다르다. 그런데 앞서 말한 것처럼 일반 쿼리는 글자 하나라도 다르면 다른 쿼리라 인식하기 때문에 세 쿼리 모두 다 다른 것으로 인식해버린다.

그렇기 때문에 매번 최적화와 컴파일을 다시 수행해야한다.

이걸 그냥 아래와 같은 저장 프로시저로 만들면?

```sql
CREATE PROC select_by_name
	@Name NVARCHAR(3)
ASSELECT *FROM userTblWHERE name =@name;
```

```sql
EXEC select_by_name '이승기';
EXEC select_by_name '성시경';
EXEC select_by_name '은지원';
```

이렇게 하면 첫번째 이승기를 검색하는 과정에서만 최적화 및 컴파일을 수행하고 나머지는 메모리(캐시)에 있는것을 사용하게 된다. 실제로 다른 것들의 경과시간이 0ms 인지는 직접 확인해보길 바란다.

결국 자주 쓰는 쿼리라면 일반 쿼리를 여러 개 날리는 것보다 저정 프로시저를 쓰는게 성능적인 측면에서 효과적인 것을 확인 할 수 있다.


## 📌 저장 프로시저의 장점

1. **SQL Server의 성능을 향상 시킬 수 있다.**
    
    저장 프로시저를 처음에 실행하면 최적화, 컴파일 단계를 거쳐 그 결과가 캐시(메모리)에 저장되게 되는데, 후에 해당 SP를 실행하게 되면 캐시(메모리)에 있는 것을 가져와서 사용하므로 실행속도가 빨라지게 된다. 그렇기 때문에 일반 쿼리를 반복해서 실행하는 것보다 SP 를 사용하는게  성능적인 측면에서 좋다.
    
2.  **유지보수가 좋다.**
    
    데이터베이스에서 코드를 중앙 집중화하기 때문에 유지보수가 용이하다. 
    
    C#, Java등으로 만들어진 응용프로그램에서 직접 SQL문을 호출하지 않고 저장 프로시저의 이름을 호출하도록 설정하여 사용하는 경우가 많은데, 이때 개발자는 수정요건이 발생할때 코드 내 SQL문을 건드리는게 아니라 SP 파일만 수정하면 되기 때문에 유지보수 측면에서 유리해진다.
    
3. **재사용성이 좋다**
    
     한번 저장 프로시저를 생성해 놓으면, 언제든 실행이 가능하기 때문에 재활용 측면에서 매우 좋다.
    
4.  **보안을 강화할 수 있다.**
    
    자체 보안 설정 기능을 통해 단위 실행 권한을 부여할 수 있다. 읽기, 수정, 특정 컬럼에 대한 권한 설정과 같이 **세밀한 권한을 제어**할 수 있다.
    
    실제 테이블에 접근하여 다양한 조작을 하는 것은 위험하기 때문에 실무에서는 실제로 개발자에게는 sp권한만 주는 방식을 많이 사용한다.
    
5. **네트워크의 부하를 줄일 수 있다.**
    
    하나의 요청으로 여러 sql문 실행 가능하기 때문에 네트워크 소요시간을 줄이고 성능을 개선할 수 있다.
    

## 📌 **저장 프로시저의 단점**

1. **항상 성능이 향상되는 것은 아니다.**
    - 상세설명
        
        대부분의 경우에는 성능이 향상되나 항상 그렇지는 않다.
        
        저장 프로시저를 실행할 때 최적화 단계에서 인덱스를 사용할지 안할지를 결정하게 되는데, 인덱스를 사용한다고 항상 수행결과가 빨라지지 않는다.
        
        만약에 가져올 데이터가 다량인데 인덱스를 사용하면 오히려 성능이 나빠지게 될 것이다.
        
        저장 프로시저는 첫번째 수행 시에 최적화가 이루어져서 인덱스 사용여부가 결정되어 버린다.
        
        만약에 첫번째 수행때 데이터를 몇건만 가져오도록 파라미터가 설정되어 있다면, 인덱스를 사용하도록 최적화되어 컴파일 됐을 것이다.
        
        그런데 두번째 수행에서 많은 건수의 데이터를 가져오도록 파라미터가 들어가면, 일반 쿼리문이었다면 파라미터가 달라졌으니 다시 최적화되어 컴파일 되겠지만 저장 프로시저는 그냥 인덱스를 사용하는 프로시저를 실행시켜 버릴 것이다.
        
        이렇게 되어 버리면 성능에 크게 문제가 될 것이다. 이를 방지 하기 위해서는 저장 프로시저를 다시 컴파일 해줘야한다.
        
        다시 컴파일 하는 방법은 여러개가 있는데,
        
        실무에서는 보통 인덱스 사용여부가 불분명하다면 저장 프로시저를 생성한느 시점에서 아예 실행시마다 다시 컴파일 되도록 설정해버리기도 한다.
        
        ```sql
        DROP PROC sp_recompile_test
        GO
        CREATE PROC sp_recompile_test
        	[매개변수]
        WITH RECOMPILE
        AS
        	[사용 될 쿼리문]
        GO
        ```
        
2. **오류 발생시 디버깅이 일반 SQL에 비해 어렵다.**
    
    많은 데이터베이스 관리자가 저장 프로시저 생성 권한에 대한 보안을 위해 아무에게나 허용하지않도록 하고 있다. **`따라서 높은 수준의 기술과 경험이 필요하다.`** (기본적인 SQL문을 사용하는 것보다 복잡하다.) 
    
    비즈니스 로직의 일부로 사용하는 경우 업무의 사양 변경 시 외부 응용 프로그램과 함께 저장프로시저의 정의를 변경할 필요가 있다. 이때 불필요한 수고와 변경 실수에 의한 장애를 발생시킬 가능성이 있다. 
    
3. **낮은 처리 성능**
    
    문자, 숫자열 연산에 SP를 사용하면 오히려 c, java보다 느린 성능을 보일 수 있다.
    

## 📌 면접 질문 ❓

1. **저장 프로시져란?**
    - DB 내부에 저장된 SQL 명령들을 하나의 함수처럼 실행하기 위한 쿼리의 집합
    - 쿼리문의 함수화 버전
2. **장점?**
    - 네트워크 트래픽이 감소한다.
    - 보안성이 향상된다.
    - 일반 쿼리보다 성능향상 가능하다.
        
        일반 쿼리 : 실행시마다 컴파일프로시저
        프로시저 : 반복 실행시 캐시 메모리를 사용하여 실행 (컴파일 X)
        
    - 반복적인 작업을 피할 수 있다.
    - 개발 언어에 비의존적이다.
3. **단점?**
    - 연산 처리 성능이 낮다.
    - 디버깅이 어렵다.

### 출처

[https://velog.io/@sweet_sumin/저장-프로시저-Stored-Procedure](https://velog.io/@sweet_sumin/%EC%A0%80%EC%9E%A5-%ED%94%84%EB%A1%9C%EC%8B%9C%EC%A0%80-Stored-Procedure)

https://devkingdom.tistory.com/323