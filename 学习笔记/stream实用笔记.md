### list去重（对象去重）

```java
//去掉重复的资质类型
List<JSONObject> distinctCertJsonList = certJsonList.stream().collect(Collectors.collectingAndThen(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(json -> String.valueOf(json.get("CertTypeNum"))))),ArrayList::new));
```

### 使用stream实现两个list集合合并成一个(对象属性的合并)

```java
import java.util.*;
import java.util.stream.Collectors;

/**
* @author qzz
*/
public class ListTest {

  public static void main(String[] args) {
      mergeList();
  }
  /**
   * @Description: 合并两个list<map>,并将userId相同的其它属性合并
   * @Title: mergeList
   * @param: @return
   * @return: List<Map<String,Object>>
   * @throws
   */
  public static List<Map<String,Object>> mergeList(){
      //构建List集合1
      List<Map<String,Object>> list1 = new ArrayList<>();
      Map<String,Object> data=new HashMap<>();
      data.put("userId","100001");
      data.put("userName","唐僧");
      list1.add(data);

      data=new HashMap<>();
      data.put("userId","100002");
      data.put("userName","八戒");
      list1.add(data);

      data=new HashMap<>();
      data.put("userId","100003");
      data.put("userName","悟空");
      list1.add(data);

      data=new HashMap<>();
      data.put("userId","100004");
      data.put("userName","沙僧");
      list1.add(data);

		//构建List集合2
      List<Map<String,Object>> list2 = new ArrayList<>();
      data=new HashMap<>();
      data.put("userId","100001");
      data.put("gender","男");
      data.put("age",20);
      list2.add(data);

      data=new HashMap<>();
      data.put("userId","100002");
      data.put("gender","雄");
      data.put("age",1000);
      list2.add(data);

      data=new HashMap<>();
      data.put("userId","100003");
      data.put("gender","雄");
      data.put("age",600);
      list2.add(data);

      data=new HashMap<>();
      data.put("userId","100004");
      data.put("gender","男");
      data.put("age",800);
      list2.add(data);

		//使用stream流把list1和list2根据属性userId合并一个list集合
      List<Map<String, Object>> list = list1.stream().map(m -> {
          list2.stream().filter(m2-> Objects.equals(m.get("userId"),m2.get("userId"))).forEach(m2-> {
              m.put("gender",m2.get("gender"));
              m.put("age",m2.get("age"));
          });
          return m;
      }).collect(Collectors.toList());

      for(Map<String, Object> map:list){
          System.out.println(map.toString());
      }
      return list;
  }
}
```

