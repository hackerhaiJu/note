# mybatis plus源码解析

mybatis plus的源码以 **3.5.3** 的版本进行阅读

## 1. 配置类

mybatis plus复写了mybatis的 **Configuration** 类，**Configuration** 类将所有的配置信息都进行了保存

```java
public class Configuration {
  //构建的环境对象，里面包含了环境的id和数据源、事务工厂
  protected Environment environment;
  //是否允许在嵌套语句中使用分页，如果允许设置为false
  protected boolean safeRowBoundsEnabled;
  //是否允许在嵌套语句中使用结果处理器
  protected boolean safeResultHandlerEnabled = true;
  //开启下划线转驼峰
  protected boolean mapUnderscoreToCamelCase;
  //任一方法的调用都会加载该对象的所有延迟加载属性
  protected boolean aggressiveLazyLoading;
  //是否允许单个语句返回多结果集（需要数据库驱动支持）
  protected boolean multipleResultSetsEnabled = true;
  //允许 JDBC 支持自动生成主键，需要数据库驱动支持
  protected boolean useGeneratedKeys;
  //使用列标签代替列名
  protected boolean useColumnLabel = true;
  //开启全局性缓存
  protected boolean cacheEnabled = true;
  //指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法
  protected boolean callSettersOnNulls;
  //是否使用真实的参数名称
  protected boolean useActualParamName = true;
  //当返回行的所有列都是空时，MyBatis默认返回 null。 当开启这个设置时，MyBatis会返回一个空实例
  protected boolean returnInstanceForEmptyRow;
  //从SQL中删除多余的空格字符。会影响SQL中的文字字符串
  protected boolean shrinkWhitespacesInSql;
  //日志打印的前缀
  protected String logPrefix;
  //log的实现类
  protected Class<? extends Log> logImpl;
  //vfs实现类
  protected Class<? extends VFS> vfsImpl;
  //默认sql支持类
  protected Class<?> defaultSqlProviderType;
  /**
   * 利用本地缓存机制（Local Cache）防止循环引用和加速重复的嵌套查询
   * SESSION ：会缓存一个会话中执行的所有查询
   * STATEMENT：本地缓存将仅用于执行语句，对相同 SqlSession 的不同查询将不会进行缓存
   */
  protected LocalCacheScope localCacheScope = LocalCacheScope.SESSION;
  //当没有为参数指定特定的 JDBC 类型时，空值的默认 JDBC 类型。 某些数据库驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER
  protected JdbcType jdbcTypeForNull = JdbcType.OTHER;
  //指定对象的哪些方法触发一次延迟加载
  protected Set<String> lazyLoadTriggerMethods = new HashSet<>(Arrays.asList("equals", "clone", "hashCode", "toString"));
  //设置超时时间，它决定数据库驱动等待数据库响应的秒数
  protected Integer defaultStatementTimeout;
  //默认拉取的数量
  protected Integer defaultFetchSize;
  /**
   * 默认的结果集的类型
   * DEFAULT
   * FORWARD_ONLY：只允许结果集的游标向下移动，读取了的记录会自动释放内存
   * SCROLL_INSENSITIVE、SCROLL_SENSITIVE：保证游标可以向上移动到任意位置，如果数据量过大了会出现OOM
   */
  protected ResultSetType defaultResultSetType;
  /**
   * 执行器的类型
   * SIMPLE：SimpleExecutor，每次都会关闭 Statement，每次操作都会开启新的
   * REUSE：ReuseExecutor：不会关闭 Statement，会存放在缓存中进行复用
   * BATCH：BatchExecutor：会重复使用Statement，并且会批量进行更新，事务没法自动提交
   */
  protected ExecutorType defaultExecutorType = ExecutorType.SIMPLE;

  /**
   * 指定 MyBatis 应如何自动映射列到字段或属性
   * NONE：关闭自动映射
   * PARTIAL：只会自动映射没有定义嵌套结果映射的字段
   * FULL：自动映射任何复杂的结果集
   */
  protected AutoMappingBehavior autoMappingBehavior = AutoMappingBehavior.PARTIAL;

  /**
   * 指定发现自动映射目标未知列（或未知属性类型）的行为
   * NONE：什么都不做
   * WARNING：打印告警日志（org.apache.ibatis.session.AutoMappingUnknownColumnBehavior）
   * FAILING：映射失败（SqlSessionException）
   */
  protected AutoMappingUnknownColumnBehavior autoMappingUnknownColumnBehavior = AutoMappingUnknownColumnBehavior.NONE;
  //自定义的配置类
  protected Properties variables = new Properties();
  //创建默认的反射工厂
  protected ReflectorFactory reflectorFactory = new DefaultReflectorFactory();
  //默认的对象工厂用于创建对象
  protected ObjectFactory objectFactory = new DefaultObjectFactory();
  //默认的包装对象的工厂类，默认实现类中并没有进行实现
  protected ObjectWrapperFactory objectWrapperFactory = new DefaultObjectWrapperFactory();
  //延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。
  protected boolean lazyLoadingEnabled = false;
  //代理工厂对象
  protected ProxyFactory proxyFactory = new JavassistProxyFactory(); // #224 Using internal Javassist instead of OGNL
  //数据库的id
  protected String databaseId;
  /**
   * 指定一个提供 Configuration 实例的类，
   * 这个被返回的 Configuration 实例用来加载被反序列化对象的延迟加载属性值。
   * 这个类必须包含一个签名为static Configuration getConfiguration() 的方法
   *
   * @see <a href='https://github.com/mybatis/old-google-code-issues/issues/300'>Issue 300 (google code)</a>
   */
  protected Class<?> configurationFactory;
  //mapper接口的注册工厂
  protected final MapperRegistry mapperRegistry = new MapperRegistry(this);
  //连接器链用于对插件进行代理
  protected final InterceptorChain interceptorChain = new InterceptorChain();
  //类型处理器的注册工厂
  protected final TypeHandlerRegistry typeHandlerRegistry = new TypeHandlerRegistry(this);
  //别名注册工厂
  protected final TypeAliasRegistry typeAliasRegistry = new TypeAliasRegistry();
  //驱动注册工厂
  protected final LanguageDriverRegistry languageRegistry = new LanguageDriverRegistry();
  //保存所有的sql语句解析出来的 MappedStatement 对象
  protected final Map<String, MappedStatement> mappedStatements = new StrictMap<MappedStatement>("Mapped Statements collection")
      .conflictMessageProducer((savedValue, targetValue) ->
          ". please check " + savedValue.getResource() + " and " + targetValue.getResource());
  //保存解析出来的缓存
  protected final Map<String, Cache> caches = new StrictMap<>("Caches collection");
  //保存所有的ResultMap
  protected final Map<String, ResultMap> resultMaps = new StrictMap<>("Result Maps collection");
  //保存参数映射 ParameterMap 标签
  protected final Map<String, ParameterMap> parameterMaps = new StrictMap<>("Parameter Maps collection");
  //保存主键生成的策略
  protected final Map<String, KeyGenerator> keyGenerators = new StrictMap<>("Key Generators collection");
  //保存加载过的资源路径
  protected final Set<String> loadedResources = new HashSet<>();
  //xml解析的sql片段
  protected final Map<String, XNode> sqlFragments = new StrictMap<>("XML fragments parsed from previous mappers");

  //下面都是解析不完整的缓存
  protected final Collection<XMLStatementBuilder> incompleteStatements = new LinkedList<>();
  protected final Collection<CacheRefResolver> incompleteCacheRefs = new LinkedList<>();
  protected final Collection<ResultMapResolver> incompleteResultMaps = new LinkedList<>();
  protected final Collection<MethodResolver> incompleteMethods = new LinkedList<>();

  /*
   * A map holds cache-ref relationship. The key is the namespace that
   * references a cache bound to another namespace and the value is the
   * namespace which the actual cache is bound to.
   */
  protected final Map<String, String> cacheRefMap = new HashMap<>();
  
}
```



