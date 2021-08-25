##XML序列化
所谓XML 即是用来存储对象的成员变量的一个XML文件

要将对象的成员变量转化为XML格式，需要使用**Xml序列化器（Xmlserializer类）**，序列化之后的对象以XML文件的形式保存。

###XML序列化过程描述
例子如下：

有一个Person类，代码如下：

	public class Person {  
    private int id;  
    private String age;  
    private String name;  

    public int getId() {  
       return id;  
  	}  

  	public void setId(int id) {
		this.id = id;  
 	}  
	
 	public String getAge() {  
 	     return age;  
 	}  
	
 	public void setAge(String age) {  
  	     this.age = age;  
  	}  
	
  	public String getName() {  
  	     return name;  
 	}  
	
 	public void setName(String name) {  
 	     this.name = name;  
 	}  
	
 	  @Override  
 	public String toString() {  
  	   return "Person [id=" + id + ", age=" 	+ age + ", name=" + name + "]";  
  		}  

	}

该Person类对应的有若干个实例对象，如：{id=0;name=”张三”;age=20}，{id=1;name=”李四”;age=19}

将其XML序列化的过程，就是将实例对象以XML格式存储在XML文件当中。