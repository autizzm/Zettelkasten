
18-10-2025 20:26

Status: #baby 

Tags: [[Tomcat]] [[Java+]]

---
# Embedded Tomcat

#### Создание сервера:

```java
Tomcat tomcat = new Tomcat();
```

#### Настройка сервера

```java
tomcat.setPort(8080);
tomcat.setBaseDir("tomcat-base-dir");
tomcat.setHostname("localhost");
```

#### Создание коннектора

Сетевой коннектор для подключений. Без него, к серверу нельзя будет подключиться.

Он создаётся ==lazily== => достаточно вызвать getter, чтобы он инициализировался.

```java
tomcat.getConnector();
```

#### Добавление web приложения (context):

> [!note] **Про WebApp context**
> Он задаётся с помощью `web.xml`
> И в него можно добавлять информацию:
> - константы
> - классы
> - прочую хуйню
> 




```java
Host host = tomcat.getHost();
String webAppLocation = new File("src/main/webapp/").getAbsolutePath();

Context context = tomcat.addWebapp(host, "/identity", webAppLocation);
```

- **contextPath** - webapp's name (путь в url)
- **webAppLocation** - путь к webapp root (можно через  [[Classpath - Java|classpath]])
По сути есть два варианта:
- `"src/main/webapp/"`
- `"target/<your_app_name>"`
 
#### Настройка Context 

Добавление классов в context:

```java
File classesDir = new File("target/classes");  
WebResourceRoot resources = new StandardRoot(context);  
resources.addPreResources(new DirResourceSet(  
        resources,  
        "/WEB-INF/classes",  
        classesDir.getAbsolutePath(),  
        "/"  
));  
context.setResources(resources);
```

Установка маппинга via Java code:

```java
tomcat.addServlet(String contextPaath, String servletName, Servlet servlet);
tomcat.addServletMappingDecoded(String urlPattern, String servletName);
```

> [!note] 
> При работе со Spring MVC добавлять DispatcherServlet вручную - НЕ НАДО. Он его сам найдёт.


### start/stop/destroy

```java
tomcat.start();

tomcat.stop();

tomcat.destroy();
tomcat.getServer.await(); -> //заставляет текущий thread ждать, пока tomcat завершится
```



### Пример работы

(Для Spring MVC добавлен DispatcherServlet)

> [!warning]
> Имя dispatcher занято `RequestDispatcher`.
> А у конторллеров спринга есть свой `DispatcherServlet` => добавляем его через `tomcat.addServlet(...)` но даём имя не `dispatcher` - любое другое, чтоб не клэшилось с Tomcat-овским.


```java
Tomcat tomcat = new Tomcat();  
  
tomcat.setPort(8080);  
tomcat.setHostname("localhost");  
tomcat.setBaseDir("tomcat-base-dir");  
tomcat.getConnector();  
Host host = tomcat.getHost();  
WebappLoader webappLoader = new WebappLoader();  
webappLoader.setDelegate(true);  
  

String webAppLocation = new File("target/first-spring-mvc-app").getAbsolutePath();  

  
Context context = tomcat.addWebapp(host,"/myapp", webAppLocation);  
context.setLoader(webappLoader);  
  
Tomcat.addServlet(context, "dispatcherServlet", new DispatcherServlet());  
context.addServletMappingDecoded("/", "dispatcherServlet");  
  
File classesDir = new File("target/classes");  
WebResourceRoot resources = new StandardRoot(context);  
resources.addPreResources(new DirResourceSet(resources, "/WEB-INF/classes", classesDir.getAbsolutePath(), "/" ));  
context.setResources(resources);  
  
tomcat.start();  
  
System.out.println("running");  
  
tomcat.getServer().await();
```

----
#### [[Embedded Tomcat - Flashcards|Link to flashcards]]



---
### References:

