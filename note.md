### 자바 ORM 표준 JPA 프로그래밍 - 
## 엔티티 매핑
# 목차

* 객체와 테이블 매핑
* 데이터베이스 스키마 자동  생성
* 필드와 컬럼 매핑
* 기본 키 매핑
* 실전 예제 -1. 요구사항 분석과 기본 매핑

# 객체와 테이블 매핑

* 객체와 테이블 매핑 : @Entity, @Table
* 필드와 컬럼 매핑: @Column
* 기본 키 매핑: @Id
* 연관관계 매핑: @ManyToOne, @JoinColumn


# @Entity
* @Entity가 붙은 클래스는 JPA가 관리, 엔티티라 한다.
* JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 필수

* 주의
  * 기본 생성자 필수(파라미터가 없는 public 또는 protected 생성자)
  * final 클래스, enum, interface, inner 클러스 사용 X
  * 저장할 필드에 final 사용 X



# @Table은 엔티티와 매핑할 테이블 지정
|속성|기능|기본값|
|:------|:-----|:-----|
|name|매핑할 테이블 이름|엔티티 이름을 사용|
|catalog|데이터베이스 catalog 매핑||
|schema|데이터베이스 schema||
|uniqueConstraints(DDL)|DDL 생성 시에 유니크 제약 조건 생성||

```java
## name 속성 
@Table(name = "MBR")
public class Member{
  
  @Id
  private Long id;
  private String name;
}
```
쿼라 실행 시 Meber 테이블을 조회하지 않고 name 속성으로 지정한 MBR 테이블을 조회한다.






