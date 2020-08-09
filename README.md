# NPE_avoid
空指针异常NullpointException简称NPE在开发中是最常见的bug，在这里探求可以在开发阶段就可以发现，或是避免的方法。
## java8自带的Optional
	//这里很容易NPE
	String version = computer.getSoundcard().getUSB().getVersion();
	//这么写很非空判断很多
	String version = "UNKNOWN";
	if(computer != null)
    	{
        	Soundcard soundcard = computer.getSoundcard();
        	if(soundcard != null){
            USB usb = soundcard.getUSB();
            if(usb != null){
                 version = usb.getVersion();
                }
            }
    }
	//采用Optional模式
	String name = computer.flatMap(Computer::getSoundcard)
                          .flatMap(Soundcard::getUSB)
                          .map(USB::getVersion)
                          .orElse("UNKNOWN");
                          
java8的Optional的使用需要改写原来的代码，有一定的学习成本，具体详情的连接[https://juejin.im/post/5a570846518825734e3e2bb7](https://juejin.im/post/5a570846518825734e3e2bb7)

## 可用的静态代码扫描

### FindBugs 
FindBugs一个静态分析工具，它检查类或者JAR 文件，将字节码与一组缺陷模式进行对比以发现可能的问题。对一些Android特性的内容无法检查，如AndroidManifest文件。

Findbugs自带检测器，其中有60余种Bad practice，80余种Correctness，1种 Internationalization，12种Malicious code vulnerability，27种Multithreaded correctness，23种Performance，43种Dodgy。

但是2015年后就没有继续维护了，直接在玩客云项目中跑，会卡在databingding生成的代码中，要配置过滤文件，需要一定的学习成本
### PMD
PMD 是一款采用BSD协议发布的Java程序代码检查工具，可以做到检查Java代码中是否含有未使用的变量、是否含有空的抓取块、是否含有不必要的对象等。安装后可以扫描生成.html，或是.xml。方便查看有问题代码


### 360 FileLine
360 FireLine 火线，是360旗下的一款免费代码扫描分析产品，目前火线扫描规则共覆盖六大类，约61个检查规则：1.APP安全检查，2.代码规范检查3，内存泄露检查,4.日志输出检查5，空指针检查,6，多线程检查

使用起来方便，可以在Android Studio中展示结果，比较方便。

结合最近发生的NPE,静态代码扫描工具能做的非常有限，还是需要大家养成良好的代码习惯。
## 代码避免

### String对象的相等判断，判空，打印
	BigDecimal bd = getPrice();
	System.out.println(String.valueOf(bd)); //doesn't throw NPE
	System.out.println(bd.toString()); //throws "Exception in thread "main" 	java.lang.NullPointerException"
  
	TextUtils.isEmpty(str)
  
	TextUtils.equals(p, path)
	
### Collection
Collection类能够提供方便的空的List，Set和Map,(这些)有Collections.EMPTYLIST, Collections.EMPTYSET和 Collections.EMPTY_MAP这些能够被使用的（静态变量）。

	public List getOrders(Customer customer){
   	List result = Collections.EMPTY_LIST;
    	return result;
	} 
 
	//支持泛型的list
	public List<User> getUsers(int age){
   	List<User> result = Collections.EMPTY_LIST;
    	return result;
	}
	
### @NonNull和@Nullable
通过使用像@NonNull和@Nullable类似的注释提示这个方法是否为空安全（null safe）。IDE和其他工具可以读出这个注释来帮你做一个空检查或者告诉你是否需要空检查。

	@Nullable
	String ss;
 
	@NonNull
	IAdUtils adUtils;

 
 
  