**MybatisConfiguration 继承至 Configuration** 新增了一些自己的逻辑注册了一些默认的处理器

```java
public class MybatisConfiguration extends Configuration {
  
  /**
     * Mapper 注册
     */
    protected final MybatisMapperRegistry mybatisMapperRegistry = new MybatisMapperRegistry(this);
    /**
     * 是否生成短key缓存
     */
    @Setter
    @Getter
    private boolean useGeneratedShortKey = true;

    public MybatisConfiguration(Environment environment) {
        this();
        this.environment = environment;
    }
    /**
     * 初始化调用
     */
    public MybatisConfiguration() {
        super();
        this.mapUnderscoreToCamelCase = true;
        typeHandlerRegistry.setDefaultEnumTypeHandler(CompositeEnumTypeHandler.class);
        languageRegistry.setDefaultDriverClass(MybatisXMLLanguageDriver.class);
    }
  
}
```

## 2. 工厂类

### 2.1 SqlSessionFactoryBuilder

用于根据配置文件解析出 **SqlSessionFactory** 核心工厂类

```java
public SqlSessionFactory build(Reader reader, String environment, Properties properties) {
    try {
      //创建XML的解析器
      XMLConfigBuilder parser = new XMLConfigBuilder(reader, environment, properties);
      // parser.parse() 解析出对应的 Configuration对象进行构建
      return build(parser.parse());
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
        reader.close();
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }
```

