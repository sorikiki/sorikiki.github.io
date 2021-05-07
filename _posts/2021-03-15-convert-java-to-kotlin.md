---
layout: post
title: '자바에서 코틀린으로'
author: 'Dasol Kim'
tags: Tale
---

<p> 필자는 이미 코틀린을 공부하고 안드로이드 프로젝트에서 다양한 문법을 적용해보는 연습을 거쳤지만 '여기 이 부분에서 왜 이렇게 써도 되는지' 에 대한 의문을 해결하기엔 조금 부족했다. 그래서 이번 기회에 또 다른 주된 언어인 자바와 함께, 객체 지향 프로그래밍에 대한 개념을 확립하고 연장선상으로 코틀린에는 어떻게 적용되는지 공부하였다. 따라서, 오늘 포스팅에서는 자바의 클래스 파일을 코틀린 언어의 관습을 적용시킨 형태로 변환하여, 코틀린을 이전보다 심도있게 이해하는 것을 목표로 한다. 본론에 들어가기 앞서 필자가 글을 작성하기 위해 활용한 주된 레퍼런스를 첨부한다.</p>
<a href="https://developer.android.com/codelabs/java-to-kotlin?hl=ko&continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fkotlin-for-java%3Fhl%3Dko%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fjava-to-kotlin#0">Converting Java to Kotlin</a>
<br>
<br>

<h1>✅ null 처리, val/var의 쓰임, data class의 용도</h1>
<h2>User.java</h2>
{% highlight ruby linenos=table %}
import org.jetbrains.annotations.Nullable;

public class User {

    @Nullable
    private String firstName;
    @Nullable
    private String lastName;

    public User(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
}

{% endhighlight %}

<p>위의 Users라는 클래스 내부를 보면, 클래스명과 동일한 생성자 함수가 있고 firstName과 lastName이라는 필드, 그리고 이들 필드 각각에 대응하는 get과 set함수를 확인할 수 있다. </p>

<h2>User.kt</h2>
{% highlight ruby linenos=table %}
class User(var firstName: String?, var lastName: String?)
{% endhighlight %}

<p>IDE에서 자바 코드를 코틀린으로 변환해주었더니, 코드가 눈에 띄게 간략화되었다. <strong>자바와는 달리, 코틀린 클래스 내부에서 모든 변수 선언은 '프로퍼티'로서 이루어진다. </strong>프로퍼티는 실제 값이 메모리에 저장되는 실체인 필드보다는 넓은 개념으로, 보통 필드에 접근자(get/set)를 포함한 개념을 프로퍼티라고 일컫는다. 자바에서는 private한 필드에 접근하기 위해 public 메소드인 get과 set을 정의해주었는데, 코틀린에서는 생성자에 var이 붙은 프로퍼티 선언만으로 이 모든 것이 해결된다. firstName과 lastName은 암묵적으로 default get과 set 메소드를 가지고 있어, 변수에 접근하거나 변수에 직접 대입연산자로 값을 할당해주는 것만으로도 이 둘 메소드를 호출해준다. 여기서 'default'라고 적은 이유는 각각의 프로퍼티에 'custom' getter와 setter을 부여해줄 수도 있기 때문이다. </p>

<p><strong>정리하면</strong>, 자바에서 getter 메소드를 가진 필드는 코틀린에서 val 프로퍼티로 정의되며 getter와 setter 메소드를 가진 필드는 var 프로퍼티로 정의된다.</p>
<p><strong>또한</strong>, 자바에서 변수가 null일 수 있음을 표시하기 위해 @Nullable이라는 주석을 변수 위에 붙여줬는데, 코틀린에서는 데이터 타입 뒤에 ?을 붙여주면 된다. 이렇게 null-safety를 지키는 이유는 클래스를 인스턴스화할 때 사용자 마음대로 어떠한 프로퍼티에 값을 지정해주지 않는 상황이 발생할 수 있기 때문이다.</p>
<p>코틀린에서는 이렇게 별도의 기능을 구현하는 함수없이 데이터만 보유한 클래스 앞에 관습적으로 'data' 키워드를 붙여주는게 좋다. </p>

{% highlight ruby linenos=table %}
data class User(var firstName: String?, var lastName: String?)
{% endhighlight %}

<p>기존에 존재하는 User 클래스 앞에 'data'를 붙여준 결과이다. 이렇게 하는 것이 좋은 이유는 데이터 클래스명 바로 뒤에 위치한 주생성자에서 사용된 프로퍼티에게 특혜를 주기 때문이다. 각각의 프로퍼티별로, 특정한 기능을 담당하는 메소드(equals(), hashCode(), toString(), copy())에 접근할 수 있으며 구조분해 선언(destructuring declarations)이 가능하다. 자세한 내용은 아래 코틀린 공식 문서에 잘 설명되어 있다.</p>
<a href="https://kotlinlang.org/docs/data-classes.html">Data classes</a>
<p>참고로 코틀린의 구조적 동등비교자(structural equality)인 '=='은 자바의 .equals()이며, 참조 동등비교자(referential equality)인 '==='은 자바의 '=='에 대응한다. 객체는 독립된 메모리를 차지하기 때문에 두 객체의 key와 value의 쌍과 이들의 집합이 일치하라도, 서로 다른 변수에 부여된 인스턴스라면 이 두 객체는 '==='을 사용했을 때 false를 반환한다. 반면 이와 같은 상황에서 '=='은 그 안의 내용을 비교하기 때문에 true를 반환한다. 따라서 data 클래스의 인스턴스는 각각 가리키는 메모리 영역이 다르더라도, .equals() 메소드를 사용하여 그 안의 내용만 비교될 수 있다.</p>
<br>
<br>
<h1> ✅ 객체 초기화, companion objects, 싱글턴 패턴</h1>
<h2>Repository.java</h2>

{% highlight ruby linenos=table %}
import java.util.ArrayList;
import java.util.List;

public class Repository {

