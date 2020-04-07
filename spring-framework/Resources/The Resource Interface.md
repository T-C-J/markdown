#### Spring 资源管理

---

> 虽然java有自己的uri可以访问底层资源，但是用于访问从类路径或上下文里获取资源是不可行的，



##### Resource Interface

~~~java
public interface Resource extends InputStreamSource {
    boolean exists();
    boolean isOpen();
    URL getURL() throws IOException;
    File getFile() throws IOException;
    Resource createRelative(String relativePath) throws IOException;
    String getFilename();
    String getDescription();
}
~~~

~~~java
public interface InputStreamSource {
    InputStream getInputStream() throws IOException;
}
~~~