### 2.2 MybatisSqlSessionFactoryBuilder

mybatis plus定义的工厂构建器将mybatis的 **Builder** 进行了替换，修改成了自己实现的 **MybatisXMLConfigBuilder**，其中的逻辑都是差不多的

```java
public SqlSessionFactory build(Reader reader, String environment, Properties properties) {
        try {
            //TODO 这里换成 MybatisXMLConfigBuilder 而不是 XMLConfigBuilder
            MybatisXMLConfigBuilder parser = new MybatisXMLConfigBuilder(reader, environment, properties);
            return build(parser.parse());
        } catch (Exception e) {
            throw ExceptionFactory.wrapException("Error building SqlSession.", e);
        } finally {
            ErrorContext.instance().reset();
            try {
                reader.close();
            } catch (IOException e) {
                // Intentionally ignore. Prefer previous error.
            }
        }
    }
```

### 2.3 MybatisXMLConfigBuilder

**XMLConfigBuilder** myabtis的核心解析类，用于将xml配置的sql和配置构建为对应的实体类进行处理，mybatis plus进行了增强

```java
private void parseConfiguration(XNode root) {
    try {
      // issue #117 read properties first
      /**
       * 获取到 xml文件中 properties 的地址将下面的子节点 property解析成 Properties对象
       * 将 properties节点中的属性 resource以及url 加载出来，最后统一合并成 Properties（HashTable对象）
       * 最后设置到 Configuration对象中的 variables 属性
       */
      propertiesElement(root.evalNode("properties"));

      /**
       * 加载 settings 节点的配置信息，会检查 Configuration 对象中是否有对应属性的 set 方法
       */
      Properties settings = settingsAsProperties(root.evalNode("settings"));
      /**
       * 获取到 settings 里面的 vfsImpl的实现类进行加载，设置到 Configuration vfsImpl属性
       */
      loadCustomVfs(settings);
      /**
       * 设置自定义的日志实现
       */
      loadCustomLogImpl(settings);
      /**
       * 处理别名标签中子标签 package和typeAlias 前者是获取对应包下所有的子类，后面是单独的某个类的别名；然后根据 @Alias 注解来确定别名
       * 但是要注意两个标签的顺序 typeAlias在前；
       */
      typeAliasesElement(root.evalNode("typeAliases"));
      /**
       * 解析配置文件中的插件 Interceptor 添加到 configuration中
       */
      pluginElement(root.evalNode("plugins"));
      /**
       * 解析 ObjectFactory 对象，用于通过class创建实例化对象
       */
      objectFactoryElement(root.evalNode("objectFactory"));
      /**
       * 解析包装对象工厂，用于后续对结果集进行处理时，创建映射进行包装
       */
      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
      /**
       * 解析反射工厂
       */
      reflectorFactoryElement(root.evalNode("reflectorFactory"));
      /**
       * 将上面解析出来的 settings 配置给 configuration 对象进行赋值
       */
      settingsElement(settings);
      // read it after objectFactory and objectWrapperFactory issue #631
      /**
       * 解析环境，其中包含了 事务工厂以及数据源，以下默认的类型会创建的事务工厂以及数据源
       * JDBC: JdbcTransactionFactory
       * POOLED: PooledDataSourceFactory
       * 构建环境对象 Environment设置 事务工厂以及数据源
       */
      environmentsElement(root.evalNode("environments"));
      /**
       * 设置数据源的提供对象，主要用于设置 数据库Id到 configuration中
       */
      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
      /**
       * 处理类型处理器，通过注解 MappedTypes 来标记java类型的处理，根据 MappedJdbcTypes 来对应jdbc的类型，然后关联上处理器
       * 最后存储的类型是 Map<JavaType, Map<JdbcType, Handler>> 首先通过java中的类型进行获取到对应的jdbc类型然后获取到对应的Handler
       */
      typeHandlerElement(root.evalNode("typeHandlers"));
      //解析mappers的文件
      mapperElement(root.evalNode("mappers"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
    }
  }
```

**解析Mapper接口信息**