    private static Repository INSTANCE = null;

    private List<User> users = null;

    public static Repository getInstance() {
        if (INSTANCE == null) {
            synchronized (Repository.class) {
                if (INSTANCE == null) {
                    INSTANCE = new Repository();
                }
            }
        }
        return INSTANCE;
    }

    // keeping the constructor private to enforce the usage of getInstance
    private Repository() {

        User user1 = new User("Jane", "");
        User user2 = new User("John", null);
        User user3 = new User("Anne", "Doe");

        users = new ArrayList();
        users.add(user1);
        users.add(user2);
        users.add(user3);
    }

    public List<User> getUsers() {
        return users;
    }

    public List<String> getFormattedUserNames() {
        List<String> userNames = new ArrayList<>(users.size());
        for (User user : users) {
            String name;

            if (user.getLastName() != null) {
                if (user.getFirstName() != null) {
                    name = user.getFirstName() + " " + user.getLastName();
                } else {
                    name = user.getLastName();
                }
            } else if (user.getFirstName() != null) {
                name = user.getFirstName();
            } else {
                name = "Unknown";
            }
            userNames.add(name);
        }
        return userNames;
    }
}
{% endhighlight %}

<p>두번째 클래스인 Repository의 자바 버전을 보면 싱글턴(singleton) 패턴을 확인할 수 있다. 싱글턴 패턴은 클래스의 인스턴스화가 한번만 이루어지는 것이다. 따라서 클래스의 getInstance()에 접근하여 반환되는 결과물은 모두 동일할 것이다. 또한 사용자들의 리스트에 해당하는 users 변수는 처음에는 null로 초기화되었다가 생생자 함수가 실행되면서 세 개의 유저 객체를 가진 리스트로 또 다시 초기화된다. </p>

<h2>Repository.kt</h2>
{% highlight ruby linenos=table %}
import java.util.*

class Repository private constructor() {
    private var users: MutableList<User>? = null
    fun getUsers(): List<User>? {
        return users
    }

    val formattedUserNames: List<String>
        get() {
            val userNames: MutableList<String> =
                ArrayList(users!!.size)
            for (user in users) {
                var name: String
                name = if (user!!.lastName != null) {
                    if (user!!.firstName != null) {
                        user!!.firstName + " " + user!!.lastName
                    } else {
                        user!!.lastName
                    }
                } else if (user!!.firstName != null) {
                    user!!.firstName
                } else {
                    "Unknown"
                }
                userNames.add(name)
            }
            return userNames
        }

    companion object {
        private var INSTANCE: Repository? = null
        val instance: Repository?
            get() {
                if (INSTANCE == null) {
                    synchronized(Repository::class.java) {
                        if (INSTANCE == null) {
                            INSTANCE =
                                Repository()
                        }
                    }
                }
                return INSTANCE
            }
    }

    // keeping the constructor private to enforce the usage of getInstance
    init {
        val user1 = User("Jane", "")
        val user2 = User("John", null)
        val user3 = User("Anne", "Doe")
        users = ArrayList<Any?>()
        users.add(user1)
        users.add(user2)
        users.add(user3)
    }
}
{% endhighlight %}
<p>이번에는 코틀린 버전을 보자. 자바에서 초기화 블록이 생성자였다면, 코틀린에서는 init {}이다. 참고로 코틀린에서는 주생성자를 통해 초기화가 가능하지만 여기에는 코드를 작성할 수 없어 init 블록을 활용했다. 한편 자바에서 생성자에 접근 제어자 private을 사용하여 static 메소드인 getInstance에 접근하도록 유도했다면, 여기 코틀린에서는 private constructor()을 명시하고 companion object를 사용하여 instance 프로퍼티에 접근하도록 한다. 즉, 자바의 static은 곧 코틀린의 companion object에 대응한다고 할 수 있다. 단, 해당 클래스가 싱글턴 패턴을 따른다는 점을 고려하면 아래와 같이 수정할 수 있다.</p>

{% highlight ruby linenos=table %}
object Repository {

