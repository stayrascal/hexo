---
title: Spring-test
date: 2017-04-16 23:22:18
tags: Spring 
---

# Spring Annotation
* @BootstrapWith: 配置Spring TestContext框架如，@BootstrapWith用于指定自定义TestContextBootstrapper.
* @ContextConfiguration: 确定如何加载和配置ApplicationContext进行集成测试。
	- @ContextConfiguration(locations = {}, classes = {}, inheritInitializers = true)
* @WebAppConfiguration:用于声明为集成测试加载的ApplicationContext
	- default value: "file:src/main/webapp"
	- 必须和ContextConfiguration一起联合使用
* @ContextHierarchy：定义用于集成测试的ApplicationContexts层次结构
	- `@ContextHierarchy({
    @ContextConfiguration(classes = AppConfig.class),
    @ContextConfiguration(classes = WebConfig.class)
})`
* @ActiveProfiles:声明哪些bean定义配置文件应该处于活动状态。
* @TestPropertySource: 配置属性文件和内联属性的位置,加载到ApplicaitonContext的环境中
	- 优先级高于operating system’s environment or Java system properties as well as property sources 
* @DirtiesContext：当一个application context被标为dirty的时候，它将会被从cache中移除
	- 可以注解在类或者方法上
* @TestExecutionListeners
* @Commit
* @Rollback
* @BeforeTransaction
* @AfterTransaction
* @Sql
* @SqlConfig
* @SqlGroup
	- `@SqlGroup({
    @Sql(scripts = "/test-schema.sql", config = @SqlConfig(commentPrefix = "`")),
    @Sql("/test-user-data.sql")
)}`

