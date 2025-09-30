### Sample
```java
String sql = "select name from jdbc_student where id = ?";
String name = jdbcTemplate.queryForObject(sql, String.class, 1);
System.out.println("name: " + name);		// name: lizhuo
```

```java
String sql = "select * from jdbc_student where id = ?";
RowMapper<Student> rowMapper = new BeanPropertyRowMapper<Student>(Student.class);
// RowMapper<Student> rowMapper = (rs, rowNum) -> new Student(rs);
Student student = jdbcTemplate.queryForObject(sql, rowMapper, 1);
```

```java
String sql = "select name from jdbc_student where age between ? and ? ";
List<String> names = jdbcTemplate.queryForList(sql, String.class, 11, 20);
```

```java
//List<Map>
sql = "select * from jdbc_student where age between ? and ? ";
List<Map> maps = jdbcDao.queryForList(sql, Map.class, 11, 20);
System.out.println(maps);
//[{id=2, name=Zara, age=11}, {id=4, name=Ayan, age=15}, {id=5, name=AA, age=12}]
```

```java
public class BaseJdbcDao {
private JdbcTemplate jdbcTemplate;

 public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
  this.jdbcTemplate = jdbcTemplate;
 }

 @SuppressWarnings("unchecked")
public <T> List<T> queryForList(String sql, Class<T> tClass, Object... args) {
	 RowMapper<T> rowMapper = null;
	 if (Map.class.isAssignableFrom(tClass)) {
		 rowMapper = (RowMapper<T>) new ColumnMapRowMapper();
	 } else if (String.class.equals(tClass) || Integer.class.equals(tClass) 
				|| Long.class.equals(tClass)) {
		 rowMapper = new SingleColumnRowMapper<T>(tClass);
	 } else {
		 rowMapper = new BeanPropertyRowMapper<T>(tClass);
	 }
	 List<T> list = jdbcTemplate.query(sql, rowMapper, args);
	 return list;
 }

 public <T> T queryForObject(String sql, Class<T> tClass, Object... args) {
  List<T> list = queryForList(sql, tClass, args);
	 return list == null || list.isEmpty() ? null : list.get(0);
 } 
}
```

### In Condition

```java
@Autowired
@Qualifier("primaryJdbcTemplate")
private JdbcTemplate primaryTemplate;

NamedParameterJdbcTemplate namedParameterJdbcTemplate = new NamedParameterJdbcTemplate(primaryTemplate);
String sql = "select * from google_drive_permission where folder_id in(:ids)";
Map idsMap = Collections.singletonMap("ids", folderIds);
return namedParameterJdbcTemplate.query(sql, idsMap, BeanPropertyRowMapper.newInstance(DrivePermission.class));
```

### Batch Update

```java
final RowMapper<DriveBean> ROW_MAPPER_FOLDER = (rs, rowNum) -> new DriveBean(rs);
List<DriveBean> lists = primaryTemplate.query(SQL_ALL_FOLDER_W_ID, new Object[] { folderId }, ROW_MAPPER_FOLDER);
```

```java
@Autowired
private NamedParameterJdbcTemplate namedParameterJdbcTemplate;

public void insertBatchUseSqlParameterSourceUtils(final List<Customer> customers) {
    SqlParameterSource[] params = SqlParameterSourceUtils.createBatch(customers.toArray());
    namedParameterJdbcTemplate.batchUpdate("INSERT INTO CUSTOMER (NAME, AGE) VALUES (:name, :age)", params);
}

@Transactional
public int[] create(List<Job> jobs) {
    SqlParameterSource[] params = SqlParameterSourceUtils.createBatch(jobs.toArray());
    return jdbc.batchUpdate("insert into Jobs (id, name, text, email) values (:id, :name, :text, :email)", params);
}

```
