---
title: Spring boot에서 Quartz 라이브러리로 스케줄링 하기
teaser: feat. cron expression
category: programming
tags: [programming, spring boot, quartz, scheduler]
---


<h1>Quartz</h1>

Quartz는 로거를 사용해서 과정을 출력해주니 미리 import 해두면 편하다.

Gradle import
```
compile('org.quartz-scheduler:quartz:2.2.1')
```

1. 일을 할 클래스를 만든다.
```
public class SimpleJob implements Job{
    ...
    @Override
    public void execute(JobExecutionContext jobExecutionContext) {
        // 스케줄에 따라 실행할 일 작성
    }
}
```

2. 설정 정보를 담음 Config 클래스를 만든다.
```
@Configuration
public class SchedulerConfig {

    private static final Logger LOG = LoggerFactory.getLogger(SchedulerConfig.class);

    @Autowired
    @Qualifier("CronJobTrigger")
    private Trigger cronJobTrigger;

    @Bean
    public JobFactory jobFactory(ApplicationContext applicationContext) {
        AutowiringSpringBeanJobFactory jobFactory = new AutowiringSpringBeanJobFactory();
        jobFactory.setApplicationContext(applicationContext);
        return jobFactory;
    }

    @Bean
    public SchedulerFactoryBean schedulerFactoryBean(JobFactory jobFactory, @Qualifier("simpleJobDetail") JobDetail jobDetail)
            throws IOException {
        SchedulerFactoryBean factory = new SchedulerFactoryBean();
        factory.setJobFactory(jobFactory);
        factory.setQuartzProperties(quartzProperties());
        factory.setTriggers(cronJobTrigger); // 팩토리에서 cronJobTrigger을 준다
        LOG.info("Job will start...");
        return factory;
    }

    @Bean(name = "CronJobTrigger")
    public CronTriggerFactoryBean CronJobTrigger(
            @Qualifier("simpleJobDetail") JobDetail jobDetail,
            @Value("${job.cron.expression}") String expression) {
        return createCronTrigger(jobDetail, expression);
    }

    private static CronTriggerFactoryBean createCronTrigger(@Qualifier("simpleJobDetail") JobDetail jobDetail, String expression){
        CronTriggerFactoryBean factoryBean = new CronTriggerFactoryBean();
        factoryBean.setJobDetail(jobDetail);
        factoryBean.setCronExpression(expression);
        factoryBean.setMisfireInstruction(SimpleTrigger.MISFIRE_INSTRUCTION_FIRE_NOW);
        return factoryBean;
    }

    @Bean
    public Properties quartzProperties() throws IOException {
        PropertiesFactoryBean propertiesFactoryBean = new PropertiesFactoryBean();
        propertiesFactoryBean.setLocation(new ClassPathResource("/quartz.properties")); // quartz 설정 파일을 만들고 경로 등록
        propertiesFactoryBean.afterPropertiesSet();
        return propertiesFactoryBean.getObject();
    }

    @Bean
    public JobDetailFactoryBean simpleJobDetail() {
        JobDetailFactoryBean factoryBean = new JobDetailFactoryBean();
        factoryBean.setJobClass(SimpleJob.class); //여기에 일할 클래스를 등록
        factoryBean.setDurability(true);
        return factoryBean;
    }
}
```



이렇게 프로퍼티 설정 하면 되는데
CronTrigger가 아니라 기본 제공되는 SimpleTrigger도 있으니 참고하면 된다.
SimpleTrigger의 경우 Date로 시간을 지정하고 frequency를 지정할 수 있다.
