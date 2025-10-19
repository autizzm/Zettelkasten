
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
tomcat.getConnector();  
tomcat.setBaseDir("tomcat-base-dir");
tomcat.setHostname("localhost");
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

tomcat.addContext(Host host, String contextPath, String baseDirectory)
Context context = tomcat.addWebapp(host, "/identity", webAppLocation);
```

- **contextPath** - webapp's name (путь в url)
- **webAppLocation** - путь к web.xml

#### Настройка Context 

Добавление классов в context:

```java
File classesDir = new File("target/classes");  
WebResourceRoot resources = new StandardRoot(ctx);  
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
tomcat.addSeervlet(String contextPaath, String servletName, Servlet servlet);
tomcat.addServletMappingDecoded(String urlPattern, String servletName);
```


### start/stop/destroy

```java
tomcat.start();

tomcat.stop();

tomcat.destroy();
tomcat.getServer.await(); -> //заставляет текущий thread ждать, пока tomcat завершится
```



----
#### [[Embedded Tomcat - Flashcards|Link to flashcards]]



---
### References:

