{
:title "jackson常用注释"
:layout :post
:tags ["json" "jackson"]
}
# Table of Contents

1.  [@JsonRootName](#org87fc69d)


<a id="org87fc69d"></a>

# @JsonRootName

@JsonRootName用于控制类被序列化为json时的根元素的键。

比如类声明如下：

    @JsonRootName("myPojo")
    public class TestPojo {
      private String name;
      public String getName() {
        return name;
      }
    
      public void setName(String name) {
        this.name = name;
      }
    }

对该类的实例进行json编码后，格式是下面这样：

    {
        "myPojo" : {
            "name" : "testName"
        }
    }

