박성철 님께서 공유해주신 [Moving to Java 11: Rediscover Some Gems You Might Have Missed](https://dzone.com/articles/moving-to-java-11-rediscover-some-quotcode-gemsquo)라는 글을 보고, 해당 문서에 명시된 메서드들 한번씩 써보고 정리해봤다.

# 당신이 놓치고 있었을, Java 11의 몇가지 보석같은 기능들

## 입출력 관련
### InputStream
#### ```static InputStream nullInputStream()```
* 자바 11에서 추가됨
* 바이트를 읽어들이지 않는 InputStream을 반환한다 (=이 스트림에서 read()를 호출하면 -1이 반환된다)
* 이 메서드는 확인 예외를 던지지 않으므로, 예외를 잡지 않아도 된다.
* close() 메서드는 아무런 역할을 하지 않는다.
#### ```byte[] readAllBytes() throws IOException```
* 자바 9에서 추가됨
* 입력 스트림에서 전체 데이터를 한번에 읽어와서 바이트 배열로 반환한다. 입력 스트림에서 가져오는 전체 데이터의 크기가 작다고 알고 있을때 유용하다.
#### ```byte[] readNBytes(int len) throws IOException```
* 자바 11에서 추가됨
* 입력 스트림에서 len 만큼의 데이터를 바이트 배열로 반환한다.
#### ```long transferTo(OutputStream out) throws IOException```
* 자바 9에서 추가됨
* 입력 스트림의 전체 바이트 데이터를 출력 스트림으로 모두 전송하고, 전송한 데이터의 바이트 수를 long 타입으로 반환한다.
* 개인적으로는 조금 아쉬운데, 데이터의 length나 offset을 지정할 수 있었으면 더 좋았을 것 같다.
### OutputStream
#### ```static OutputStream nullOutputStream()```
* 자바 11에서 추가됨
* 바이트를 출력하지 않는 OutputStream을 반환한다 (=이 스트림에서 write()를 호출해도 아무 일도 일어나지 않는다)
* 이 메서드는 확인 예외를 던지지 않으므로, 예외를 잡지 않아도 된다.
* close() 메서드는 아무런 일을 하지 않는다.
### Reader
#### ```static Reader nullReader()```
* 자바 11에서 추가됨
* ```InputStream.nullInputStream()```의 Reader 버전으로, 각종 특징들은 nullInputStream() 과 동일하다.
#### ```long transferTo(Writer out) throws IOException```
* 자바 10에서 추가됨
* ```InputStream.transferTo(Writer)```의 Reader 버전으로, 각종 특징들은 InputStream.transferTo()와 동일하다.
### Writer
#### ```static Writer nullWriter()```
* 자바 11에서 추가됨
* ```OutputStream nullOutputStream()```의 Writer 버전으로, 각종 특징들은 nullOutputStream()과 동일하다.

## 파일과 경로 관련
### Files
#### ```static Path of(String first, String... more)```
* 자바 11에서 추가됨
* 인자로 넘어온 문자열들을 순서대로 조합해서, 현재 실행중인 플랫폼에 맞는 경로를 표현하는 Path 객체를 만든다.
* 지금까지 자주 해오던, File.separator를 구분자로 하는 경로 문자열을 만드는 작업을 하는 메서드라고 보면 정확하겠다.
* 즉, 아래 두 코드는 동일한 작업을 하는 코드라고 하겠다.

```
(자바 10 이전의 경우)
String paths[] = {"c:", "develop", "java", "jdk"};
String path = "";
for(int i = 0; i < paths.length; i++) {
    path += paths[i];
    if(i != path.length - 1) path += File.seperator;
}
System.out.println(path);       // c:\develop\java\jdk

(자바 11부터)
Path jdkPath = Path.of("c:", "develop", "java", "jdk");
String path = jdkPath.toString();
System.out.println(path);       // c:\develop\java\jdk
```

### String 클래스
#### ```IntStream codePoints()``` , ```IntStream chars()```
* 자바 9에서 추가됨
* 문자열의 각 글자가 가리키는 유니코드 코드포인트의 순열을 IntStream 형태로 반환한다.
#### ```boolean isBlank()```
* 자바 11에서 추가됨
* 주어진 문자열이 비어있는 문자열인지 확인한다. 비어있는 문자열이란, 길이 관계 없이 화이트 스페이스 문자만으로 구성된 문자열이다.

```
String text1 = "Hello, world!";
String text2 = "     \t\t   ";
String text3 = "";
System.out.println(
    text1.isBlank() + "\t" +            // false
    text2.isBlank() + "\t" +            // true
    text3.isBlank()                     // true
);
```

#### ```Stream<String> lines()```
* 자바 11에서 추가됨
* 문자열을 개행문자를 기준으로 분할한 스트림을 반환한다.
#### ```String repeat(int n)```
* 자바 11에서 추가됨
* 문자열을 n 횟수만큼 반복한 문자열을 반환한다.
* 이 메서드도 개인적으로는 좀 아쉬운데, 구분자를 추가로 입력받아서 반복한 횟수만큼 구분자로 구분된 문자열을 반환해주는 메서드도 함께 제공되었으면 더 좋았을 거 같다.
#### ```String strip()``` , ```String stripLeading()``` , ```String stripTrailing()```
* 자바 11에서 추가됨
* 각각 『문자열 앞뒤/앞/뒤 의 공백을 제거』한 문자열을 반환한다.
* 기존의 trim() 메서드와 다른점은, API 문서에 따르면 trim() 메서드는 코드 포인트 U+0020 이하인 문자들을 공백으로 판단하여 제거한다고 한다. 
테스트 해 보니, strip() 메서드는 그 외에도 U+3000 Ideographic Space 등도 제거해주는걸 확인할 수 있었는데, 정확한 기준은 잘 모르겠다.
(일단 API 문서에 따르면 Characters.isWhitespace() 호출해서 true가 나오는 문자를 삭제한다고는 하는데, 이 설명대로면 String.trim()이랑 다를게 없다)

## 컬렉션 API
### Collection
#### ```<T> T[] toArray(IntFunction<T[]> generator)```
* 자바 11에서 추가됨
* 컬렉션의 요소들을 generator로 제공된 생성자 타입으로 변환한다.
* 기존에도 ```Collection.toArray()``` 메서드를 통해서 컬렉션을 배열로 변환할 수 있었지만, 반환 타입이 Object[] 여서 타입 안정성이 깨질 가능성이 있었다.
* generator에 반환 타입의 생성자를 레퍼런스로 넘겨서, 정확한 타입으로 반환할 수 있다.

```
(자바 10 이전의 경우)
List<String> list = ....blahblah...
// Integer[] nums = (Integer[])list.toArray() 로 써도 컴파일 타임에 오류가 발생하지 않는다!!!
String[] strings = (String[])list.toArray();

(자바 11의 경우)
List<String> list = ....blahblah...
// 컴파일러는 list의 타입이 List<String>인 것을 보고, String[] 이외의 타입으로는 변환할 수 없다고 추론해낸다
// 따라서 String[]::new 이외의 생성자를 넘기면 컴파일 타임에 오류가 발생한다
String[] strings = list.toArray(String[]::new);
```

### Map, Set, List 등 Collection 하위 인터페이스
#### ```static <E> List<E> copyOf(Collection<? extends E> coll)```
* 자바 10에서 추가됨
* 컬렉션 coll을 받아서, 이 coll이 그대로 복사된 새 변경 불가 컬렉션을 반환한다.
* 기존의 Collections.unmodifiableXXX() 와 다른점은, 기존의 Collections.unmodifiable... 은 반환값이 변경 불가 뷰였기 때문에,
원본 컬렉션의 데이터에 수정을 가하면 뷰의 값도 변경되어 표시되었다. copyOf() 메서드로 반환된 컬렉션은 주어진 컬렉션의 복사본이기 때문에
원본을 수정한다 하더라도, 반환된 컬렉션은 수정되지 않는다.

## Stream
### ```static <T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperater<T> next)```
* 자바 9에서 추가됨
* 초기값 ```seed```에서 시작해서, 단항연산 ```next```로 지정한 동작대로 값이 순차적으로 변경되는 순차 스트림을 생성한다. 이 순차 스트림은 ```hasNext```가 false일 때 종료된다.

### ```Stream<T> takeWhile(Predicate<? super T> predicate)```
* 자바 9에서 추가됨
* takeWhile로 전달된 predicate를 만족하는 동안만 순회를 진행한다. 전달된 predicate를 만족하지 못하는 경우 순회를 중단하고, 스트림은 종료된다.
### ```Stream<T> dropWhile(Predicate<? super T> predicate)```
* 자바 9에서 추가됨
* dropWhile은 takeWhile과 반대로, predicate를 만족하는 값은 버리고 다시 순회한다. 전달된 predicate가 만족하지 않는 시점부터 순회가 진행된다.

악간 억지스럽기는 한데, 위 세개의 메서드를 조합해서 15 ~ 75 까지의 숫자를 출력하는 코드는 다음과 같다.

```
// 사실은 IntStream.iterate(15, n -> n <= 75, n -> ++n) 으로도 충분하다

IntStream.iterate(1, n -> n <= 100, n -> ++n)       // 1 ~ 100 까지의 정수 스트림
    .takeWhile(n -> n <= 75)                        // 75 이하의 값만 취한다
    .dropWhile(n -> n < 15)                         // 15 미만의 값은 버린다.
    .forEach(System.out::println);
```