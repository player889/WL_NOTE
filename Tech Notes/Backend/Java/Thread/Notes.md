## 101

```java
class Task implements Runnable{
	@Override
	public void run (){
		System.out.println ("Runnable interface");
	}
}

class ThreadDemo extends Thread{
	@Override
	public void run(){
		System.out.println( "Thread class ");
	}
}

class RunnableDemo {
	public static void main (String ... args){
		//Thread
		new ThreadDemo().start();
		//Runnable
		new Thread (new Task(), "Thread 1").start();
		new Thread (new Task(), "Thread 2").start();
	}
}

```

## synchronized in different way

```java
synchronized void method(){
	...
}

void method(){
	synchronized(this){
	}
}
```

`fas:ExclamationCircle` synchronized with `static` method

```java

class Something{
	static synchronized void method(){
		...
	}
}

class Something{
	static void method(){
		synchronized(Something.class){
			...
		}
	}
}
```

### volatile

1. 原子性
   即一個操作或者多個操作 要麼全部執行並且執行的過程不會被任何因素打斷，要麼就都不執行。
   比如從賬戶 A 向賬戶 B 轉 1000 元，那麼必然包括 2 個操作：從賬戶 A 減去 1000 元，往賬戶 B 加上 1000 元。
   試想一下，如果這 2 個操作不具備原子性，會造成什麼樣的後果。假如從賬戶 A 減去 1000 元之後，操作突然中止。然後又從 B 取出了 500 元，取出 500 元之後，再執行 往賬戶 B 加上 1000 元 的操作。這樣就會導致賬戶 A 雖然減去了 1000 元，但是賬戶 B 沒有收到這個轉過來的 1000 元。
   所以這 2 個操作必須要具備原子性才能保證不出現一些意外的問題。
2. 可見性
   假若執行線程 1 的是 CPU1，執行線程 2 的是 CPU2。由上面的分析可知，當線程 1 執行 i =10 這句時，會先把 i 的初始值加載到 CPU1 的高速緩存中，然後賦值為 10，那麼在 CPU1 的高速緩存當中 i 的值變為 10 了，卻沒有立即寫入到主存當中。
   此時線程 2 執行 j=i，它會先去主存讀取 i 的值並加載到 CPU2 的緩存當中，注意此時內存當中 i 的值還是 0，那麼就會使得 j 的值為 0，而不是 10.
   這就是可見性問題，線程 1 對變量 i 修改了之後，線程 2 沒有立即看到線程 1 修改的值。
    ```java
    //線程1執行的代碼
    int i = 0;
    i = 10;
    //線程2執行的代碼
    j = i;
    ```
3. 有序性
   　由於語句 1 和語句 2 沒有數據依賴性，因此可能會被重排序。假如發生了重排序，在線程 1 執行過程中先執行語句 2，而此是線程 2 會以為初始化工作已經完成，那麼就會跳出 while 循環，去執行 doSomethingwithconfig(context)方法，而此時 context 並沒有被初始化，就會導致程式出錯。
    ```java
    //線程1:
    context = loadContext();   //語句1
    inited = true;             //語句2
    //線程2:
    while(!inited ){
    	   sleep()
    }
    doSomethingwithconfig(context);
    ```

synchronized 關鍵字是防止多個線程同時執行一段代碼，那麼就會很影響程式執行效率，而 volatile 關鍵字在某些情況下性能要優於 synchronized，但是要注意 volatile 關鍵字是無法替代 synchronized 關鍵字的，因為 volatile 關鍵字無法保證操作的原子性。通常來說，使用 volatile 必須具備以下 2 個條件：

1. 對變量的寫操作不依賴於當前值
2. 該變量沒有包含在具有其他變量的不變式中
