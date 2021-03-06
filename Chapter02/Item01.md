## Item 01 : 생성자 대신 static factory method를 사용할 수 없는지 생각해 보라
(Consider static factory methods instead of constructors)


**정적 팩토리 메서드 (static factory method) 장단점**

---
## First Adventage
#### 생성자와는 달리, static factory method들은 이름을 붙일 수 있다.
#### (unlike constructors,static factory methods have names.)

```java
public class Member {

	private String memberName;
	private String memberPhone;

	//member1 생성자
	public Member(String memberName, String memberPhone) {
		this.memberName = memberName;
		this.memberPhone = memberPhone;
	}

	//member2 생성자
	public Member(String memberName) {
		this.memberName = memberName;
	}

}
```

```java
	Member member1 = new Member("junoh","01098765432");
	Member member2 = new Member("sooduck");
```

member1의 생성자는 이름과, 휴대폰번호를 초기화시켜주고  

member2의 생성자는 이름만 초기화 시켜준다.

member1과 member2의 생성자 이름은 Member로 이름이 같다.


```java
public class sfmMember {

	private String memberName;
	private String memberPhone;

	//member1의 static factory method
	public static sfmMember new_Name_Phone(String memberName, String memberPhone) {
		return new sfmMember(memberName, memberPhone);
	}

	//member2의 static factory method
	public static sfmMember new_Name(String memberName) {
		return new sfmMember(memberName);
	}

	private sfmMember(String memberName, String memberPhone) {
		this.memberName = memberName;
		this.memberPhone = memberPhone;
	}

	private sfmMember(String memberName) {
		this.memberName = memberName;
	}

}
```


```java
	sfmMember sfmmember1 = sfmMember.new_Name_Phone("junoh" , "01098765432");
	sfmMember sfmmember2 = sfmMember.new_Name("sooduck");
```
이번에는 객체 생성시 생성자를사용하지 않고 이름있는 메소드를 이용하였다.  

인자가 어떤 역할인지 알 수 있다.

적절한 네이밍으로 가독성을 높여주며 실수를 줄여준다.  


## Second Advantage

#### 생성자와는 달리, static factory method들은 호출될때 새 객체를 생성하지 않는다.
#### (unlike constructors, static factory methods are not required to create a new object each time they’re invoked.)

생성자는 매번 new Instance를 만든다.  

static factory method에서는 객체생성이 필수가 아니다.  

아래의 메소드는 불필요한 객체생성을 피할 수 있다.  


java.lang.Boolean.class 일부
```java
    public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
    }
```



**static factory method in Singleton Pattern**
```java
public class Singleton{
    private static final Singleton INSTANCE = new Singleton();

    private Singleton(){}

    //static factory method
    public static Singleton getSingleton(){
        return INSTANCE;
    }
}
```



**static factory method in DBConnection**
```java
public class DbConnection{
   private static final int MAX_CONNS = 100;
   private static int totalConnections = 0;

   private static Set<DbConnection> availableConnections = new HashSet<DbConnection>();

   private DbConnection(){
     // ...
     totalConnections++;
   }

   //static factory method
   public static DbConnection getDbConnection(){

      if(totalConnections < MAX_CONNS){
        return new DbConnection();

      }else if(availableConnections.size() > 0){
          DbConnection dbc = availableConnections.iterator().next();
          availableConnections.remove(dbc);
          return dbc;

      }else {
          throw new NoDbConnections();
      }
   }

   public static void returnDbConnection(DbConnection dbc){
     availableConnections.add(dbc);
     //...
   }

}
```


## Third Advantage

#### 생성자와는 달리, static factory method는 return값 자료형의 하위 자료형 객체를 반환할 수 있다.
#### (unlike constructors, static factory method can return an object of any subtype of their return type.)

java.util.EnumSet 일부
```java
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
        Enum<?>[] universe = getUniverse(elementType);
        if (universe == null)
            throw new ClassCastException(elementType + " not an enum");

        if (universe.length <= 64)
            return new RegularEnumSet<>(elementType, universe);
        else
            return new JumboEnumSet<>(elementType, universe);
    }
```
enum의 elements 갯수가  

64개 이하인 경우 RegularEnumSet을 반환하고  

65개 이상인 경우 JumboEnumSet을 반환하다.  

4번째 장점도 있는데, jdk1.7부터 다이아몬드 연산자 나와서 Pass!

## Disadvantage
#### static factory method와 다른 static method의 구분이 쉽지 않다.
#### (static factory methods is that they are not readily distinguishable from other static methods.)

static factory method는 다른 static method와 구분이 어렵다.  

주석을 통해 static factory method 임을 알리거나,

네이밍을 잘 하여 static method와 헷갈리지 않도록 해야 한다.

static factory method의 이름으로는 다음과 같은것들을 사용한다.
```java
//valueOf : type-conversion
String s = String.valueOf((int) i);

//of : more simple than valueOf
enum Fruit {bas, asd , qwe}
Set<Fruit> fruits = EnumSet.of(Fruit.bas , Fruit.asd);

//getInstance :
Singleton singleton = Singleton.getInstance();

//newInstance :
Class c=Class.forName("Main");  
Main s=(Simple)c.newInstance();

//getType :
... 모르겠다

//newType :
Channels.newChannel(in);
```
