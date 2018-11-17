{
:title "jackson编码使用getter方法"
:layout :post
:tags ["json" "jackson"]
}

# Table of Contents



jackson使用getter方法进行json序列化。

例如：下面的main方法。

    @JsonIgnoreProperties(ignoreUnknow = true)
    public class TestModle {
      private List<String> listsStr;
    
      public void setLists(List<String> lst) {
        this.listsStr = lst;
      }
    
      public List<String> getLists() {
        return this.listsStr;
      }
    }
    
    class Main {
      public static void main(String[] args) {
        ObjectMapper mapper = new ObjectMapper();
        TestModel tm = new TestModel();
        String result = mapper.writerWithDefaultPrettyPrinter().writeValueAsString(tm);
        System.out.println(result);
      }
    }

输出：

    {
        "lists" : null
    }

