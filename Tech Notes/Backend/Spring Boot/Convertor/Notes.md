# Entity To Dto with lambda function

```java

@Service 
// or @Component
public DtoMapper implement Function<Person, PersonDTO>{
	@override
	public PersonDTO apply(Person person){
		return new PersonDTO(person.getName, person.getAge);
	}
}


```

```java

@Autowried
private DtoMapper dtoMapper;
person.findAll().stream().map(dtoMapper).collect(Collectors.toList());
//or without autowired
person.findAll().stream().map(PersonDTO::new).collect(Collectors.toList());

```

