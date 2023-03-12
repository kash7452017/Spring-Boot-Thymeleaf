## Spring Boot - Thymeleaf
>參考網址：https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html
>
>Thymeleaf 是一種適用於 Web 和獨立環境的現代服務器端 Java 模板引擎，能夠處理 HTML、XML、JavaScript、CSS 甚至純文本。
>
>* Thymeleaf 在有網絡和無網絡的環境下皆可運行，即它可以讓美工在瀏覽器查看頁面的靜態效果，也可以讓程序員在服務器查看帶數據的動態頁面效果。這是由於它支持html 原型，然後在html 標籤裡增加額外的屬性來達到模板+數據的展示方式。瀏覽器解釋html 時會忽略未定義的標籤屬性，所以thymeleaf 的模板可以靜態地運行；當有數據返回到頁面時，Thymeleaf 標籤會動態地替換掉靜態內容，使頁面動態顯示。
>
>* Thymeleaf 開箱即用的特性。它提供標準和spring標準兩種方言，可以直接套用模板實現JSTL、OGNL表達式效果，避免每天套模板、改jstl、改標籤的困擾。同時開發人員也可以擴展和創建自定義的方言。
>* Thymeleaf 提供spring標準方言和一個與SpringMVC 完美集成的可選模塊，可以快速的實現表單綁定、屬性編輯器、國際化等功能

### 以下演示基本Thymeleaf Demo頁面
**添加Thymeleaf依賴項目**
```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

**DemoController.java程式碼，此範例控制器簡單透過Model注入數據並轉向Thymeleaf頁面顯示**
```
@Controller
public class DemoController {
	
	// Create a mapping for "/hello"
	@GetMapping("/hello")
	public String sayHello(Model theModel) {
		theModel.addAttribute("theDate", new java.util.Date());
		
		// We have Thymeleaf dependency in Maven POM
		// Spring Boot will auto-configure to use Thymeleaf
		return "helloworld";
		// src/main/resources/templates/helloworld.html
	}
}
```

**helloworld.html程式碼，添加thymeleaf命名空間允許使用thymeleaf表達式，同時我們簡單顯示時間資訊，`${theDate}`此數據與控制器中屬性名稱相同，在控制器中透過Model添加數據，在thymeleaf模板上即可訪問此數據**
```
<!DOCTYPE HTML>
<html xmlns:th="http://thymeleaf.org">

<head>
	<title>Thymeleaf Demo</title>
	
	<!-- reference CSS file -->
	<link rel="stylesheet"
		  th:href="@{/css/demo.css}" />
</head>

<body>

	<p th:text="'Time on the server is ' + ${theDate}" class="funny" />

</body>

</html>
```
![image](https://user-images.githubusercontent.com/101872264/224470797-f4025cf3-abc8-41be-ac04-11f5db56d21d.png)

### 以下演示使用Thymeleaf顯示員工列表

**Employee.java程式碼，創建實體類別，包含基本屬性以及Getter/Setter方法等**
```
@Entity
@Table(name="employee")
public class Employee {

	// define fields
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="first_name")
	private String firstName;
	
	@Column(name="last_name")
	private String lastName;
	
	@Column(name="email")
	private String email;
	
	// define constructors
	public Employee() {
		
	}
	
	public Employee(int id, String firstName, String lastName, String email) {
		this.id = id;
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
	}

	public Employee(String firstName, String lastName, String email) {
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
	}
	
	// define getter/setter
	以下省略...
}
```

**EmployeeController.java程式碼，手動添加員工數據，並配置映射方法，透過模組添加數據使Thymeleaf頁面可訪問數據**
```
@Controller
@RequestMapping("/employees")
public class EmployeeController {

	// load employee data
	
	private List<Employee> theEmployees;
	
	@PostConstruct
	private void loadData() {
		
		// create employee
		Employee emp1 = new Employee(1, "Leslie", "Andrews", "leslie@luv2code.com");
		Employee emp2 = new Employee(2, "Emma", "Baumgarten", "emma@luv2code.com");
		Employee emp3 = new Employee(3, "Avani", "Gupta", "avani@luv2code.com");
		
		// create the list
		theEmployees = new ArrayList<>();
		
		// add to the list
		theEmployees.add(emp1);
		theEmployees.add(emp2);
		theEmployees.add(emp3);
	}
	
	// add mapping for "/list"	
	@GetMapping("/list")
	public String listEmployees(Model theModel) {
		
		// add to the spring model
		theModel.addAttribute("employees", theEmployees);
		
		return "list-employees";
	}
}
```

**list-employees.htmlt程式碼，使用thymeleaf模板創建顯示頁面，並加入Bootstrap更改頁面樣式**
```
<!DOCTYPE HTML>
<html lang="en" xmlns:th="http://www.thymeleaf.org">

<head>
	<!-- Required meta tags -->
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    
    <!-- Bootstrap CSS -->
    <link rel="stylesheet"
      href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css">
  </head>
</head>

<body>

<div class="container">

	<h3>Employee Directory</h3>
	<hr>
	
	<table class="table table-borderd table-striped">
		<thead class="thead-dark">
			<tr>
				<th>FristName</th>
				<th>listName</th>
				<th>Email</th>
			</tr>
		</thead>
		
		<tbody>
			<tr th:each="tempEmployee : ${employees}">
				<td th:text="${tempEmployee.firstName}" />
				<td th:text="${tempEmployee.lastName}" />
				<td th:text="${tempEmployee.email}" />
			</tr>
		</tbody>
	
	</table>
	
</div>

</body>

</html>
```
![image](https://user-images.githubusercontent.com/101872264/224471669-f9d303d2-981c-4c6f-97c8-e0ccd946e442.png)
