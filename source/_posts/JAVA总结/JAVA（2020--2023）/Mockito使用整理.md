---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
Mockito使用整理





[知乎总结mockito](https://juejin.cn/post/7036380453011456007)

[使用总结](https://blog.csdn.net/Wu_zx520/article/details/126313496)

1. 代码样式 --使用squaretest插件生成，然后调试

```java
@RunWith(MockitoJUnitRunner.class)
public class TryActivityAuthServiceImplTest {
    @Mock
    private HrUserRpcService mockHrUserRpcService;
    @Mock
    private RemoteCacheService mockRemoteCacheService;

    @InjectMocks  //注入可以注入bean的类
    private TryActivityAuthServiceImpl tryActivityAuthServiceImplUnderTest;
}
```

2. 静态类mock

   ①使用try(){
   限定作用范围，不然报错
   }

```java
//demo
        try(MockedStatic<AdapterManagerImpl> service = Mockito.mockStatic(AdapterManagerImpl.class, Mockito.RETURNS_MOCKS)) {
            service.when(() -> AdapterManagerImpl.getInstance()).thenReturn(adapterManager);

```

② //类级别的，多处使用


```java
@BeforeClass
    public static void setUp() {
        // mock静态方法
        AuthQueryBO authQuery = new AuthQueryBO();
        authQuery.setSeller("fds");
        //静态类mock
        service = Mockito.mockStatic(LoginUserContext.class, Mockito.RETURNS_MOCKS);
        service.when(() -> LoginUserContext.getContext().toAuthQuery()).thenReturn(authQuery);
    }
@AfterClass
public static void over() {
    if(!service.isClosed()) {
        service.close();
    }
}

//demo2
@Before  每个方法执行前执行
public void setUp() throws Exception {
    areaServiceImplUnderTest = new AreaServiceImpl();
    areaServiceImplUnderTest.areaDao = areaDao;
    areaServiceImplUnderTest.areaService = areaService;
    areaServiceImplUnderTest.directoryConfig = directoryConfig;
    areaServiceImplUnderTest.redisWrapper = redisWrapper;
    areaServiceImplUnderTest.acesUtil = acesUtil;
    areaServiceImplUnderTest.addressReadExportServiceAdapter = addressReadExportServiceAdapter;
 
}
@BeforeClass  类维度，因此为静态方法
public static void staticUp(){
 // mock静态方法
    //静态类mock
    Properties props = new Properties();
    //dev:测试,cn：中国线上,th：泰国,id：印尼
    props.put("env", "dev");
    //dev:测试环境,uat：预发,prd：线上
    props.put("profile", "dev");
    BizConfig bizConfig = new BizConfig(props);
}
```
```java
**暂未成功使用类级别注释解决，使用try作用于方法范围成功**
// private AreaServiceImpl areaServiceImplUnderTest;

@Mock
AdapterManagerImpl adapterManager;
  
public void testxx(){
try( MockedStatic<AdapterManagerImpl> service = Mockito.mockStatic(AdapterManagerImpl.class, Mockito.RETURNS_MOCKS)){
    service.when(() -> AdapterManagerImpl.getInstance()).thenReturn(adapterManager);
        ;
}
}
```

3.使用demo

```
doThrow(new RuntimeException()).when(mockTryActivityStatusService).dropPromotion(tryActivityStateTransformBO);
donothing()
```

4.*@Before*、 *@BeforeClass*、-- junit4

 *@BeforeEach* 和 *@BeforeAl* --junit5



在JUnit 4中，@BeforeEach注解不存在，可以使用@Before注解来代替，另外需要添加@RunWith和@Mock注解来使用Mockito：

```
📋@RunWith(MockitoJUnitRunner.class) //使用MockitoRunner运行测试
```

5.IEDA使用三方插件 squaretest

* 先用这个生成框架代码![suqaretest](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20230224154632045.png)

* 然后找到生成的测试类，去测试类中使用ALT+M去设置模板或者生成指定方法测试函数

  ![image-20230224154929841](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20230224154929841.png) 