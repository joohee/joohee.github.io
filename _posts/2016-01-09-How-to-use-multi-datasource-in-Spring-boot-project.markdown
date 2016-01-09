---
layout: post
title:  "How to use multi-datasource in Spring boot project"
date:   2016-01-09 17:40:00
categories: spring-boot
---

### Spring boot에서 multi-datasource 활용하기.
- 목적
대부분의 경우 1개의 datasource를 사용하지만 서비스가 커지고 요구사항에 따라 다른 datasource에 접근해야 할 수 있다.
이를 대비하여 multi-datasource 접속 테스트를 진행한다.
- 선행 작업 (@see MainRepositoryConfig.java, OtherRepositoryConfig.java, DataSourceProperties.java)
JPA는 EntityManagerFactory 로부터 EntityManager와 TransactionManager를 생성하여 사용해야 한다.
default EntityManager* 를 사용하지 않고 @Transactional 을 설정할 때 마다 transactionManager name을 지정해준다.
- 과정
DataSourceProperties.java에서 DataSourceBuilder를 이용하여 각각의 properties를 조회, Datasource를 생성한다.
MainRepositoryConfig.java, OtherRepositoryConfig.java 에서 EntityManagerFactory를 생성한다.
각각에 맞는 Datasource 를 injection 하고, entity class가 포함되어 있는 package를 지정한다.
위에서 생성한 EntityManagerFactory를 기반으로 EntityManger, TransactionManager를 생성한다.
주의) default EntityMangaerFactory 등이 생성되지 않으므로 @Primary 를 이용하여 우선순위를 설정해둔다.
해당 설정이 들어있는 class 최 상단에 아래와 같은 annotation을 생성한다.
ex. MainRepositoryConfig.java
> 
> @Configuration
> @EnableTransactionManagement
> @EnableJpaRepositories(entityManagerFactoryRef = "entityManagerFactoryMain", transactionManagerRef = "transactionManagerMain", basePackages = { "com.sk.prototype.repository.main" }
> 
주의

entity 이름을 명시적으로 지정해주지 않으면 default entityManager가 생성되는데, 이 상황에서는 굳이 쓸 필요가 없다.
DatasourceProperties.java

> @Configuration
> @EnableConfigurationProperties
> public class DataSourceProperties {
> 
>     @Bean(name = "mainDataSource") @Qualifier("mainDataSource")
>     @Primary
>     @ConfigurationProperties(prefix="spring.datasource.main")
>     public DataSource mainDataSource() {
>         return DataSourceBuilder.create().build();
>     }
> 
>     @Bean(name = "otherDataSource") @Qualifier("otherDataSource")
>     @ConfigurationProperties(prefix="spring.datasource.other")
>     public DataSource otherDataSource() {
>         return DataSourceBuilder.create().build();
>     }
> }


주의) transactionManger 와 entityManager, entityManagerFactory에 명시적으로 bean name 을 지정하지 않은 경우 injection error가 발생할 수 있으므로 이를 방지하기 위해 @Primary annotation을 붙여준다.
entityManagerFactory를 생성할 때 package를 지정해주게 되어 있는데, 이 곳에 Entity 로 사용하는 java class가 포함되어 있어야 한다. 안 그러면 에러남.

MainRepositoryConfig.java
 
> @Configuration
> @EnableTransactionManagement
> @EnableJpaRepositories(entityManagerFactoryRef = "entityManagerFactoryMain", transactionManagerRef = "transactionManagerMain",
>                       basePackages = { "com.sk.prototype.repository.main" } )
> public class MainRepositoryConfig {
>   @Inject
>   private JpaProperties jpaProperties;
> 
>   @Inject @Qualifier("mainDataSource")
>   private DataSource mainDataSource;
> 
>   @Bean(name = "entityManagerMain")
>   @Primary
>   public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
>       return entityManagerFactoryMain(builder).getObject().createEntityManager();
>   }
> 
>   @Bean(name = "entityManagerFactoryMain")
>   @Primary
>   public LocalContainerEntityManagerFactoryBean entityManagerFactoryMain (EntityManagerFactoryBuilder builder) {
>       return builder
>               .dataSource(mainDataSource)
>               .properties(getVendorProperties(mainDataSource))
>               .packages("com.sk.prototype.domain.main")
>               .persistenceUnit("mainPersistenceUnit")
>               .build();
>   }
> 
>   private Map<String, String> getVendorProperties(DataSource dataSource) {
>       return jpaProperties.getHibernateProperties(dataSource);
>   }
> 
>   @Bean(name = "transactionManagerMain")
>   @Primary
>   PlatformTransactionManager transactionManagerMain(EntityManagerFactoryBuilder builder) {
>       return new JpaTransactionManager(entityManagerFactoryMain(builder).getObject());
>   }
> }
> OtherRepositoryConfig.java

다른 Database에 접근하려는 경우.

>   @Configuration
>   @EnableTransactionManagement
>   @EnableJpaRepositories(entityManagerFactoryRef = "entityManagerFactoryOther", transactionManagerRef = "transactionManagerOther",
>                          basePackages = { "com.sk.prototype.repository.other" } )
>   public class OtherRepositoryConfig {
>       @Inject
>       private JpaVendorAdapter jpaVendorAdapter;
> 
>       @Inject
>       private JpaProperties jpaProperties;
> 
>       @Inject @Qualifier("otherDataSource")
>       private DataSource otherDataSource;
> 
>       @Bean(name = "entityManagerOther")
>       public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
>           return entityManagerFactoryOther(builder).getObject().createEntityManager();
>       }
> 
>       @Bean(name = "entityManagerFactoryOther")
>       public LocalContainerEntityManagerFactoryBean entityManagerFactoryOther (EntityManagerFactoryBuilder builder) {
>           return builder
>                   .dataSource(otherDataSource)
>                   .properties(getVendorProperties(otherDataSource))
>                   .packages("com.sk.prototype.domain.other")
>                   .persistenceUnit("otherPersistenceUnit")
>                   .build();
>       }
> 
>       private Map<String, String> getVendorProperties(DataSource dataSource) {
>           return jpaProperties.getHibernateProperties(dataSource);
>       }
> 
>       @Bean(name = "transactionManagerOther")
>       PlatformTransactionManager transactionManagerOther(EntityManagerFactoryBuilder builder) {
>           return new JpaTransactionManager(entityManagerFactoryOther(builder).getObject());
>       }
>   }


application.yml

> spring:
>   datasource:
>     main:
>       platform: mysql
>       url: jdbc:mysql://localhost/main-db
>       username: root
>       password:
>       driverClassName: com.mysql.jdbc.Driver
>     other:
>       platform: mysql
>       url: jdbc:mysql://localhost/other-db
>       username: root
>       password:
>       driverClassName: com.mysql.jdbc.Driver
       
테스트
선행사항
datasource에 맞는 database를 설정한다.
Application.java를 실행한다.
실행 후 MainRepositoryConfig로 설정된 곳에 account테이블이, OtherRepositoryConfig로 생성된 곳에 youtube_movie 테이블이 생성되고 데이터가 들어오는지 확인한다.