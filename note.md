## 엔티티 매핑
### 목차

* 객체와 테이블 매핑
* 데이터베이스 스키마 자동  생성
* 필드와 컬럼 매핑
* 기본 키 매핑
* 실전 예제 -1. 요구사항 분석과 기본 매핑

### 객체와 테이블 매핑

* 객체와 테이블 매핑 : @Entity, @Table
* 필드와 컬럼 매핑: @Column
* 기본 키 매핑: @Id
* 연관관계 매핑: @ManyToOne, @JoinColumn


#### @Entity
* @Entity가 붙은 클래스는 JPA가 관리, 엔티티라 한다.
* JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 필수

* 주의
  * 기본 생성자 필수(파라미터가 없는 public 또는 protected 생성자)
  * final 클래스, enum, interface, inner 클러스 사용 X
  * 저장할 필드에 final 사용 X


#### @Table은 엔티티와 매핑할 테이블 지정
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
  
  @Id
  private Long id;
  private String name;
}
```
쿼라 실행 시 Meber 테이블을 조회하지 않고 name 속성으로 지정한 MBR 테이블을 조회한다.

### 데이터베이스 스키마 자동 생성
* DDL을 애플리케이션 실행  시점에 자동 생성
* 테이블 중심 -> 객체 중심
* 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 생성
* 이렇게 생성된 DDL은 개발 장비에서만 사용
* 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용
(Local 실행 시 테이블이 없어도 자동으로 생성해 준다.)


### 데이터베이스 스키마 자동생성 - 속성
hibernate.hbm2ddl.auto
|옵션|설명|
|:------|:-----|
|create|기존테이블 삭제 후 다시 생성(DROP + CREATE)|
|create-drop|create와 같으나 종료시점에 테이블 DROP|
|update|변경분만 반영(운영DB에는 사용하면 안 됨)|
|vaildate|앤티티와 테이블이 정상 매핑되었는지만 확인|
|none|사용하지 않음|   


### 데이터베이스 스키마 자동 생성 - 주의
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










 






