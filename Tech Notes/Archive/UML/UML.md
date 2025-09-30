-   Dependency 依賴
-   Association 關聯
-   Aggregation 聚合
-   Composition 組合
-  Generalization 泛化

![[Pasted image 20230330095008.png|500]]
![[Pasted image 20230330095210.png|500]]

![[Pasted image 20230330115050.png|600]]

* Abstract
  ![[Pasted image 20230330100341.png|500]]
* Realization  -  箭頭指向 interface
  ![[Pasted image 20230330095551.png|500]]
* Generalization -  (is-a)  箭頭指向 父類別
  ![[Pasted image 20230330095643.png|500]]
* Association(has-a)
  ![[Pasted image 20230330095815.png|500]]
* Dependency (use-a)
  ![[Pasted image 20230330095901.png|500]]
  ```ad-note
  title: sample
  ~~~java
  	//代码清单1 B.java
	public class B {
	  public String field1;   //成员变量
	
	  public void method1() {
	    System.println("在类B的方法1中");
	  }
	
	  public static void method2() {                 //静态方法
	    System.out.println("在类B的静态方法2中");
	  }
	}
  ~~~
  ~~~
  	/* 代码清单2 A.java
	  A依赖于B
	*/
	
	public class A {
	  public void method1() {
	    //A依赖于B的第一种表现形式：B为A的局部变量
	    B b = new B();
	    b.method1();
	  }
	
	  public void method2() {
	    //A依赖于B的第二种表现形式： 调用B的静态方法
	    B.method2();
	  }
	
	  public void method3(B b)  {
	    //A依赖于B的第三种表现形式：B作为A的方法参数
	    String s = b.field1;
	  }
	
	  //A依赖于B的第四种表现形式：B作为A的方法的返回值
	  public B method4() {
	    return new B();
	  }
	}
  ~~~
	```

* Aggregation(has-a)
  ![[Pasted image 20230330100216.png|500]]
* Composition(contain-a)
  ![[Pasted image 20230330112923.png|500]]

-tx-
| Aggregation vs Compostion                                   || 
| ---------------------------------------- | -------------------------------------------- |
| own a                                    | part of a                                    |
| ![[Pasted image 20230330113915.png\|200]]     | ![[Pasted image 20230330113937.png\|200]]         |
| 學生畢業離開學校，學生、學校還是單獨存在 | 員工離開公司，員工存在但公司可能就不存在了 |