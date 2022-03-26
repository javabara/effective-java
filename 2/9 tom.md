# try-finally보다는 try-with-resources를 사용하라

자바 라이브러리는 `close` 메서드를 호출해 직접 닫아줘야하는 자원이 많다. 하지만 자원 닫기는 클라이언트가 놓치기 쉬워 **예측할 수 없는 성능 문제로 이어지기도 한다.** 이러한 자원 중 상당수가 안정망으로 finalizer를 활용하고 있지만 문제가 많다. ([아이템 8](https://github.com/javabara/effective-java/blob/main/2/8.md))

## try-finally
전통적으로 자원이 닫힘을 보장하는 수단으로 `try-finally`가 쓰였다.

``` java
public static String firstLineOfFile(String path) throw IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();
    } finally {
        br.close();
    }
}
```

위 코드에서는 예외가 `try` 블록과 `finally` 블록 모두에서 발생할 수 있는데, 만약 `readLine` 메서드에서 예외가 발생하고, 같은 이유로 `close` 메서드에서도 예외가 발생한다면 `close`의 예외가 다른 예외를 잡아먹게되어 스택 추적 내역에 `readLine`에 대한 예외 정보가 남지 않아 디버깅에 어려움을 겪게 된다.

``` java
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
```

또한 이와같이 out과 in 처럼 자원을 두개 이상 사용하게 되면 depth가 점점 늘어나 코드가 지저분하게 된다.

이러한 문제들은 자바 7에서 나온 `try-with-resources`로 해결 가능하다. 이 구조는 해당 자원이 `autoCloseable` 인터페이스로 구현되어 있어야 한다.


## try-with-resources
``` java
public static String firstLineOfFile(String path) throw IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    } catch (Exception e) {
        return defaultVal;
    }
}
```

짧고 일기 수월할 뿐 아니라 문제를 진단하기도 쉽다. 또한 `catch` 절을 사용하기 때문에 `try` 문을 더 중첩하지 않고도 다수의 예외를 처리할 수 있다.

## 핵심 정리
꼭 회수해야 하는 자원을 다룰 때는 `try-finally` 말고, `try-with-resources`를 사용하자. 코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용하다.