```java
private void mapperElement(XNode parent) throws Exception {
    if (parent != null) {
      for (XNode child : parent.getChildren()) {
        //解析 package 标签
        if ("package".equals(child.getName())) {
          //解析 package 包路径下面的mapper接口
          String mapperPackage = child.getStringAttribute("name");
          configuration.addMappers(mapperPackage);
        } else {
          /**
           * 解析 mapper 标签
           * 处理：
           * 1. resource != null 并且 url和class 为空：
           *    解析 xml配置文件，解析mapper.xml中 namespace来设置命名空间，然后解析后续的节点
           *    解析完 xml配置后，会进行 mapper的解析，mapper对应的就是接口
           * 2. resource == null 和 class == null 并且 url != null
           *    按照url的io流进行解析
           * 3. resource == null 和 class != null 并且 url == null
           *    解析某个具体的mapper
           */
          String resource = child.getStringAttribute("resource");
          String url = child.getStringAttribute("url");
          String mapperClass = child.getStringAttribute("class");
          if (resource != null && url == null && mapperClass == null) {
            ErrorContext.instance().resource(resource);
            InputStream inputStream = Resources.getResourceAsStream(resource);
            //其中 MapperBuilderAssistant 属性比较重要用于关联缓存
            XMLMapperBuilder mapperParser = new XMLMapperBuilder(inputStream, configuration, resource, configuration.getSqlFragments());
            mapperParser.parse();
          } else if (resource == null && url != null && mapperClass == null) {
            ErrorContext.instance().resource(url);
            //根据url获取到数据流进行解析
            InputStream inputStream = Resources.getUrlAsStream(url);
            XMLMapperBuilder mapperParser = new XMLMapperBuilder(inputStream, configuration, url, configuration.getSqlFragments());
            mapperParser.parse();
          } else if (resource == null && url == null && mapperClass != null) {
            //解析 mapperClass 类型
            Class<?> mapperInterface = Resources.classForName(mapperClass);
            configuration.addMapper(mapperInterface);
          } else {
            throw new BuilderException("A mapper element may only specify a url, resource or class, but not more than one.");
          }
        }
      }
    }
  }
```



### 2.4 XMLMapperBuilder

用于解析 **xml** 文件，并且将对应的sql解析成实体类。在构建时会创建一下核心的类

- MapperBuilderAssistant：xml文件的构建助手
  - Cache：根据配置构建缓存
  - ParameterMap：映射xml配置中的 ParameterMap标签
  - ParameterMapping：根据xml每一行参数构建为参数映射对象
  - ResultMap：用于解析xml resultMap标签，下面会保存多个 ResultMapping
  - ResultMapping：更具xml resultMap中定义的结果集映射构建对应的实体
  - MappedStatement：将xml中对应的 select等语句解析的映射实体

```java
private void configurationElement(XNode context) {
    try {
      String namespace = context.getStringAttribute("namespace");
      if (namespace == null || namespace.isEmpty()) {
        throw new BuilderException("Mapper's namespace cannot be empty");
      }
      //设置上命名空间，mapper的完全限定名
      builderAssistant.setCurrentNamespace(namespace);
      /**
       * 解析缓存引用，解析成 CacheRefResolver，如果解析失败了 存入 configuration中
       * 通过 MapperBuilderAssistant 去从 configuration 中设置当前 Cache 对象用于本地缓存
       * 如果没有获取 Cache的话就会抛出异常，存放到 configuration中的 incompleteCacheRefs 集合中
       */
      cacheRefElement(context.evalNode("cache-ref"));
      /**
       * 解析缓存节点
       * 根据配置需要设置缓存中采用什么类型的进行缓存，以及设置淘汰策略，刷新间隔实践，是否只读，是否阻塞
       * 然后通过 MapperBuilderAssistant.useNewCache() 根据当前命名空间创建一个缓存
       */
      cacheElement(context.evalNode("cache"));
      /**
       * 解析 parameterMap 标签
       * 可以用于跟 result 标签进行配和使用，每个属性都可以关联一个resultMap，解析成 ParameterMapping
       */
      parameterMapElement(context.evalNodes("/mapper/parameterMap"));
      /**
       * 解析xml文件中的 resultMap
       */
      resultMapElements(context.evalNodes("/mapper/resultMap"));
      //解析sql标签
      sqlElement(context.evalNodes("/mapper/sql"));
      //解析sql语句的标签
      buildStatementFromContext(context.evalNodes("select|insert|update|delete"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing Mapper XML. The XML location is '" + resource + "'. Cause: " + e, e);
    }
  }
```

#### 解析ResultMap

