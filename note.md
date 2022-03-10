## 엔티티 매핑
### 목차

1. 객체와 테이블 매핑
2. 데이터베이스 스키마 자동  생성
3. 필드와 컬럼 매핑
4. 기본 키 매핑
5. 실전 예제 -1. 요구사항 분석과 기본 매핑

### 1. 객체와 테이블 매핑

* 객체와 테이블 매핑 : @Entity, @Table
* 필드와 컬럼 매핑: @Column
* 기본 키 매핑: @Id
* 연관관계 매핑: @ManyToOne, @JoinColumn


### @Entity
* @Entity가 붙은 클래스는 JPA가 관리, 엔티티라 한다.
* JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 필수

* 주의
  * 기본 생성자 필수(파라미터가 없는 public 또는 protected 생성자)
  * final 클래스, enum, interface, inner 클러스 사용 X
  * 저장할 필드에 final 사용 X


### @Table은 엔티티와 매핑할 테이블 지정
|속성|기능|기본값|
|:------|:-----|:-----|
|name|매핑할 테이블 이름|엔티티 이름을 사용|
|catalog|데이터베이스 catalog 매핑||
|schema|데이터베이스 schema||
|uniqueConstraints(DDL)|DDL 생성 시에 유니크 제약 조건 생성||

```java
name 속성 
@Table(name = "MBR")
public class Member{
}
```
쿼라 실행 시 Meber 테이블을 조회하지 않고 name 속성으로 지정한 MBR 테이블을 조회한다.

### 2. 데이터베이스 스키마 자동  생성
* DDL을 애플리케이션 실행  시점에 자동 생성
* 테이블 중심 -> 객체 중심
* 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 생성
* 이렇게 생성된 DDL은 개발 장비에서만 사용
* 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용
(Local 실행 시 테이블이 없어도 자동으로 생성해 준다.)


### 2. 데이터베이스 스키마 자동  생성 - 속성
hibernate.hbm2ddl.auto
|옵션|설명|
|:------|:-----|
|create|기존테이블 삭제 후 다시 생성(DROP + CREATE)|
|create-drop|create와 같으나 종료시점에 테이블 DROP|
|update|변경분만 반영(운영DB에는 사용하면 안 됨)|
|vaildate|앤티티와 테이블이 정상 매핑되었는지만 확인|
|none|사용하지 않음|   


### 2. 데이터베이스 스키마 자동  생성 - 주의
* **운영 장비에는 절대 create, create-drop, update 사용하면 안된다.**
* 개발 초기 단계는 create 또는 update
* 테스트 서버는 update 또는 validate
* 스테이징과 운영 서버는 validate 또는 none

### DDL 생성 기능
* 제약조건 추가: 회원 이름은 필수, 10자 초과x
 * @Column(nullable = false, length = 10)
* 유니크 제약조건 추가
```java
name 속성 
@Table(uniqueConstraints = {@UniqueConstraint(name = "NAME_AGE_UNIQUE", columnNames = {"NAME", "AGE"})})
```
* DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고  JPA의 실행 로직에는 영향을 주지 않는다.

### 3. 필드와 컬럼 매핑

### 매핑 어노테이션 정리
hibernate.hbm2ddl.auto
|옵션|설명|
|:------|:-----|
|@Colum|컬럼 매핑|
|@Temporal|날짜 타입 매핑|
|@Enumerated|enum 타입 매핑|
|@Lob|BLOB, CLOB 매핑|
|@Transient|특정 필드를 컬럼에|

### @Column
|속성|설명|기본값|
|:------|:-----|:-----|
|name|필드와 매핑할 테이블의 컬럼 이름|객체의 필드 이름|
|insertable,</br> updateable|등록, 변경 가능 여부|TURE|
|nullable(DDL)|null 값의 허용 여부를 설정한다 false로 설정하면 DDL 생성 시에 NOT NULL 제약 조건이 붙는다.||
|unique(DDL)|@Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용한다.||
|columnDefinition(DDL)|데이터베이스 컬럼 정보를 직접 줄 수 있다. </br> ex)varchar(100) default 'EMPTY'|필드의 자바 타입과 방언 정보를 사용|
|length(DDL)|문자 같이 제약조건, String 타입에만 사용한다.|255|
|precision,</br> scale(DDL)|BigInteger, BigDecimal 타입에서 사용, 각각 소수점 포함 자리수, 소수의 자리수를 의미.|precision=19,|

### @Enumerated
자바 enum 타입을 매핑할 때 사용
주의! ORDINAL 사용 X -> 추후 enum 값 추가 시 데이터 꼬일 수 있다.
|속성|설명|기본값|
|:------|:-----|:-----|
|value|EnumType.ORDINAL : enum 순서를 데이터 베이스에 저장 </br> EnumType.STRING: enum 이름을 데이터베이스에 저장|EnumType.ORDINAL|

