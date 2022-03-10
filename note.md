
#목차

*객체와 테이블 매핑
*데이터베이스 스키마 자동  생성
*필드와 컬럼 매핑
*기본 키 매핑
*실전 예제 -1. 요구사항 분석과 기본 매핑

#객체와 테이블 매핑

객체와 테이블 매핑 : @Entity, @Table
필드와 컬럼 매핑: @Column
기본 키 매핑: @Id
연관관계 매핑: @ManyToOne, @JoinColumn


#@Entity
*@Entity가 붙은 클래스는 JPA가 관리, 엔티티라 한다.
*JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 필수

*주의
 *기본 생성자 필수(파라미터가 없는 public 또는 protected 생성자)
 *final 클래스, enum, interface, inner 클러스 사용 X
 *저장할 필드에 final 사용 X



#@Table은 엔티티와 매핑할 테이블 지정