```java
private ResultMap resultMapElement(XNode resultMapNode, List<ResultMapping> additionalResultMappings, Class<?> enclosingType) {
    ErrorContext.instance().activity("processing " + resultMapNode.getValueBasedIdentifier());
    String type = resultMapNode.getStringAttribute("type",
        resultMapNode.getStringAttribute("ofType",
            resultMapNode.getStringAttribute("resultType",
                resultMapNode.getStringAttribute("javaType"))));
    //解析resultMap返回的type
    Class<?> typeClass = resolveClass(type);
    if (typeClass == null) {
      typeClass = inheritEnclosingType(resultMapNode, enclosingType);
    }
    Discriminator discriminator = null;
    List<ResultMapping> resultMappings = new ArrayList<>(additionalResultMappings);
    //获取到下面的子标签
    List<XNode> resultChildren = resultMapNode.getChildren();
    /**
     * 遍历resultMap下面的节点
     * 1. 先判断是否是 constructor 节点，解析构造函数
     * 2. discriminator 节点，解析鉴别器
     * 3. 其他节点，解析普通的字段映射
     * 4. 如果节点是id节点，那么就添加到flags中，表示是主键
     */
    for (XNode resultChild : resultChildren) {
      if ("constructor".equals(resultChild.getName())) {
        processConstructorElement(resultChild, typeClass, resultMappings);
      } else if ("discriminator".equals(resultChild.getName())) {
        discriminator = processDiscriminatorElement(resultChild, typeClass, resultMappings);
      } else {
        List<ResultFlag> flags = new ArrayList<>();
        if ("id".equals(resultChild.getName())) {
          flags.add(ResultFlag.ID);
        }
        //构建 ResultMapping 对象，每个 ResultMap有多个字段映射对象
        resultMappings.add(buildResultMappingFromContext(resultChild, typeClass, flags));
      }
    }
    String id = resultMapNode.getStringAttribute("id",
            resultMapNode.getValueBasedIdentifier());
    //解析是否继承某个ResultMap
    String extend = resultMapNode.getStringAttribute("extends");
    Boolean autoMapping = resultMapNode.getBooleanAttribute("autoMapping");
    ResultMapResolver resultMapResolver = new ResultMapResolver(builderAssistant, id, typeClass, extend, discriminator, resultMappings, autoMapping);
    try {
      //将resultMap添加到配置文件对象中
      return resultMapResolver.resolve();
    } catch (IncompleteElementException e) {
      configuration.addIncompleteResultMap(resultMapResolver);
      throw e;
    }
  }
```

解析 **ResultMap** 主要比较重要的点是对 **typeHandler** 的解析，xml解析的时候会将对应的数据类型注册到 **typeHandlerRegistry** 中

```java
protected TypeHandler<?> resolveTypeHandler(Class<?> javaType, Class<? extends TypeHandler<?>> typeHandlerType) {
    if (typeHandlerType == null) {
      return null;
    }
    //获取到TypeHandler，是通过 allTypeHandlersMap 这个map获取的，因为这里面是保存的所有类型处理器，不需要通过jdbc或者javaType进行获取
    TypeHandler<?> handler = typeHandlerRegistry.getMappingTypeHandler(typeHandlerType);
    if (handler == null) {
      // 通过java的类型去获取到jdbc的处理器
      handler = typeHandlerRegistry.getInstance(javaType, typeHandlerType);
    }
    return handler;
  }
```



#### 解析SQL

