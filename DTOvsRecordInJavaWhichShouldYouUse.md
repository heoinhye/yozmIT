# DTO vs Record: 무엇을 사용해야 할까?

Java개발에서 데이터 전송 객체(DTO)와 레코드(Record)의 차이점과 사용 사례 이해

Java 애플리케이션에서는 종종 애플리케이션의 다양한 계층 간 또는 서비스 간에 데이터를 전송해야 합니다. 이를 위해 우리는 데이터 전송 객체(DTO)를 사용합니다. DTO는 복잡한 동작이나 로직 없이 데이터를 담기 위한 간단한 객체입니다. 이 객체의 역할은 데이터를 묶어서 필요한 곳에 전달하는 것입니다.

그런데 Java14에서 새로운 기능인 **레코드(Records)**가 도입되었습니다. 레코드는 DTO와 마찬가지로 데이터를 담는 데 초점을 맞춘 특별한 클래스 유형입니다. 중요한 차이점은 레코드는 우리가 반복적으로 해야 하는 많은 작업을 자동으로 처리해준다는 것입니다. 예를 들어, 데이터를 가져오는 메서드(일명 getter)를 자동으로 생성하고, 동등성 검사나 toString() 메서드도 알아서 처리해줍니다. 이 기능은 Java 16에서 완전히 사용 가능해지면서 레코드는 Java에서 데이터를 처리하는 깔끔하고 현대적인 방법이 되었습니다.

그렇다면 왜 DTO와 레코드를 비교하는 걸까요? 그 이유는 둘 다 데이터를 운반하는 비슷한 목적을 가지고 있기 때문입니다. 하지만 Java가 계속 발전함에 따라 언제 DTO를 사용하고 언제 레코드를 사용해야 하는지 이해하는 것이 중요합니다. 이 글에서는 DTO와 레코드의 차이점을 알아보고, 특히 현대적인 Java 애플리케이션에서 어떤 것이 더 적합한지 결정하는 데 도움을 드리겠습니다.


## DTO란 무엇일까?
**데이터 전송 객체(Data Transfer Object, DTO)**는 애플리케이션의 다양한 부분 간에 데이터를 이동시키는 데 사용되는 간단한 Java 객체입니다. 애플리케이션의 계층 간에 데이터를 운반하는 컨테이너라고 생각할 수 있습니다. 예를 들어, 웹 애플리케이션에서 DTO는 서비스 계층에서 컨트롤러로 데이터를 전송하거나, 컨트롤러에서 뷰 계층으로 데이터를 전달할 때 사용될 수 있습니다.

DTO는 애플리케이션의 다른 부분들을 서로 분리하는 데 도움을 주며, 코드가 더 체계적이고 유지 관리하기 쉬워집니다. 일반적으로 DTO에는 비즈니스 로직이나 복잡한 동작이 없습니다. 대신, 그저 데이터를 담고 있는 역할만 합니다.

### DTO는 어떻게 구현될까?
DTO는 보통 일반적인 Java 클래스로 구현됩니다. 일반적인 DTO는 다음과 같은 요소를 포함합니다:

• 데이터를 담는 private 필드 <br>
• 데이터를 접근하고 수정하기 위한 Getter와 Setter 메서드 <br>
• 객체를 생성하는 생성자(Constructor) <br>
• 객체를 비교하거나 출력할 때 유용한 toString(), hashCode(), equals() 메서드를 재정의(Override) <br>

다음은 UserDTO 클래스의 예입니다:

```java
import java.util.Objects;

public class UserDTO {
    private String name;
    private int age;
    private String email;

    // Constructor
    public UserDTO(String name, int age, String email) {
        this.name = name;
        this.age = age;
        this.email = email;
    }

    // Getters and Setters
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    // Overriding toString method for meaningful output
    @Override
    public String toString() {
        return "UserDTO{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                '}';
    }

    // Overriding equals method for object comparison
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        UserDTO userDTO = (UserDTO) o;
        return age == userDTO.age && Objects.equals(name, userDTO.name) && Objects.equals(email, userDTO.email);
    }

    // Overriding hashCode method
    @Override
    public int hashCode() {
        return Objects.hash(name, age, email);
    }
}
```

이 UserDTO 클래스는 사용자에 대한 정보를 담고 있습니다: 이름, 나이, 이메일과 같은 정보입니다. 또한 두 개의 UserDTO 객체를 비교할 수 있는 equals() 메서드, 고유한 해시 코드를 생성하는 hashCode() 메서드, 그리고 읽기 쉽도록 출력하는 toString() 메서드와 같은 기본 기능도 제공합니다.

> "Lombok" 같은 도구를 사용하면, 직접 반복적인 코드를 작성하지 않고도 완전한 DTO를 만들 수 있습니다. 하지만 이후에 다룰 레코드(Records)에서는, Java가 반복적인 코드를 없애주는 또 다른 방법을 제공하는데, 이는 기본적으로 \*\*불변성(immutability)을 유지하는 다른 설계 철학을 따릅니다."

UserDTO를 사용하는 방법은 다음과 같습니다:

