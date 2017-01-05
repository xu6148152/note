# Dagger2

主要是``@Module``和``@Component``的概念

### Module
实体类``@Module``，可以认为是一个单独的模块,其本身是向别人提供依赖，需要向别人提供的依赖的方法都需要以``provide``开头,并且加以``@Provides``注解

### Component
接口或抽象类，可以认为是某个具体的模块，它需要依赖于别的模块``@Component(dependencies = xxxComponent.class, modules = xxxModule.class)``，编译时，会自动生成``Provider<方法名>``的对象,这些对象是由根据``@Inject``的构造方法和``@Module``中的``@Provides``自动生成的抽象工厂``xxx_Factory``来创建的。

### Inject
如果类中用``@Inject``修饰了变量，那么会自动生成一个对应该类的``xxx_MembersInjector``的类,用于创建相应的对象。

#### 流程
在需要``Component``的地方声明了其需要的``module``和``component``，然后编译生成``Component``实例，其内部的变量``Provider``是依赖模块中声明了``@Provides``的方法，这些方法在对应的``Module``中，会自动生成抽象工厂。也就是将方法名生成了对应的类实例(或反之，提供与实例类对应的方法名)，这样``Component``就实例化完成，其内部包含了所有依赖的实例对象。``@Inject``会生成对应的``MembersInjector``类，并用对应的``@Inject``的构造函数生成``Provider``对象，并在其``Component``内部进行实例化。

### 自定义注解
``AbstractProcessor``  
将限定名写入资源文件