```java
public void parseStatementNode() {
    //解析当前sql的id
    String id = context.getStringAttribute("id");
    //解析当前数据的数据库id，可以配置多数据库
    String databaseId = context.getStringAttribute("databaseId");

    if (!databaseIdMatchesCurrent(id, databaseId, this.requiredDatabaseId)) {
      return;
    }
    //获取到节点标签的名称
    String nodeName = context.getNode().getNodeName();
    //将标签名称转换为枚举类型，判断是 select还是什么类型
    SqlCommandType sqlCommandType = SqlCommandType.valueOf(nodeName.toUpperCase(Locale.ENGLISH));
    //是否是查找类型
    boolean isSelect = sqlCommandType == SqlCommandType.SELECT;
    //是否需要刷新缓存
    boolean flushCache = context.getBooleanAttribute("flushCache", !isSelect);
    //是否需要使用缓存
    boolean useCache = context.getBooleanAttribute("useCache", isSelect);
    boolean resultOrdered = context.getBooleanAttribute("resultOrdered", false);

    // Include Fragments before parsing
    XMLIncludeTransformer includeParser = new XMLIncludeTransformer(configuration, builderAssistant);
    includeParser.applyIncludes(context.getNode());

    //解析参数的类型
    String parameterType = context.getStringAttribute("parameterType");
    //解析出class对象
    Class<?> parameterTypeClass = resolveClass(parameterType);
    //解析出语言驱动
    String lang = context.getStringAttribute("lang");
    //通过语言驱动器，用xml或者string字符串创建出SqlSource对象用于保存sql语句
    LanguageDriver langDriver = getLanguageDriver(lang);

    // Parse selectKey after includes and remove them.
    //解析下面 selectKey 节点
    processSelectKeyNodes(id, parameterTypeClass, langDriver);

    // Parse the SQL (pre: <selectKey> and <include> were parsed and removed)
    //key生成器
    KeyGenerator keyGenerator;
    String keyStatementId = id + SelectKeyGenerator.SELECT_KEY_SUFFIX;
    keyStatementId = builderAssistant.applyCurrentNamespace(keyStatementId, true);
    if (configuration.hasKeyGenerator(keyStatementId)) {
      keyGenerator = configuration.getKeyGenerator(keyStatementId);
    } else {
      keyGenerator = context.getBooleanAttribute("useGeneratedKeys",
          configuration.isUseGeneratedKeys() && SqlCommandType.INSERT.equals(sqlCommandType))
          ? Jdbc3KeyGenerator.INSTANCE : NoKeyGenerator.INSTANCE;
    }
    /**
     * 创建 SqlSource对象其中会判断 sql语句的类型根据 ${} 是动态sql还是 预处理sql
     * 调用：XMLLanguageDriver类
     * 如果是 #{} 节点会添加 StaticTextSqlNode类型
     * 如果是 ${} 节点会添加 TextSqlNode类型
     * 最终返回的是一个 MixedSqlNode 对象其中包含了一个 List<SqlNode>
     *
     * 如果是 ${} : SqlSource 类型为：DynamicSqlSource，
     * 如果是 #{} : RawSqlSource，其中内部包含了一个 StaticSqlSource类型的 SqlSource
     *
     * 实际：${}采用 DynamicSqlSource 处理，#{} 采用 StaticSqlSource进行处理
     */
    SqlSource sqlSource = langDriver.createSqlSource(configuration, context, parameterTypeClass);
    //解析采用什么 StatementType 进行后续的sql处理
    StatementType statementType = StatementType.valueOf(context.getStringAttribute("statementType", StatementType.PREPARED.toString()));
    //每次拉取的条数
    Integer fetchSize = context.getIntAttribute("fetchSize");
    //超时时间
    Integer timeout = context.getIntAttribute("timeout");
    //解析使用到的 parameterMap 名称
    String parameterMap = context.getStringAttribute("parameterMap");
    //解析resultType的名称
    String resultType = context.getStringAttribute("resultType");
    Class<?> resultTypeClass = resolveClass(resultType);
    //解析使用到的resultMap名称
    String resultMap = context.getStringAttribute("resultMap");
    String resultSetType = context.getStringAttribute("resultSetType");
    ResultSetType resultSetTypeEnum = resolveResultSetType(resultSetType);
    if (resultSetTypeEnum == null) {
      resultSetTypeEnum = configuration.getDefaultResultSetType();
    }
    String keyProperty = context.getStringAttribute("keyProperty");
    String keyColumn = context.getStringAttribute("keyColumn");
    String resultSets = context.getStringAttribute("resultSets");
    //构建 MapperBuilderAssistant
    builderAssistant.addMappedStatement(id, sqlSource, statementType, sqlCommandType,
        fetchSize, timeout, parameterMap, parameterTypeClass, resultMap, resultTypeClass,
        resultSetTypeEnum, flushCache, useCache, resultOrdered,
        keyGenerator, keyProperty, keyColumn, databaseId, langDriver, resultSets);
  }
```



## 3. MapperBuilderAssistant

mapper构建的助手，所有的配置以及sql在解析的时候都会通过 **MapperBuilderAssistant** 构建到 **Configuration** 类当中

### 构建缓存

根据配置中指定的缓存类

- PerpetualCache：永久缓存
- LruCache：lru算法换成

```java
public Cache useNewCache(Class<? extends Cache> typeClass,
      Class<? extends Cache> evictionClass,
      Long flushInterval,
      Integer size,
      boolean readWrite,
      boolean blocking,
      Properties props) {
    Cache cache = new CacheBuilder(currentNamespace)
        .implementation(valueOrDefault(typeClass, PerpetualCache.class))
        .addDecorator(valueOrDefault(evictionClass, LruCache.class))
        .clearInterval(flushInterval)
        .size(size)
        .readWrite(readWrite)
        .blocking(blocking)
        .properties(props)
        .build();
    configuration.addCache(cache);
    currentCache = cache;
    return cache;
  }
```