    private var users: MutableList<User>? = null
    fun getUsers(): List<User>? {
       return users
    }

    val formattedUserNames: List<String>
        get() {
            val userNames: MutableList<String> =
                ArrayList(users!!.size)
        for (user in users) {
            var name: String
            name = if (user!!.lastName != null) {
                if (user!!.firstName != null) {
                    user!!.firstName + " " + user!!.lastName
                } else {
                    user!!.lastName
                }
            } else if (user!!.firstName != null) {
                user!!.firstName
            } else {
                "Unknown"
            }
            userNames.add(name)
       }
       return userNames
   }

    // keeping the constructor private to enforce the usage of getInstance
    init {
        val user1 = User("Jane", "")
        val user2 = User("John", null)
        val user3 = User("Anne", "Doe")
        users = ArrayList<Any?>()
        users.add(user1)
        users.add(user2)
        users.add(user3)
    }
}
{% endhighlight %}
<p>이렇게 private constructor을 가진 클래스로 지정하지 않고 object 키워드를 사용해주면 별도의 인스턴스화 없이 Repository에 직접 접근하여 함수나 프로퍼티를 불러오기만 하면 된다. 즉, 이제는 companion object가 무의미해지므로 지울 수 있다.</p>

<p>formattedUserNames 프로퍼티의 getter 내부를 보게 되면, 특정한 기호 '!!'를 많이 볼 수 있는데, 이는 코틀린에서 not-null assertion 연산자라고 부른다. 코틀린에서 Null Pointer Exception, 일명 NPE라고 부르는 예외 처리와 관련된 연산자 중 하나가 바로 not-null assertion 연산자인데, 이 연산자를 사용하게 되면 실제 null인 값을 지닌 변수에 접근하게 될 때 예외를 맞닥뜨려 앱이 예상치 못하게 작동될 수 있다. 따라서 null이 아닌 경우 할당해준 바로 그 값을, 그 값이 null인 경우에는 다른 대안적인 not-null 값을 할당해주는 로직을 부여해줌으로써 NPE를 효과적으로 다룰 수 있게 된다. 조건문과 함께 elvis operator(?:)을 사용하면 코드의 가독성을 높여주면서 더욱 간결하게 작성할 수 있다. </p>

{% highlight ruby linenos=table %}
object Repository {

    private var users = mutableListOf<Users>()
    fun getUsers(): List<Users>? {
        return users
    }

    val formattedUserNames: List<String>
        get() {
            return users.map { user ->
                if (user.lastName != null) {
                    if (user.firstName != null) {
                        "${user.firstName} ${user.lastName}"
                    } else {
                        user.lastName ?: "Unknown"
                    }
                } else user.firstName ?: "Unknown"
            }
        }

