### 자바에는 close를 호출해 직접 닫아줘야 하는 자원이 있다.
ex) InputStream, OutputStream, java.sql.Connection
```JAVA
// 일반적으로 많이 사용하는 try-finally
// 자원 1개 사용
static String firstLineOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();
    } finally {
        br.close();
    }
}
// 나쁘지 않지만, 자원을 하나 더 사용하면 ?
```
```JAVA
// 자원 2개 사용
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
// 코드가 너무 지저분하다.
```
### try-finally의 단점
1. 자원 두개 이상 사용시 코드가 지저분하다.
2. finally 블록에서 던져진 exception에 가려 try 블록의 exception이 무시된다.   
   (디버깅 시 최초의 예외를 찾기 힘듬)
3. 종합하면, 가독성을 해칠 가능성이 높고, 예외 처리 로직을 작성하기에 결점이 있다.

---
### 해결책: try-with-resoucres (자바7 부터)
이 구조를 사용하려면 해당 자원이 AutoCloseable 인터페이스를 구현해야 한다.
```JAVA
// 자원 1개 사용
static String firstLineOfFile(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(
            new FileReader(path))) {
        return br.readLine();
    }
}
```
```JAVA
// 자원 2개 사용
static void copy(String src, String dst) throws IOException {
    try (InputStream   in = new FileInputStream(src);
         OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    }
}
```
```JAVA
// catch exception과 함께 사용
static String firstLineOfFile(String path, String defaultVal) {
    try (BufferedReader br = new BufferedReader(
            new FileReader(path))) {
        return br.readLine();
    } catch (IOException e) {
        return defaultVal;
    }
}
```
### try-with-resources의 장점
1. 가독성이 매우 좋아짐
2. 다수의 예외 처리 가능, 예외 발생시 디버깅 하기 편리해짐   
   close(보이지는 않지만) 호출 시 발생하는 예외는 숨겨지고 readline의 예외가 기록된다.
3. 숨겨진 예외는 버려지지않고, 스택 추적 내역에 숨겨져있다.(suppressed)   
   (자바7 에서 getSuppressed 메서드를 이용하면 가져올 수도 있다.)