### 构建ParameterMap

构建 **ParameterMap** 将其添加到 **configuration** 当中，其中包括了多个 **ParameterMapping**

```java
public ParameterMap addParameterMap(String id, Class<?> parameterClass, List<ParameterMapping> parameterMappings) {
    id = applyCurrentNamespace(id, false);
    ParameterMap parameterMap = new ParameterMap.Builder(configuration, id, parameterClass, parameterMappings).build();
    configuration.addParameterMap(parameterMap);
    return parameterMap;
  }
```

构建 **ParameterMapping** 其中会指定参数的类型处理器以及jdbc的类型等，在执行sql的时候会进行调用处理

```java
public ParameterMapping buildParameterMapping(
      Class<?> parameterType,
      String property,
      Class<?> javaType,
      JdbcType jdbcType,
      String resultMap,
      ParameterMode parameterMode,
      Class<? extends TypeHandler<?>> typeHandler,
      Integer numericScale) {
    resultMap = applyCurrentNamespace(resultMap, true);

    // Class parameterType = parameterMapBuilder.type();
    Class<?> javaTypeClass = resolveParameterJavaType(parameterType, property, javaType, jdbcType);
    TypeHandler<?> typeHandlerInstance = resolveTypeHandler(javaTypeClass, typeHandler);

    return new ParameterMapping.Builder(configuration, property, javaTypeClass)
        .jdbcType(jdbcType)
        .resultMapId(resultMap)
        .mode(parameterMode)
        .numericScale(numericScale)
        .typeHandler(typeHandlerInstance)
        .build();
  }
```

### 构建ReusltMap

```java
public ResultMap addResultMap(
      String id,
      Class<?> type,
      String extend,
      Discriminator discriminator,
      List<ResultMapping> resultMappings,
      Boolean autoMapping) {
    //获取到当前的命名空间
    id = applyCurrentNamespace(id, false);
    extend = applyCurrentNamespace(extend, true);
    //是否有继承的resultMap
    if (extend != null) {
      if (!configuration.hasResultMap(extend)) {
        throw new IncompleteElementException("Could not find a parent resultmap with id '" + extend + "'");
      }
      //根据标签是否需要继承某一个resultMap，如果需要继承，则将父类的resultMap的resultMappings添加到当前的resultMap中
      ResultMap resultMap = configuration.getResultMap(extend);
      //获取到父类的resultMap的resultMappings
      List<ResultMapping> extendedResultMappings = new ArrayList<>(resultMap.getResultMappings());
      //删除相同的数据
      extendedResultMappings.removeAll(resultMappings);
      // Remove parent constructor if this resultMap declares a constructor.
      boolean declaresConstructor = false;
      for (ResultMapping resultMapping : resultMappings) {
        if (resultMapping.getFlags().contains(ResultFlag.CONSTRUCTOR)) {
          declaresConstructor = true;
          break;
        }
      }
      if (declaresConstructor) {
        extendedResultMappings.removeIf(resultMapping -> resultMapping.getFlags().contains(ResultFlag.CONSTRUCTOR));
      }
      resultMappings.addAll(extendedResultMappings);
    }
    
    //直接构建，然后保存到configuration
    ResultMap resultMap = new ResultMap.Builder(configuration, id, type, resultMappings, autoMapping)
        .discriminator(discriminator)
        .build();
    configuration.addResultMap(resultMap);
    return resultMap;
  }
```

