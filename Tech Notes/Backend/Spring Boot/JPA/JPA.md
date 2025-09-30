#JPA

1. [[#@MapperSuperclass|@MapperSuperclass]]
2. [[#@Immutable|@Immutable]]
3. [[#@MapperSuperclass vs @Inheritance|@MapperSuperclass vs @Inheritance]]
4. [[#jpa 實體類壹對多 set 與 list 使用|jpa 實體類壹對多 set 與 list 使用]]
5. [[#Query with @OneToMany|Query with @OneToMany]]
6. [[#Update with @OneToMany|Update with @OneToMany]]
7. [[#Native query with Enum as parameter|Native query with Enum as parameter]]
8. [[#@Embeddable|@Embeddable]]
9. [[#List Data to Pageable|List Data to Pageable]]
10. [[#Pageable /w ExampleMatcher|Pageable /w ExampleMatcher]]
11. [[#Page Class|Page Class]]
12. [[#Query Example|Query Example]]
13. [[#ConversionFailedException|ConversionFailedException]]
14. [[#Base Service && BaseRepository|Base Service && BaseRepository]]
15. [[#Query with join table / native SQL|Query with join table / native SQL]]
16. [[#Query with List<Object[]>|Query with List<Object[]>]]
17. [[#Example of Query|Example of Query]]
18. [[#JPA SQL in files|JPA SQL in files]]
19. [[#Example - One to many|Example - One to many]]
20. [[#Example - Many to Many|Example - Many to Many]]
21. [[#N+1 Probelm|N+1 Probelm]]
22. [[#Specification - Query customized Object with dynamic where conditions from join table|Specification - Query customized Object with dynamic where conditions from join table]]
23. [[#@ElementCollection|@ElementCollection]]
24. [[#@EnumSet|@EnumSet]]
25. [[#Execute SQL Script|Execute SQL Script]]
26. [[#Query Example with boolean type|Query Example with boolean type]]
27. [[#Query In Condition|Query In Condition]]
28. [[#JPA UPDATE - optimistic locking|JPA UPDATE - optimistic locking]]
29. [[#Persist|Persist]]
30. [[#@SubSelect|@SubSelect]]
31. [[#@Audited|@Audited]]
32. [[#JPA Transaction Manager|JPA Transaction Manager]]
33. [[#LOG SQL INSERT VALUE|LOG SQL INSERT VALUE]]


## @MapperSuperclass

```java
@MappedSuperclass
public abstract class AbstractTableObject {
 // common mappings
}

@Entity
@Table(name = "someTable")
public class TableObject extends AbstractTableObject {
 // remaining mappings
}

@Entity
@Table(name = "someTable")
@Immutable
public class SimpleTableObject extends AbstractTableObject {
 // nothing here
}

```

## @Immutable

_The entity only for query with this @Immutable
exception causes when update data by this define entity_

## @MapperSuperclass vs @Inheritance

[\_Link](https://stackoverflow.com/questions/42609526/hibernate-extend-entity-for-same-table)
If you want **Parent Child** relation and want generate table per class then you can use @Inheritance(strategy = InheritanceType.TABLE_PER_CLASS) it will create table per class.
Otherwise, you may want to use @MapperSuperclass

```java
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public class Payment {
    @Id
    @GeneratedValue(strategy = GenerationType.TABLE)
    private int id;
    @Column(nullable = false)
    private double amount;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public double getAmount() {
        return amount;
    }

    public void setAmount(double amount) {
        this.amount = amount;
    }
}


@Entity
public class CreditCard extends Payment {
private String ccNumber;
private Date expireDate;

    public String getCcNumber() {
        return ccNumber;
    }

    public void setCcNumber(String ccNumber) {
        this.ccNumber = ccNumber;
    }

    public Date getExpireDate() {
        return expireDate;
    }

    public void setExpireDate(Date expireDate) {
        this.expireDate = expireDate;
    }
}
```

## jpa 實體類壹對多 set 與 list 使用

#oneToMany

> 當從一的一端取出其所對應的多的一端時，如果用的是 set 那麽取出多的一端的值時順序是無序的，如果用的是 list 那麽取出多的一端的值時順序是有序的（其實就是 list 與 set 的特性罷了）
>
> 問題:因為 set 查詢出的數據是無序的，如果當壹的一端對應 5 條數據，而分頁的單頁只顯示 3 條數據，當到下一頁時，其中的兩條數據可能與與上一頁的 3 條數據出現隨機相同的情況，因此會導致數據顯示的缺失和無序。
>
> 註：不過 jpa 設計的時候用的是 set 不是沒有道理的，主要是利用 set 的數據不可重復性，用於避免數據的重復，比如新增數據的時候避免數據的重復插入。
>
> 完美的解決辦法：在實體類 set 屬性上加上@orderBy("id asc")屬性進行排序，然後在取值的時候使用 LinkedHashSet（不可重復且有序 sss）接住即可

### @OneToMany Example

```java
    @OneToMany(mappedBy = "customer", cascade = CascadeType.ALL)
    private final Set<Address> addresses = new HashSet<Address>();

    // No setter, only a getter which returns an immutable collection
    public Set<Address> getAddresses() {
        return Collections.unmodifiableSet(this.addresses);
    }

    public void addAddress(Address address) {
        address.setCustomer(this);
        this.addresses.add(address);
    }
```

## Query with @OneToMany

```java
@Repository
public interface CustomerRepository extends JpaRepository<Customer, Long> {
    List<Customer> findByAddresses_City(String city);
}
```

### Using @Data in @OneToMany

https://stackoverflow.com/questions/40266770/spring-jpa-bi-directional-cannot-evaluate-tostring  
[解决 spring data jpa 一对多，多对一双向依赖引用递归，查询出现 java.lang.StackOverflowError: null 问题](https://www.jianshu.com/p/1e10a6eee284)

### ISSUE

> [!Note] Method threw 'org.hibernate.LazyInitializationException' exception. Cannot evaluate com.worldline.ctbc.extras.abo.entity.CompanyBranch.toString()
> <span style="color:red;">`rir:ArrowRight`</span> 有三種方法解決:
>
> 1. Dao add `@Transactional(readOnly = true)`
> 2. fetch = FetchType.EAGER
> 3. @JsonBackReference and @JsonBackReference

> [!Note] Method threw 'java.lang.StackOverflowError' exception. Cannot evaluate com.XXX.XXX.A.toString()
> debug 模式下报错:Method threw 'java.lang.StackOverflowError' exception. Cannot evaluate xxx.toString()  
> <span style="color:red;">`rir:ArrowRight`</span> 解決:
>
> 1. Remove @Data
> 2. Override toString method or @ToString(exclude = {"payment"}) //payment is @OneToMany field

## Update with @OneToMany

```java
@ToString(exclude = {"companyBranchCollateralList", "companyBranchGuarantorList", "company"})
@Data
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Builder
@Table(name = "company_branch")
@Audited
@JsonIdentityInfo(generator = ObjectIdGenerators.PropertyGenerator.class, property = "id", scope = CompanyBranch.class)
public class CompanyBranch extends WLEntity implements Deletable {

    private static final long serialVersionUID = 6509632020750143450L;

	@JsonManagedReference
	@OneToMany(mappedBy = "companyBranch", cascade = CascadeType.ALL, orphanRemoval = true)
	private List<CompanyBranchGuarantor> companyBranchGuarantorList = new ArrayList<>();

	@JsonProperty("companyBranchGuarantorList")
	public void setCompanyBranchGuarantorList(List<CompanyBranchGuarantor> companyBranchGuarantorList) {
	    this.companyBranchGuarantorList.clear();
	    if (companyBranchGuarantorList != null) {
	        this.companyBranchGuarantorList.addAll(companyBranchGuarantorList);
	    }
	}

}


@Data
@NoArgsConstructor
@AllArgsConstructor
@Entity
@Table(name = "company_branch_guarantor")
@JsonIdentityInfo(generator = ObjectIdGenerators.PropertyGenerator.class, property = "id", scope = CompanyBranchGuarantor.class)
@Audited
@AuditOverride(forClass = WLEntity.class, isAudited = true)
public class CompanyBranchGuarantor extends WLEntity implements Deletable {

    @JsonBackReference
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name="company_branch_id", nullable = false)
    private CompanyBranch companyBranch;

```

```java

if (null == oldEntity.getVersion()) {
    oldEntity.setVersion(1L);
}

this.updateGuarantors(po, oldEntity);
companyBranchRepository.save(oldEntity);

```

```java
private void updateGuarantors(CompanyBranchBean po, CompanyBranch entity) {
    if (CollectionUtils.isEmpty(po.getCompanyBranchGuarantors())) {
        entity.getCompanyBranchGuarantorList().clear();
    } else {
        List<CompanyBranchGuarantor> newGuarantorsEntity = this.mapGuarantorsToEntity(entity, po.getCompanyBranchGuarantors());
        List<CompanyBranchGuarantor> existingGuarantorsEntity = entity.getCompanyBranchGuarantorList();

        List<CompanyBranchGuarantor> removeList = existingGuarantorsEntity.stream()
                .filter(exist -> newGuarantorsEntity.stream()
                        .noneMatch(newGuarantor -> Objects.equals(exist.getId(), newGuarantor.getId())))
                .collect(Collectors.toList());

        List<CompanyBranchGuarantor> saveList = newGuarantorsEntity.stream()
                .filter(item -> StringUtils.isEmpty(item.getId()))
                .peek(item-> item.setVersion(1L))
                .collect(Collectors.toList());

        existingGuarantorsEntity.replaceAll(exist -> {
            newGuarantorsEntity.stream().filter(newGuarantor -> Objects.equals(exist.getId(), newGuarantor.getId()))
                    .findFirst().ifPresent(newGuarantor -> {
                        exist.setCompanyBranch(entity);
                        exist.setName(newGuarantor.getName());
                        exist.setBirthday(newGuarantor.getBirthday());
                        exist.setIdn(newGuarantor.getIdn());
                        exist.setIdnIssueDate(newGuarantor.getIdnIssueDate());
                        exist.setOwnerIdnIssueAt(newGuarantor.getOwnerIdnIssueAt());
                        exist.setOwnerIdnIssueType(newGuarantor.getOwnerIdnIssueType());
                        exist.setLastModifiedBy(newGuarantor.getLastModifiedBy().orElse(""));
                        exist.setLastModifiedDate(newGuarantor.getLastModifiedDate().orElse(LocalDateTime.now()));
                    });
            return exist;
        });

        existingGuarantorsEntity.addAll(saveList);

        entity.getCompanyBranchGuarantorList().removeAll(removeList);
        entity.getCompanyBranchGuarantorList().addAll(existingGuarantorsEntity);
    }
}
```

## Native query with Enum as parameter

````java
public enum ItemType { NORMAL, LARGE };
public interface ItemRepository extends JpaRepository<Item, Long> {
    @Query(value = "select * from items where type = :#{#type?.name()}", nativeQuery = true)
    List<Item> findByType(@Param("type") ItemType type);
}
n```

## @Version

對於資料來說，簡單理解：在資料庫並行操作時，為了保證資料的正確性，我們會做一些並行處理，主要就是加鎖。在加鎖的選擇上，常見有兩種方式：悲觀鎖和樂觀鎖。

==悲觀鎖==：簡單的理解就是把需要的資料全部加鎖，在事務提交之前，這些資料全部不可讀取和修改。
==樂觀鎖==：使用對單條資料進行版本總和檢查碼比較，來對保證本次的更新是最新的，否則就失敗，效率要高很多。在實際工作中，樂觀鎖不止在資料庫層面，其實我們在做分佈式系統的時候，為了實現分佈式系統的資料一致性，分佈式事物的一種做法就是樂觀鎖。

## @Trasient

```java
//判斷是否為新增
@Entity
@Table(name="user")
public class UserEntity implement Persistable {

	@Trasient //不會持久化
	@JsonIgnore //json 顯示忽略
	@Override
	public boolean isNew(){
		return getId() == null;
	}

}
````

## @Embeddable

```java
User
	---Long id
	---String name
	--String email
	@Embedded
	--UserDetails userDetails

@Embeddable
UserDetails
	--Date dateOfBirth
	--String dex
	--String Address
```

## List Data to Pageable

```java
List<AccountEntity> entities = null;
new PageImpl<>(entities, PageRequest.of(currentPage, length), entities.size());
```

## Pageable /w ExampleMatcher

```java
String columne = map.get("mDataProp_" + map.get("iSortCol_0"));
String direction = map.get("sSortDir_0");
int length = Integer.parseInt(map.get("iDisplayLength"));
int currentPage = Integer.parseInt(map.get("iDisplayStart")) / length;

Pageable pageable = PageRequest.of(currentPage, length, Sort.by(Direction.fromString(direction), columne));

repository.findAll(Example.of(entity,
    ExampleMatcher
    .matching()
    .withMatcher(map.get("searchType"), GenericPropertyMatchers.ignoreCase().contains())
    .withIgnorePaths("id")), pageable);
```

## Page Class

![[Snipaste_2020-01-01_23-11-40.png|350]]

## Query Example

```java
@Query("select u from User u where u.nickname like %?1%")
List<User> findByNicknameContains(String nickname);

@Query(value = "select * from user where nickname like ?1%", nativeQuery = true)
Page<User> findByNicknameStartsWith(String nickname, Pageable pageable);

@Query("SELECT o FROM OpportunityEntity o LEFT JOIN InvoiceInformationEntity i ON cast(o.id as text) = i.opportunityId where i.billType = :billType")
Page<OpportunityEntity> findByBillType(@Param("billType") String billType, Pageable pageable);

```

## ConversionFailedException

```java
@Query(select p.id,p.name,p.age from Person p)
List<Object[]> findPersonResult();
```

```java
@Query(select new com.xx.yy.PersonResult(p.id,p.name,p.age) from Person p)
List<PersonResult> findPersonResult();
```

```java
//若返回類型為POJO，必須是所有POJO的所有字段，不能只查詢某個字段
@Query(value="select * from cyd_sys_user",nativeQuery=true)
List<UserBean> find_SQL_pojo();
```

---

## Base Service && BaseRepository

```java
@Service
public class InvoiceService extends BaseServiceImp<InvoiceEntity, Long, InvoiceRepository> {
    //	@Autowired
	//	private InvoiceRepository invoiceRepository;
    @Transactional(rollbackOn = { Exception.class })
	private void save(InvoiceEntity entity) {
		entity = repository.save(entity);
	}
}
```

```java
public abstract class BaseServiceImp<T, ID extends Serializable, R extends BaseRepository<T, ID>> implements BaseService<T, ID, R> {
	@Autowired
	protected R repository;
	public List<Select2Vo> findByTxt() {
		return null;
	}
	@Override
	public R getRepository() {
		return null;
	}
}
```

```java
public interface BaseService<T, ID extends Serializable, R extends BaseRepository<T, ID>> {
	List<Select2Vo> findByTxt();
	R getRepository();
}
```

```java
@NoRepositoryBean
public interface BaseRepository<T, ID extends Serializable> extends JpaRepository<T, ID> {
	List<T> select2FindBySearchTxt(String attributeName, String searchTxt);
}
```

```java
public class BaseRepositoryImp<T, ID extends Serializable> extends SimpleJpaRepository<T, ID> implements BaseRepository<T, ID> {
	private EntityManager entityManager;
	public BaseRepositoryImp(JpaEntityInformation<T, ?> entityInformation, EntityManager entityManager) {
		super(entityInformation, entityManager);
		this.entityManager = entityManager;
	}
	@Override
	public List<T> select2FindBySearchTxt(String attributeName, String searchTxt) {
		CriteriaBuilder builder = entityManager.getCriteriaBuilder();
		CriteriaQuery<T> cQuery = builder.createQuery(getDomainClass());
		Root<T> root = cQuery.from(getDomainClass());
		cQuery.select(root).where(builder.like(root.<String>get(attributeName), "%" + searchTxt + "%"));
		TypedQuery<T> query = entityManager.createQuery(cQuery);
		return query.getResultList();
	}
}
```

## Query with join table / native SQL

```java
@Query(value = "SELECT i.id, o.\"parentName\", o.name, i.\"billId\", b.name AS \"billName\", b.\"jobTitle\", b.\"contactName\", i.\"requestHost\",i.\"currencyType\", i.amount ,i.\"proofLv1\",i.\"proofLv2\",i.\"proofLv3\",i.\"taxRate\" ,i.percentage,i.\"taxType\",i.deposit,i.\"billLanguage\",i.\"specialBillingNeeds\" FROM opportunity o LEFT JOIN invoice_information i ON CAST(o.id as text) = i.\"opportunityId\" LEFT JOIN billing b ON CAST(b.id AS TEXT) = i.\"billId\" WHERE i.\"billType\" = 'Fix Fee' ORDER BY o.id ASC LIMIT 10", nativeQuery = true)
List<iInvoiceInfo> initSelect2();
```

```java
public interface iInvoiceInfo {
	public String getId();
	public String getBillId();
	public String getBillName();
	public String getContactName();
	public String getJobTitle();
	public String getParentName();
	public String getName();
	public String getRequestHost();
	public String getCurrencyType();
	public String getAmount();
	public String getProofLv1();
	public String getProofLv2();
	public String getProofLv3();
	public String getTaxRate();
	public String getPercentage();
	public String getTaxType();
	public String getDeposit();
	public String getBillLanguage();
	public String getSpecialBillingNeeds();
}
```

## Query with List<Object[]>

```java
String query =
    "select foo, bar from  FooEntity as foo, BarEntity as bar "+
    "where  foo.someothercol = 'foo' and foo.somecol = bar.somecol";
List<Object[]> results = em.createQuery(query).getResultList();

for (Object[] fooAndBar: results) {
    FooEntity foo = (FooEntity) fooAndBar[0];
    BarEntity bar = (BarEntity) fooAndBar[1];
    //do something with foo and bar
}
```

## Example of Query

```java
TypedQuery<Account> tp = em.createQuery("SELECT a FROM Account a, User u WHERE u.account_id = a.id AND a.email = :email AND a.pwd = :pwd AND a.role = 'admin'", Account.class);
            tp.setParameter("email", this.username);
            tp.setParameter("pwd", this.password);
            Account result = tp.getSingleResult();
```

## JPA SQL in files

![[Snipaste_2019-11-15_23-11-05.png]]
![[Snipaste_2019-11-15_23-11-21.png]]

## Example - One to many

![[Snipaste_2020-06-29_09-13-21.png]]
![[Snipaste_2020-06-29_09-13-54.png]]
![[Snipaste_2020-06-29_09-14-12.png]]

==DO NOT NEED TO add Foregin Key==

~~@Column(name = "user_id")
private String user_id;~~

## Example - Many to Many

**joinColumns 指定中間表中關聯自己 ID 的字段，**
**inverseJoinColumns 表示中間表中關聯對方 ID 的字段**

==private Set<AccountEntity> accounts = new HashSet<>();==

**THIS WILL MAKE MIDDLE TABLE columm <font color="red">`primary key`</font>** (constraint)

[Link](https://stackoverflow.com/questions/38452063/unwanted-unique-constraint-when-using-manytomany-jpa-annotation)

==private List<AccountEntity> accounts;==

Notes:
// @ManyToMany(cascade = CascadeType.MERGE)
private Set<AccountEntity> accounts = new HashSet<>();

Create middle Table: ==user_accounts==

```java
@ManyToMany(cascade = CascadeType.ALL, fetch = FetchType.EAGER)
@JoinTable(
    name = "user_account",
    joinColumns = {
        @JoinColumn(
            name = "account_id",
            referencedColumnName = "id",
            foreignKey = @ForeignKey(name = "account_id")
        )
    },
    inverseJoinColumns = {
        @JoinColumn(
            name = "user_id",
            referencedColumnName = "id",
            foreignKey = @ForeignKey(name = "user_id")
        )
    },
    foreignKey = @ForeignKey(name = "account_id"),
    inverseForeignKey = @ForeignKey(name = "user_id")
)

private Set<UserEntity> users = new HashSet<>();
```

```java
  @ManyToMany(cascade = CascadeType.ALL)
  @JoinTable(
      name = "user_account",
      joinColumns = {
          @JoinColumn(
              name = "user_id",
              referencedColumnName = "id",
              foreignKey = @ForeignKey(name = "user_id")
          )
      },
      inverseJoinColumns = {
          @JoinColumn(
              name = "account_id",
              referencedColumnName = "id",
              foreignKey = @ForeignKey(name = "account_id")
          )
      },
      foreignKey = @ForeignKey(name = "user_id"),
      inverseForeignKey = @ForeignKey(name = "account_id")
  )
//  @ManyToMany(cascade = CascadeType.MERGE)
  private Set<AccountEntity> accounts = new HashSet<>();
```

## N+1 Probelm

### Service

```java
@EqualsAndHashCode(callSuper = true)
@Entity
@Table(name = "[user]")
@Data
@EntityListeners(RepostitoryListener.class)
@NamedEntityGraph(name = "UserInfo.log", attributeNodes = {@NamedAttributeNode("logs")})
public class UserEntity extends BaseEntity implements Serializable {

  ...

  @JsonManagedReference
  @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
  private List<LogEntity> logs;

  ...
}
```

### Repository

```java
@Repository
public interface UserRepository extends JpaRepository<UserEntity, Long>,
    JpaSpecificationExecutor<UserEntity> {
      ...
@EntityGraph(value = "UserInfo.log", type = EntityGraph.EntityGraphType.FETCH)
Page<UserEntity> findByAuthorityIn(List<UserType> authority, Pageable pageRequest);
      ...
}
```

## Specification - Query customized Object with dynamic where conditions from join table

```java
CriteriaBuilder cb = em.getCriteriaBuilder();
//Customized Return Object
CriteriaQuery<LoadUserResponseDto> cq = cb.createQuery(LoadUserResponseDto.class);
logger.info("=========Start===========");

//@ManyToMany between UserEntity and AccountEntity
Metamodel m = em.getMetamodel();
EntityType<UserEntity> UserEntity_ = m.entity(UserEntity.class);
Root<UserEntity> root = cq.from(UserEntity_);
//join with accounts
Join<UserEntity, AccountEntity> account = root.join("accounts", JoinType.LEFT);

//LoadUserResponseDto with constructor POJO
cq.select(cb.construct(LoadUserResponseDto.class,
                       account.get("name"),
                       root.get("id"),
                       root.get("name"),
                       root.get("email"),
                       root.get("notes"),
                       root.get("authority"),
                       root.get("enable"),
                       root.get("active")
));

List<Predicate> predicatesList = new ArrayList<>();

//WHERE condition
Predicate predicate = cb.equal(account.get("name"), "艾盟生物科技股份有限公司");
predicatesList.add(predicate);

Predicate email = cb.equal(root.get("email"), "sdfsf@gmail.com");
predicatesList.add(email);

Predicate name = cb.equal(root.get("name"), "Jay--");
predicatesList.add(name);

Predicate[] predicates = new Predicate[predicatesList.size()];
cq.where(predicatesList.toArray(predicates));

//QUERY
TypedQuery<LoadUserResponseDto> q = em.createQuery(cq);

List<LoadUserResponseDto> userEntities = q.getResultList();
System.out.println("userEntities.size() = " + userEntities.size());
userEntities.forEach(o -> {
  logger.info(" o = {} ", new Gson().toJson(o));
});
```

```java
@Entity
@Table(name = "[user]")
public class UserEntity extends PrimaryBaseEntity implements Serializable {

  private Long id;
  private UserType authority;
  private Boolean active;
  private Boolean enable = Boolean.TRUE;
  private String name;
  private String email;
  private String notes;
  private String pwd;
  private String uuid;
  private String alias;

  private Set<OrgEntity> org = new HashSet<>();
  private Set<AccountEntity> accounts = new HashSet<>();
  private Set<ProjectEntity> projects = new HashSet<>();

  ...

  @BatchSize(size = 20)
  @JsonIgnore
  @ManyToMany(cascade = {CascadeType.MERGE, CascadeType.PERSIST}, fetch = FetchType.LAZY)
  @Fetch(FetchMode.SUBSELECT)
  @JoinTable(
      name = "user_account",
      joinColumns = {
          @JoinColumn(
              name = "user_id",
              referencedColumnName = "id",
              foreignKey = @ForeignKey(name = "user_id")
          )
      },
      inverseJoinColumns = {
          @JoinColumn(
              name = "account_id",
              referencedColumnName = "id",
              foreignKey = @ForeignKey(name = "account_id")
          )
      },
      foreignKey = @ForeignKey(name = "user_id"),
      inverseForeignKey = @ForeignKey(name = "account_id")
  )
  public Set<AccountEntity> getAccounts() {
    return accounts;
  }

}
```

```java
@Entity
@Table(name = "account")
public class AccountEntity extends PrimaryBaseEntity implements Serializable {

  private static final long serialVersionUID = 1L;

  private Long id;
  private String code;
  private String name;
  private Set<UserEntity> users = new HashSet<>();

  @ManyToMany(cascade = {CascadeType.MERGE, CascadeType.PERSIST}, fetch = FetchType.LAZY)
  @JoinTable(
    name = "user_account",
    joinColumns = {@JoinColumn(name = "user_id")},
    inverseJoinColumns = {@JoinColumn(name = "account_id")}
  )
  public Set<UserEntity> getUsers() {
    return users;
  }

  ...

}
```

## @ElementCollection

![image-20201204115315300](https://i.imgur.com/6EPsota.png)

## @EnumSet

![image-20201204115501163](https://i.imgur.com/8oJdeaq.png)

## Execute SQL Script

![Snipaste_2020-11-13_15-50-03](https://i.imgur.com/JNOTsCu.png)

## Query Example with boolean type

```java
Users users = new Users();
users.setId(12);
Example<Users> example = Example.of(users);
Page<Users> pageUsers = userRepository.findAll(example, page);
```

==You are using "boolean" primitive type, so your "Example" object has implicitly the "false" value.. you can use "Boolean" type to fix it==

## Query In Condition

<img src="https://i.imgur.com/UlGQzPv.png" alt="image-20201224100225936" style="zoom:50%;" />

<img src="https://i.imgur.com/sXJEmr6.png" alt="image-20201224100259370" style="zoom:50%;" />

```sql
select distinct g.id, g.name
from groups g
         join report r on g.id = any(string_to_array(r.groups,',')::bigint[])
```

<img src="https://i.imgur.com/sXJEmr6.png" alt="image-20201224100259370" style="zoom:50%;" />

```sql
SELECT g.id,g.school_ids,string_agg(s.locality_name,',')
FROM goods g LEFT JOIN school s ON s.id = ANY(STRING_TO_ARRAY(g.school_ids, ','))
GROUP BY g.id,g.school_ids
ORDER BY g.id ASC
```

<img src="https://i.imgur.com/Vzegu5Y.png" alt="image-20201224100701968" style="zoom:50%;" />

```sql
select r.name, r.individuals, string_agg(g.member,',') from report r
left join groups g on g.id = any(string_to_array(r.groups,',')::bigint[])
group by r.name, r.individuals
```

<img src="https://i.imgur.com/DnfN99l.png" alt="image-20201224105004268" style="zoom:50%;" />

```sql
select r.name, concat_ws(',',string_agg(g.member, ','),r.individuals)
from report r
         left join groups g on g.id = any (string_to_array(r.groups, ',')::bigint[])
group by r.name, r.individuals

```

Sql in JPA

```sql
any(CAST(string_to_array(r.groups, ',') AS BIGINT[]))
```

## JPA UPDATE - optimistic locking

> ```
> org.springframework.orm.ObjectOptimisticLockingFailureException: Object of class [] with identifier: optimistic locking failed; nested exception is org.hibernate.StaleObjectStateException: Row was updated or deleted by another transaction (or unsaved-value mapping was incorrect) :
> ```

```java
if (0 < entity.getMerchantMemoList().size()) {
    //db has memos
    Optional<MerchantDetail> dbData = merchantRepository.findById(entity.getId());
    if (dbData.isPresent()) {
        // memo exist in db
        List<MerchantMemoList> memoList = new ArrayList<>(dbData.get().getMerchantMemoList());
        if (!memoList.isEmpty()) {
            MerchantDetail md = memoList.get(0).getMerchant();
            List<MerchantMemoList> MerchantMemoLists = new ArrayList<>();
            for (MerchantMemoList requestMemo : entity.getMerchantMemoList()) {
                MerchantMemoList mdl = new MerchantMemoList();
                mdl.setMerchant(md);
                mdl.setMemo(requestMemo.getMemo());
                mdl.setMemoDate(requestMemo.getMemoDate());
                MerchantMemoLists.add(mdl);
            }
            entity.getMerchantMemoList().clear();
            entity.getMerchantMemoList().addAll(MerchantMemoLists);
        }
    }
}

```

## Persist

> ```
> 原來merge()也有persist()的作用！
> persist會把傳進去的實體放到持久化上下文中，此時如果持久化上下文中有了這個實體，就會拋出javax.persistence.EntityExistsException，沒有的話事務提交的時候把那個對象加進數據庫中，如果數據庫中已經存在了那個對象（那一行），就會拋出com.mysql.jdbc.exceptions.jdbc4.MySQLIntegrityConstraintViolationException；
> ```

- 如果 persist 的是一個受管實體（即已經在上下文中），就不會拋出異常。
- 如果 persist 的是一個游離實體（即上下文中沒有它），而上下文中又沒有它的受管版本，數據庫中也沒有，也不會拋出異常，而會把這個實體寫進數據庫中。
- 如果 persist 的是一個游離實體（即上下文中沒有），而上下文中又沒有它的受管版本，數據庫卻有這個實體，那麼 EntityManager 在 persist 它的時候不會拋出異常，但是事務提交的時候就會拋出異常：
    Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLIntegrityConstraintViolationException: Duplicate entry '7' for key 1；
- 如果 persist 的是一個游離實體（即上下文中沒有），而上下文中卻有它的受管版本，數據庫中又沒有這個實體，那麼還是不會拋出異常，而是把它的受管版本加進去（不是那個游離的，是那個受管的！）（即，這種情況 persist 和沒 persist 是一樣的！）。
- 如果 persist 的是一個游離實體（即上下文中沒有），而上下文中卻有它的受管版本，數據庫中也有了這個實體，那麼 EntityManager 在 persist 它的時候就會拋出異常：javax.persistence.EntityExistsException

## @SubSelect

```java
@Entity
@Immutable
@Subselect("SELECT column1, column2 FROM my_view")
@Synchronize({ "table1", "table2" })
public class MyViewEntity {
    @Id
    private Long id;
    private String column1;
    private String column2;

    // getters and setters
}
```

在上面的示例中，使用了`@Subselect`註解指定了該實體對應的 SQL 檢視的查詢語句，`@Immutable`註解表示該實體是唯讀的，並且使用`@Synchronize`註解來指定與檢視相關的表。通過這樣的方式，可以將實體對象與 SQL 檢視進行對應，並且可以執行查詢操作。

## @Audited

```yaml
#With Under scores
org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy

spring:
    jpa:
        show-sql: true
        properties:
            hibernate:
                #                allow_update_outside_transaction: true
                format_sql: true
        hibernate:
            ddl-auto: none
            naming:
                physical-strategy: org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy
        generate-ddl: false
    application:
        name: cardlite-gui
    datasource:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: oracle.jdbc.OracleDriver
        url: jdbc:oracle:thin:@127.0.0.1:1521:iasLocal
        username: iasuser
        password: iasuser
```

## JPA Transaction Manager

`allow_update_outside_transaction: true`

## LOG SQL INSERT VALUE

```yaml

spring:  
    jpa:  
        show-sql: true

logging:
    level:
        org.hibernate.orm.jdbc.bind: TRACE
```
