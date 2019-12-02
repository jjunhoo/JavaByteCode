###리플렉션 API 1부 : 클래스 정보 조회

````java
public class Book {
  private String a;
  private static String B = "BOOK";
  private static final String C = "BOOK";
  public String d = "d";
  protected String e = "e";
  public Book() {
  }
  public Book(String a, String b, String e) {
    this.a = a;
    this.d = d;
    this.e = e;
  }
  private void f() {
    System.out.println("F");
  }
  public void g() {
    System.out.println("g");
  }
  public int h() {
    return 100;
  }
}
````

```` java
public class MyBook extends Book {
}
````

```` java
public class MyBook extends Book implements MyInterface {
}
````

```` java
public class App {
  public static void main(String[] args) throws ClassNotFoundException {
    // 1. 클래스 정보를 통해 접근
    Class<Book> bookClass = Book.class;
    
    // 2. 인스턴스를 통해 접근
    Book book = new Book();
    Class<? extends Book> aClass = book.getClass();
    
    // 3. 문자열을 통해 클래스 접근 
    Class<?> aClass1 = Class.forName("com.test.Book");
    
    // 상단 'Book' 정의해둔 필드 출력
    // 출력 : public java.lang.String com.test.Book.d
    // 5개의 필드 중 'd' 필드 1개만 출력됨 
    // getField() 메서드는 public 필드만 리턴
    //
    // getDeclaredField() 메서드는 접근제어자에 상관없이 모든 필드 정보 리턴
    // 출력 : private static java.lang.String com.test.Book.B
    //        private static final java.lang.String com.test.Book.C
    //        private java.lang.String com.test.Book.a
    //        public java.lang.String com.test.Book.d
    //        protected java.lang.String com.test.Book.e
    // getDeclaredField("name") 메서드는 특정 필드명 리턴 
    Array.stream(bookClass.getFields()).forEach(System.out::println);
    
    // 특정 클래스 필드 정보 및 필드값 가져오는 방법
    // 출력 : private static java.lang.String com.test.Book.B
    //        private static final java.lang.String com.test.Book.C
    //        private java.lang.String com.test.Book.a
    //        public java.lang.String com.test.Book.d
    //        protected java.lang.String com.test.Book.e
    Book book = new Book(); 
    Arrays.stream(bookClass.getDeclaredFields()).forEach(f -> {
      try {
        // 해당 클래스에 접근제어자를 무시하고 필드명 정보와 필드값을 모두 가져옴
        f.setAccessible(true); 
        System.out.printf("%s %s\n", f, f.get(book));
      } catch (IllegalAccessException e) {
        e.printStackTrace();
      }
    });
    
    // 특정 클래스의 메서드 정보를 가져오는 방법
    // 출력 : public int com.test.Book.h()
    //        public void com.test.Book.g()
    //        + Object에서 상속받은 메서드
    Arrays.stream(bookClass.getMethods()).forEach(System.out::println);
    
    // 특정 클래스의 생성자 정보 
    // 출력 : public com.test.Book()
    //        public com.test.Book(java.lang.String.java.lang.String.java.lang.String)
    Arrays.stream(bookClass.getDeclaredConstructors()).forEach(System.out::println);
    
    // 특정 클래스의 부모클래스 정보 
    // 출력 : class com.test.Book
    System.out.println(MyBook.class.getSuperclass());
    
    // 특정 클래스의 인터페이스 정보 
    // 출력 : interface com.test.MyInterface
    Arrays.stream(MyBook.class.getInterfaces()).forEach(System.out::println);
    
    // 특정 클래스의 필드 접근제어자 정보
    // 출력 : public java.lang.String com.test.Book.d
    //        false
    //        false 
    Arrays.stream(Book.class.getDeclaredFields()).forEach(f -> {
      int modifiers = f.getModifiers();
      System.out.println(f);
      // private 확인 
      System.out.println(Modifirer.isPrivate(modifiers));
      // static 확인
      System.out.println(Modifirer.isStatic(modifiers));
    });
  }
}
````