```java
public class UserDTOUsageExample {
    public static void main(String[] args) {
        UserDTO user = new UserDTO("Ashutosh", 25, "ashutosh@example.com");

        // Access data
        System.out.println(user.getName());
        System.out.println(user.getAge());
        System.out.println(user.getEmail());

        // Using the toString() method
        System.out.println(user);

        // Comparing records
        UserDTO anotherUser = new UserDTO("Vishakha", 22, "vishakha@example.com");
        System.out.println(user.equals(anotherUser));
    }
}
```

결과

```plain text
Ashutosh
25
ashutosh@example.com
UserDTO{name='Ashutosh', age=25, email='ashutosh@example.com'}
false
```


## Java 레코드(Record)란?
Java 레코드는 Java 14에서 미리보기 기능으로 도입되었고, Java 16에서 정식으로 출시된 특별한 유형의 클래스입니다. 레코드는 불변(Immutable) 데이터를 간결하고 읽기 쉽게 담는 데 초점을 맞추고 있으며, 기존 클래스(DTO 등)에서 필요했던 반복적인 코드를 많이 줄여줍니다.

전통적인 DTO에서는 생성자, getter, equals(), hashCode(), toString() 메서드를 직접 작성해야 합니다. 하지만 레코드를 사용하면 Java가 이 모든 것들을 자동으로 생성해줍니다. 덕분에 간단한 불변 객체, 즉 데이터만 담는 역할을 하는 객체를 만들 때 매우 유용합니다.

### Java 레코드의 주요 특징
- 기본적으로 불변성: 한 번 레코드를 만들면 그 데이터를 변경할 수 없습니다. (일반적으로 변경 가능한 DTO와는 다르게 동작합니다.)
- 간결한 문법: 필드만 선언하면 Java가 자동으로 생성자, getter, equals(), hashCode(), toString() 메서드를 만들어 줍니다.
- Setter 없음: 레코드는 불변 객체이기 때문에 Setter 메서드를 제공하지 않습니다.

다음은 앞서 보았던 UserDTO와 동일한 사용자 데이터를 담는 UserRecord를 레코드로 만드는 예입니다:

```java
public record UserRecord(String name, int age, String email) {
}
```

이게 전부입니다! 단 한 줄만으로 Java는 자동으로 다음을 생성해 줍니다:

- 생성자: new UserRecord(String name, int age, String email)
- 각 필드에 대한 Getter 메서드: name(), age(), email()
- 두 개의 UserRecord 객체를 비교하는 equals() 메서드
- 고유한 해시 코드를 생성하는 hashCode() 메서드
- 문자열 표현을 반환하는 toString() 메서드: 예를 들어 UserRecord[name=Ashutosh, age=25, email=ashutosh@example.com] 같은 형태로 출력됩니다.

UserRecord를 사용하는 방법은 다음과 같습니다:

```java
public class UserRecordUsageExample {
    public static void main(String[] args) {
        UserRecord user = new UserRecord("Ashutosh", 25, "ashutosh@example.com");

        // Access data
        System.out.println(user.name());
        System.out.println(user.age());
        System.out.println(user.email());

        // Using the toString() method
        System.out.println(user);

        // Comparing records
        UserRecord anotherUser = new UserRecord("Vishakha", 22, "vishakha@example.com");
        System.out.println(user.equals(anotherUser));
    }
}
```

결과

```plain text
Ashutosh
25
ashutosh@example.com
UserRecord[name=Ashutosh, age=25, email=ashutosh@example.com]
false
```

UserRecord를 사용하면 getter, 생성자, equals(), hashCode(), toString() 메서드를 일일이 작성할 필요가 없습니다. Java 레코드는 불변 객체를 간단하고 깔끔하게 만들 수 있는 방법을 제공합니다.

### 왜 레코드를 사용할까?
- 불필요한 코드 감소: 반복적으로 작성해야 하는 getter 메서드나 equals() 메서드 같은 코드를 쓸 필요가 없어요.

- 불변성(Immutable) 보장: 객체가 생성된 후 데이터를 변경할 수 없게 만들어 다중 스레드 환경에서 더 안전하게 사용할 수 있어요.

- 명확한 의도: 레코드를 사용하면 해당 객체가 추가적인 동작이나 로직 없이 데이터를 전달하기 위한 것임을 분명하게 알 수 있어요.


## DTO와 Record 비교
이제 DTO와 Record에 대해 알았으니, 두 개를 비교해 보겠습니다.

### 불변성(Immutable)
Record는 기본적으로 불변성(Immutable)을 가지며, 한 번 생성된 Record 인스턴스의 데이터는 변경할 수 없습니다. 이러한 불변성 덕분에 데이터는 일관성을 유지하며, 추가적인 코드 없이도 스레드 안전(Thread-safe)합니다. 예를 들어, UserRecord에서는 name, age, email 필드가 생성될 때만 설정되고, 이후에는 수정할 수 없습니다.

