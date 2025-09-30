1. 使用靜態工廠取代構造器
2. 多構造器參數使用Builder
	```ad-seealso
	title: asdasd
	collapse: none
	~~~java
	   public abstract class Pizza{
			public enum Topping{
				HAM, MUSHROOM, ONION, PEPPER, SAUSAGE
			}
			final Set<Topping> toppings;
	
			abstract static class Builder<T extends Builder<T>>{
				Enumset<Topping> toppings = EnumSet.noneOf(Tapping.class);
				public T addTopping(Topping topping){
					toppings.add(Objects.requireNonNull(topping));
					return self();
				}
				abstract Pizza Builder();
				protected abstract T self();
			}
			Pizza(Builder<?> builder){
				toppings = builder.toppings.clone();
			}
	   }
	~~~
