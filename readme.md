Spring Web Initializr
==========
[![release-3.0.0][shield-release]](#)
[![build-passing][shield-build]](#)
[![code-coverage-100%][shield-coverage]](#)  
[![jdk-8][shield-jdk]](#)
[![spring-boot-2.0.0.RELEASE][shield-spring]](#)
[![MIT licensed][shield-license]](#)

Spring Web Initializr _(will be referred to as **SWI** from now on)_ is a library that will help you easily create Web Apps with Spring Boot.  
It was mainly developed in order to support [Swip (Spring Web Initializr Plugin)](https://plugins.jetbrains.com/plugin/12239-swip-spring-web-initializr-) built for IntelliJ IDEA, but can be obviously used independently.
However, you will better understand the usage and the purpose of the library, if you choose to use Swip first.

Table of Contents
-----------------
  * [Description](#Description)
  * [Example](#Example)
  * [Contributing](#Contributing)
  * [License](#License)
  
Description
-----------

SWI is providing interfaces or abstract classes that contain the base logic for the Create, Read, Update & Delete  operations of a ResourcePersistable.  
It is assumed, that the user of the library is going to use a template engine compatible with Spring, but there is no restriction as to which one.

_ResourcePersistable\<ID\>_
* Meant to be implemented by a JPA @Entity (e.g. User)  
* **ID** stands for the class of the field representing the primary key of the ResourcePersistable entity (e.g. Long)
* There is only a single method to be implemented, which should be returning the primary key field  
    ```
    ID getResourcePersistableId();
    ```

_ResourcePersistableController\<R extends ResourcePersistable\<ID\>, ID extends Serializable, RF, RSF\>_
* Meant to be extended by the Spring @Controller for the corresponding ResourcePersistable entity (e.g. UserController)
* **R extends ResourcePersistable\<ID\>** stands for the ResourcePersistable (e.g. User)
* **ID extends Serializable** stands for the class of the field representing the primary key of the entity (e.g. Long)
* **RF** stands for the ResourcePersistableForm, a class that should contain all the fields required in order to create a ResourcePersistable (e.g. UserForm)
  * Might as well be the ResourcePersistable itself
* **RSF** stands for the ResourcePersistableSearchForm, that should contain all the fields in order to search for a ResourcePersistable (e.g. UserSearchForm)
  * Might as well be the ResourcePersistable itself
* The methods that should be implemented are the ones that provide the endpoints & the resources' paths, as well as the transformation methods from a ResourcePersistable to a ResourcePersistableForm and vice versa
  
_ResourcePersistableService\<R extends ResourcePersistable\<ID\>, ID extends Serializable, RSF\>_
* Meant to be extended by the Spring @Service for the corresponding ResourcePersistable (e.g. UserService)
* **R extends ResourcePersistable\<ID\>** stands for the ResourcePersistable (e.g. User)
* **ID extends Serializable** stands for the class of the field representing the primary key of the entity (e.g. Long)
* **RSF** stands for the ResourcePersistableSearchForm, a class that should contain all the fields in order to search for a ResourcePersistable (e.g. UserSearchForm)
  * Might as well be the ResourcePersistable itself  


Example
-----
First of all add the dependency in your project:

_Maven_
```xml
<dependency>
    <groupId>io.github.orpolyzos</groupId>
    <artifactId>spring-web-initializr</artifactId>
    <version>3.0.0</version>
</dependency>
```

In this simplified example the ResourcePersistable will be the User entity and we will reuse the same class for ResourcePersistableForm and ResourcePersistableSearchForm. 

<details>
    <summary>pom.xml</summary>
 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>ore.plugins.swi</groupId>
    <artifactId>swi-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>swi-demo</name>
    <description>Demo project for Spring Web Initializr</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.github.orpolyzos</groupId>
            <artifactId>spring-web-initializr</artifactId>
            <version>1.1.0</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```
</details>

<details>
    <summary>ResourcePersistable</summary>

```java
@Entity(name = "user")
public class User implements ResourcePersistable<Long> {

    @Id
    @Column(name = "id", nullable = false)
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "first_name", nullable = false)
    private String firstName;

    @Column(name = "last_name", nullable = false)
    private String lastName;

    @Column(name = "email", nullable = false, unique = true)
    private String email;


    @Override
    public Long getResourcePersistableId() {
        return this.id;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```
</details>

<details>
    <summary>ResourcePersistableRepository</summary>

```java
@Repository
public interface UserResourcePersistableRepository extends CrudRepository<User, Long> {}
```
</details>

<details>
    <summary>ResourcePersistableService</summary>

```java
@Service
public class UserResourcePersistableService extends ResourcePersistableService<User, Long, User> {

    private final CrudRepository<User, Long> userResourcePersistableRepository;

    @Autowired
    public UserResourcePersistableService(CrudRepository<User, Long> userResourcePersistableRepository) {
        super(userResourcePersistableRepository);
        this.userResourcePersistableRepository = userResourcePersistableRepository;
    }
}
```
</details>

<details>
    <summary>ResourcePersistableController</summary>

```java
@Controller
public class UserResourcePersistableController extends ResourcePersistableController<User, Long, User, User> {
      
    private final ResourcePersistableService<User, Long, User> userResourcePersistableService;

    @Autowired
    public UserResourcePersistableController(ResourcePersistableService<User, Long, User> userResourcePersistableService) {
        super(userResourcePersistableService);
        this.userResourcePersistableService = userResourcePersistableService;
    }

    @Override
    @GetMapping("/users")
    public String getResourcePersistableBaseView(Model model) {
        return super.getResourcePersistableBaseView(model);
    }

    @Override
    @PostMapping("/users")
    public String createResourcePersistable(@Valid @ModelAttribute("userForm") User resourcePersistableForm, BindingResult bindingResult, Model model, RedirectAttributes redirectAttributes) {
        return super.createResourcePersistable(resourcePersistableForm, bindingResult, model, redirectAttributes);
    }

    @Override
    @PostMapping("users/{resourcePersistableId}/delete")
    public String deleteResourcePersistable(@PathVariable("resourcePersistableId") Long resourcePersistableId, Model model) {
        return super.deleteResourcePersistable(resourcePersistableId, model);
    }

    @Override
    @GetMapping("users/{resourcePersistableId}/edit")
    public String getResourcePersistableEditView(@PathVariable("resourcePersistableId") Long resourcePersistableId, Model model) {
        return super.getResourcePersistableEditView(resourcePersistableId, model);
    }

    @Override
    @PostMapping("users/{resourcePersistableId}/edit")
    public String editResourcePersistable(@PathVariable("resourcePersistableId") Long resourcePersistableId, @Valid @ModelAttribute("userForm") User resourcePersistableForm, BindingResult bindingResult, Model model, RedirectAttributes redirectAttributes) {
        return super.editResourcePersistable(resourcePersistableId, resourcePersistableForm, bindingResult, model, redirectAttributes);
    }

    @Override
    @PostMapping("users/search")
    public String searchResourcePersistablesBy(@Valid @ModelAttribute("userSearchForm") User resourcePersistableSearchForm, BindingResult bindingResult, Model model, RedirectAttributes redirectAttributes) {
        return super.searchResourcePersistablesBy(resourcePersistableSearchForm, bindingResult, model, redirectAttributes);
    }

    @Override
    protected String getResourcePersistableBaseUri() { return "/users"; }

    @Override
    protected String getResourcePersistableBaseViewPath() { return "/user/users"; }

    @Override
    protected String getResourcePersistableEditViewPath() { return "/user/edit-user"; }

    @Override
    protected String getResourcePersistableFormHolder() { return "userForm"; }

    @Override
    protected String getResourcePersistableSearchFormHolder() { return "userSearchForm"; }

    @Override
    protected String getResourcePersistableListHolder() { return "userList"; }

    @Override
    protected User resourcePersistableFormToResourcePersistable(User user) {
        return user;
    }

    @Override
    protected User resourcePersistableToResourcePersistableForm(User user) {
        return user;
    }
}
```
</details>

Contributing
------------
To contribute to Spring Web Initializr, follow the instructions in our [contributing guide](/contributing.md).

License
-------
Spring Web Initializr is licensed under the [MIT](/license.md) license.  
Copyright &copy; 2019, Orestes Polyzos

[shield-release]: https://img.shields.io/badge/release-3.0.0-blue.svg
[shield-build]: https://img.shields.io/badge/build-passing-brightgreen.svg
[shield-coverage]: https://img.shields.io/badge/coverage-0%25-red.svg
[shield-jdk]: https://img.shields.io/badge/jdk-8-blue.svg
[shield-spring]: https://img.shields.io/badge/spring-2.0.0-blue.svg
[shield-license]: https://img.shields.io/badge/license-MIT-blue.svg