반면에, DTO는 일반적으로 가변성(Mutable)을 가지며, 객체가 생성된 후에도 필드를 변경할 수 있습니다. DTO를 불변으로 만들려면 setter 메서드를 사용하지 않거나, final 필드를 사용하여 신중하게 설계해야 합니다. 불변성을 구현하는 방식은 다음과 같습니다:

- Record: 기본적으로 불변성 보장.
- DTO: 불변성을 유지하려면 추가적인 코드 설계가 필요하며, 복잡한 코드나 잠재적인 버그가 생길 수 있습니다.

```java
public class ImmutabilityExample {
    public static void main(String[] args) {
        UserDTO userDTO = new UserDTO("Ashutosh", 25, "ashutosh@example.com");
        userDTO.setAge(26);  // DTO는 기본적으로 값 변경을 허용합니다.

        UserRecord userRecord = new UserRecord("Ashutosh", 25, "ashutosh@example.com");
        userRecord.name = "Jane"; // 이는 컴파일 에러가 발생합니다.
    }
}
```

### 보일러플레이트 코드(Boilerplate Code)
Record의 큰 장점 중 하나는 보일러플레이트 코드를 크게 줄일 수 있다는 점입니다. DTO를 사용할 때는 보통 getter, setter, 생성자, equals(), hashCode(), toString() 메서드를 수동으로 작성해야 하지만, Record는 이러한 메서드들을 자동으로 생성해줍니다.

반대로, 전통적인 DTO는 수동으로 코드를 작성해야 합니다. Lombok 같은 도구를 사용하면 보일러플레이트 코드를 줄일 수 있지만, Record만큼 간단하지는 않습니다.

- Record: 생성자, getter, equals(), hashCode(), toString()이 자동으로 생성됩니다.
- DTO: 수동 구현이 필요하거나 Lombok 같은 도구를 사용해야 합니다.

### 데이터 표현 방식
Record는 데이터를 간결하고 직관적으로 표현하는 방법을 제공합니다. Record 선언에는 필드만 포함되므로 코드가 더 깔끔하고 읽기 쉽습니다. 특히 데이터 모델이 많은 프로젝트에서 유지보수가 더 용이합니다.

예시:

```java
// Record: 간결하고 단순한 코드
public record UserRecord(String name, int age, String email) {}
```
반면, DTO는 코드가 더 길어집니다:

```java
// DTO: 더 많은 코드 필요
public class UserDTO {
    private String name;
    private int age;
    private String email;

    // 생성자, getter, setter, toString, equals, hashCode...
}
```
Record는 단순히 데이터를 전달하는 용도임을 명확하게 드러내며, DTO는 보일러플레이트 코드나 추가 로직으로 인해 복잡해질 수 있습니다.

### 커스터마이징(맞춤 설정)
DTO의 장점 중 하나는 커스터마이징이 용이하다는 점입니다. DTO에서는 데이터 유효성 검사, 데이터 변환 메서드 또는 비즈니스 로직을 추가할 수 있습니다 (순수 DTO에서는 권장되지 않지만 가능). 예를 들어, email 필드가 유효한 형식인지 확인하는 검증 메서드를 추가할 수 있습니다.

Record는 반면 커스터마이징이 제한적입니다. Record는 가볍고 불변성을 유지하도록 설계되었기 때문에, 내부 상태를 수정하거나 복잡한 로직을 쉽게 추가할 수 없습니다. 만약 데이터 객체에 커스터마이징된 동작이나 로직이 필요하다면, DTO가 더 유연한 선택입니다.

DTO에서 커스터마이징 예시:

```java
public class UserDTO {
    private String email;

    // 이메일 형식을 검증하는 메서드
    public boolean isValidEmail() {
        return email != null && email.contains("@");
    }
}
```
Record에서는 이런 커스터마이징은 보통 Record 외부에서 처리해야 합니다. Record는 데이터를 전달하는 데만 집중하고, 검증 같은 로직은 다른 곳에서 처리하는 것이 일반적입니다.

### 함수형 프로그래밍과의 연관성
함수형 프로그래밍의 핵심 원칙 중 하나는 불변성입니다. 즉, 데이터 객체가 한 번 생성된 후에는 변경되지 않아야 한다는 것입니다. Record는 기본적으로 불변이므로 함수형 프로그래밍 원칙과 잘 맞습니다. 따라서 불변 데이터 객체를 사용하고자 하는 시스템에 적합한 선택입니다.

- Record:
    - 불변성에 맞게 설계되었으며, 함수형 프로그래밍이 강조하는 상태 변화 없는 데이터 구조와 잘 맞습니다.
    - 불변 데이터를 전달할 수 있어 예측 가능하고 이해하기 쉬운 코드 스타일을 촉진합니다.

반면, DTO는 전통적으로 가변성을 가지며, 불변으로 만들려면 수동적인 설정이 필요합니다. DTO는 상태 변경이 흔한 객체 지향 프로그래밍 스타일에 더 적합합니다.

- DTO:
    - 가변성에 더 유연하여 명령형 또는 객체 지향 프로그래밍 스타일에 적합합니다.
    - 가변성을 사용하면 함수형 프로그래밍에서 일반적으로 권장되지 않는 부작용이 발생할 수 있습니다.