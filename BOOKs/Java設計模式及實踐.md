1. 繼承 Is-a
2. 依賴 Uses-a
3. 聚合 has-a
4. Solid
5. 單例模式
   ```java
   public class LockFreeInstacne{
		private static final LcLockFreeInstacne instance = new LockFreeInstacne();
		private LockFreeInstacne(){
		   System.out.println("Singleton is Instantiated");
		}
		public static synchronized LockFreeInstacne getInstance(){
		   return instance;
		}
		public void doSomething(){
			System.out.println("Something");
		}
   }
	```