```java
/**
   * Build result mapping
   *
   * @param resultType      result的类型
   * @param property        属性名称
   * @param column          列名
   * @param javaType        java类型
   * @param jdbcType        jdbc类型
   * @param nestedSelect    内嵌的select语句
   * @param nestedResultMap 内嵌的resultMap
   * @param notNullColumn   not null column
   * @param columnPrefix    列的前缀名称
   * @param typeHandler     对应的TypeHandler
   * @param flags           标识当前配置的列是id还是constructor
   * @param resultSet       result set
   * @param foreignColumn   外键的列名
   * @param lazy            是否懒加载
   * @return the result mapping
   * @since 3.5.6
   */
  public ResultMapping buildResultMapping(
      Class<?> resultType,
      String property,
      String column,
      Class<?> javaType,
      JdbcType jdbcType,
      String nestedSelect,
      String nestedResultMap,
      String notNullColumn,
      String columnPrefix,
      Class<? extends TypeHandler<?>> typeHandler,
      List<ResultFlag> flags,
      String resultSet,
      String foreignColumn,
      boolean lazy) {
    //解析java的类型
    Class<?> javaTypeClass = resolveResultJavaType(resultType, property, javaType);
    //根据resultMap字段指定的typeHandler的类型，获取对应的TypeHandler实例
    TypeHandler<?> typeHandlerInstance = resolveTypeHandler(javaTypeClass, typeHandler);
    List<ResultMapping> composites;
    //判断是否有内嵌的查询语句，如果有的话，就需要解析出来，判断外键值的名称是否为空
    if ((nestedSelect == null || nestedSelect.isEmpty()) && (foreignColumn == null || foreignColumn.isEmpty())) {
      composites = Collections.emptyList();
    } else {
      composites = parseCompositeColumnName(column);
    }
    return new ResultMapping.Builder(configuration, property, column, javaTypeClass)
        .jdbcType(jdbcType)
        .nestedQueryId(applyCurrentNamespace(nestedSelect, true))
        .nestedResultMapId(applyCurrentNamespace(nestedResultMap, true))
        .resultSet(resultSet)
        .typeHandler(typeHandlerInstance)
        .flags(flags == null ? new ArrayList<>() : flags)
        .composites(composites)
        .notNullColumns(parseMultipleColumnNames(notNullColumn))
        .columnPrefix(columnPrefix)
        .foreignColumn(foreignColumn)
        .lazy(lazy)
        .build();
  }
```

### 构建MappedStatement

将sql语句进行构建成对应的实体

```java
/**
   * Add mapped statement
   *
   * @param id             sql id
   * @param sqlSource      sql解析时的类
   * @param statementType  预构件的类型，默认 PREPARED
   * @param sqlCommandType sql的命令类型
   * @param fetchSize      fetch size
   * @param timeout        超时时间
   * @param parameterMap   参数的map id
   * @param parameterType  参数的类型
   * @param resultMap      结果的map id
   * @param resultType     结果类型
   * @param resultSetType  结果集类型
   * @param flushCache     是否刷新换成
   * @param useCache       是否使用换成
   * @param resultOrdered  result ordered
   * @param keyGenerator   key生成器
   * @param keyProperty    key属性名称
   * @param keyColumn      key列的名称
   * @param databaseId     database id
   * @param lang           驱动
   * @param resultSets     result sets
   * @return the mapped statement
   * @since 3.5.6
   */
  public MappedStatement addMappedStatement(
      String id,
      SqlSource sqlSource,
      StatementType statementType,
      SqlCommandType sqlCommandType,
      Integer fetchSize,
      Integer timeout,
      String parameterMap,
      Class<?> parameterType,
      String resultMap,
      Class<?> resultType,
      ResultSetType resultSetType,
      boolean flushCache,
      boolean useCache,
      boolean resultOrdered,
      KeyGenerator keyGenerator,
      String keyProperty,
      String keyColumn,
      String databaseId,
      LanguageDriver lang,
      String resultSets) {

    if (unresolvedCacheRef) {
      throw new IncompleteElementException("Cache-ref not yet resolved");
    }

    id = applyCurrentNamespace(id, false);
    boolean isSelect = sqlCommandType == SqlCommandType.SELECT;

    MappedStatement.Builder statementBuilder = new MappedStatement.Builder(configuration, id, sqlSource, sqlCommandType)
        .resource(resource)
        .fetchSize(fetchSize)
        .timeout(timeout)
        .statementType(statementType)
        .keyGenerator(keyGenerator)
        .keyProperty(keyProperty)
        .keyColumn(keyColumn)
        .databaseId(databaseId)
        .lang(lang)
        .resultOrdered(resultOrdered)
        .resultSets(resultSets)
        .resultMaps(getStatementResultMaps(resultMap, resultType, id))
        .resultSetType(resultSetType)
        .flushCacheRequired(valueOrDefault(flushCache, !isSelect))
        .useCache(valueOrDefault(useCache, isSelect))
        .cache(currentCache);
    //需要的参数类型去构建ParameterMap，注意这里创建的 List<ParameterMapping> 是一个无法修改的集合，真正执行的时候是重新进行构建的ArrayList
    ParameterMap statementParameterMap = getStatementParameterMap(parameterMap, parameterType, id);
    if (statementParameterMap != null) {
      statementBuilder.parameterMap(statementParameterMap);
    }

    MappedStatement statement = statementBuilder.build();
    configuration.addMappedStatement(statement);
    return statement;
  }
```

## 4. 