### @Temporal
날짜 타입(java.util.Data, java.util.Calendar)을 매핑할 때 사용
참고: LocalDate, LocalDateTime을 사용할 때는 생략 가능(최신 하이버네이트 지원 -> 최신버전 사용 중이면 Temporal 사용안해도 됨)
|속성|설명|기본값|
|:------|:-----|:-----|
|value|TemporalType.DATE: 날짜, 데이터베이스 date 타입과 매핑(예:2013-10-11) </br>TemporalType.TIME:  시간, 데이터베이스 time 타입과 매핑(예: 11:11:11)</br>TemporalType.TIMESTAMP: 날짜와 시간, 데이터베이스 timestamp 타입과 매핑(예: 2013-10-11 11:11:11)||

### @Lob
데이터베이스 BLOB, CLOB 타입과 매핑
* @Lob에는 지정할 수 있는 속성이 없다.
* 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑
 * CLOB: String, char[], java.sql.CLOB
 * BLOB: byte[],java.sql.BLOB 

### @Transient
* 필드 매핑 X
* 데이터베이스에 저장 X, 조회 X
* 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용
```java
@Transient
private Integer temp;
```

### 4. 기본 키 매핑
### 기본 키 매핑 어노테이션
* @Id
* @GeneratedValue
```java
@Id @GeneratedValue(Strategy = GenerationType.AUTO)
private Long id;
```

### 기본 키 매핑 방법
* 직접 할당: @Id만  사용
* 자동 생성: @GeneratedValue
 * IDENTITY: 데이터베이스에 위임, MYSQL
 * SEQUENCE: 데이터베이스 시퀀스 오브젝트 사용, ORACLE
  * @SequenceGenerator 필요
 * TABLE: 키 생성용 테이블 사용, 모든 DB에서 사용
  * @TableGenerator 필요
 * AUTO: 방언에 따라 자동 지정, 기본값     

#### IDENTITY 전략 - 특징
* 기본 키 생성을 데이터베이스 위임
* 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용(예: MYSQL의 AUTO_INCREMENT)
* JPA는 보통 트랜잭션 커밋 시점에 INSERTSQL 실행
* AUTO_INCREMENT는 데이터베이스에 INSERT SQL을 실행한 이후에 ID 값을 알 수 있음
* IDENTITY 전략은 em.persist() 시점에 즉시 INSERT SQL 실행하고 DB에서 식별자를 조회

#### IDENTITY 전략 - 매핑
```java
@Id @GeneratedValue(Strategy = GenerationType.IDENTITY)
private Long id;
```

#### SEQUENCE 전략 - 특징
* 데이터베이스 시퀀스는 유일한 값을 수선대로 생성하는 특별한 데이터베이스 오브젝트(예: 오라클 시퀀스)
* 오라클, PostgreSQL, DB2, H2 데이터베이스에서 사용

#### SEQUENCE 전략 - 매핑
```java
@Entity
@SequenceGenerator(
      name = "MEMEBER_SEQ_GENERATOR",
      sequenceName = "MEMBER_SEQ", //매핑할 데이터베이스 시퀀스 이름
      initialValue = 1, allocationSize = 1)
public class Member{
  @Id
  @GeneratedValue(Strategy = GenerationType.SEQUENCE, generator = "MEMBER_SEQ_GENERATOR")
  private Long id;
} 
```
#### SEQUENCE - @SequenceGenerator
* 주의: allocationSize 기본값 = 50

|속성|설명|기본값|
|:------|:-----|:-----|
|name|식별자 생성기 이름|필수|
|sequenceName|데이터베이스에 등록되어 있는 시퀀스 이름|hibernate_sequence|
|initialValue|DDL 생성 시에만 사용 됨, 시퀀스 DDL을 생성할 때 처음 1 시작하는 수를 지정한다.|1|
|allocationSize|시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용 됨)</br>데이터베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드시 1로 설정해야 한다.|50|
|catalog, schema|데이터베이스 catalog, schema 이름||

#### TABLE 전략
* 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략
* 장점: 모든 데이터베이스에 적용 가능
* 단점: 성능

#### TABLE 전략 - 매핑
```java
@Entity
@TableGenerator(
      name = "MEMEBER_SEQ_GENERATOR",
      table = "MY_SEQUENCES"
      pkColumnValue = "MEMBER_SEQ", allocationSize = 1)
public class Member{
  @Id
  @GeneratedValue(Strategy = GenerationType.TABLE, generator = "MEMBER_SEQ_GENERATOR")
  private Long id;
} 
```
아래 테이블이 생성된다.
```mysql
create table MY_SEQUENCES(
 sequence_name varchar(255) not null,
 next_val bigint,
 primary key(sequence_name)
) 
```



### 5. 실전 예제 1 - 요구사항 분석과 기본 매핑