    // keeping the constructor private to enforce the usage of getInstance
    init {
        val user1 = Users("Jane", "")
        val user2 = Users("John", null)
        val user3 = Users("Anne", "Doe")
        users.add(user1)
        users.add(user2)
        users.add(user3)
    }
}
{% endhighlight %}

<p>단순성을 위해 mutableListOf() 함수를 이용하고 init { } 함수 내부의 코드를 정리해주었다. 따라서 클래스 내부에서 '!!' 연산자를 사용할 필요성이 사라졌다. 이와 함께, 리스트의 대표적인 확장형 함수중 하나인 map(), 스트링 템플릿, 조건문과 elvis operator을 이용해 이전보다 깔끔한 형태의 코드가 완성되었다. 이제 변환된 Repository클래스의 코틀린 파일의 코드에만 집중하며 나머지 중요한 관습들을 적용시켜 보자.</p>
<br>
<br>
<h1>✅ Properties와 backing properties</h1>
<p>앞서 말한대로, 코틀린에서 클래스 내부에 선언되는 모든 변수 선언은 '프로퍼티'로서 이루어진다. 값의 실체가 저장되어 있는 필드는 드러나 있지 않지만 외부에서는 접근이 가능한 상태, 즉 encapsulation이 디폴트로 이루어진다. 따라서 실제 필드에 접근하기 위해서는 프로퍼티의 커스텀 접근자인 get과 set 함수에서 'field'라는 식별자를 사용해 backing field에 접근할 수 있다. 이렇게 필드를 사용하면 프로퍼티의 get이나 set을 암묵적으로 호출할 때 자유로운 커스터마이징이 가능하다.</p> 
<p>그러나, 어떤 상황에서는 프로퍼티 자기 자신의 필드의 지위를 다른 프로퍼티에게 넘겨주고 싶을 때가 있다. 이러한 프로퍼티를 backing property라고 부른다. 예를 들면, 오로지 클래스 내부에서만 변수의 값이 변경할 수 있게끔 만들고 싶을 때가 있다. 이러한 경우 개발자는 val과 var 중 어떤 프로퍼티를 만들어야 할 지 고민할 것이다. 답은 둘 다 선언하는 것이다. public val은 프로퍼티로 선언하고 private var은 이에 대한 backing property로 선언하면 된다. 이는 곧, 값의 실체는 private var에게 떠넘겨주고 public val은 외부 클래스에게 개방되게끔하는 backing field의 encapsulation과 유사하다! </p>

<p>backing field와 backing property는 굉장히 구분하기 어려운 개념이라고 생각한다. 공부하면서 필자가 내린 결론은 형태는 다르지만 두 가지 개념은 모두 encapsulation을 보장한다는 것이다. 비유적으로 설명하자면, 내적으로는 값의 실체를 이용해 커스터마이징을 허용하고 외적으로는 값의 껍데기에만 접근할 수 있게 하는 것이다.</p>
<a href="https://proandroiddev.com/backing-properties-in-kotlin-cb78dfebfd90">backing properties in Kotlin</a>

{% highlight ruby linenos=table %}
private val users = mutableListOf<User>()
fun getUsers(): List<User> {
        return users
}
{% endhighlight %}

<p>위의 코드에서 getUsers 메소드는 private 변수인 users를 그대로 반환하고 있다는 점에서, encapsulation에 적합하지 않다.</p>

{% highlight ruby linenos=table %}
private val _users = mutableListOf<User>()
val users: List<User>
        get() = _users
{% endhighlight %}

<p>따라서, 기존의 users 변수를 public val로 만들어주고 이에 대한 backing 프로퍼티인 _users를 생성해주었다. 참고로, 변수는 리스트 자체를 가리키는 것이 아니라 객체에 대한 참조를 담고있는 것이기 때문에 리스트의 원소가 생성 및 삭제되는 것은 변수가 초기화되는 것과 무관하다. 이는 _users 앞에 val을 써도 되는 이유이다. </p>
<br>
<br>
<h1>✅ 확장형 함수와 속성</h1>
<p>코틀린에서는 특정한 타입(예를 들면 클래스, 인터페이스 등)바깥에서 그 타입의 함수 혹은 속성을 추가적으로 설정하는 것을 허용한다. 이를 확장형 함수 혹은 확장형 속성이라고 부른다. 이들은 자기 자신 블록 내부 안에서 원래 자기 타입이 가지고 있던 멤버들에 별도로 타입명을 거치지 않고도 직접 접근할 수 있다. 코틀린의 표준 라이브러리는 기존의 자바 API에 추가적인 기능을 더하기 위해 이들에 대한 확장형 함수 및 속성을 정의하였다. 예를 들면 자바의 Iterable에 대한 확장형 메소드로 아까 작성했던 코드에 포함되었던 map()을 들 수 있다.</p>

{% highlight ruby linenos=table %}
// extension property
val User.userFormattedName: String
    get() {
        return if (lastName != null) {
            if (firstName != null) {
                "$firstName $lastName"
            } else {
                lastName ?: "Unknown"
            }
        } else {
            firstName ?: "Unknown"
        }
    }

object Repository {
    ...
    val formattedUserNames: List<String>
        get() {
            return _users.map { user -> user.formattedName }
        }
    ...
}
{% endhighlight %}

<p>이렇게 기존 formattedUserNames의 복잡한 get 로직을 User 클래스 타입의 확장형 속성으로 모듈화해주었더니, 훨씬 가독성이 높아졌다. user.firstName, user.lastName 이렇게 일일이 클래스에 접근하지 않고도 formattedName이라는 user의 새로운 확장형 속성을 만들어줌으로써 클래스 내부 속성에 직접 접근할 수 있게 되었다.</p>

{% highlight ruby linenos=table %}
init {
    val user1 = User("Jane", "")
    val user2 = User("John", null)
    val user3 = User("Anne", "Doe")
   
    _users.apply {
       add(user1)
       add(user2)
       add(user3)
    }
 }
{% endhighlight %}
<p>마무리로 코틀린의 대표적인 scope functions중 하나인 apply { }를 이용하니 코드가 더 깔끔해졌다. </p>
<br>
<h2>결론</h2>
<p>자바 클래스 예제를 변환하면서 전형적인 코틀린의 패턴을 익힐 수 있었다. 열심히 공식문서를 읽어가면서 공부한 만큼, 기존에 클론코딩에서 막연히 따라했던 때보다 이해의 폭이 넓어진 것 같아 뿌듯하다. 이번에 습득한 지식을 바탕으로 개발 실력까지 상승했으면 하는 바람이다 :)</p>
<br>