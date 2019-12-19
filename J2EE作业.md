# 1.数据库表的创建

## 1.1 教师

![image-20191217114642417](C:%5CUsers%5C11979%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191217114642417.png)

## 1.2 班级

![image-20191217114740840](C:%5CUsers%5C11979%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191217114740840.png)

## 1.3 部门

![image-20191217114901841](C:%5CUsers%5C11979%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191217114901841.png)

## 1.4 员工

![image-20191217115008695](C:%5CUsers%5C11979%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191217115008695.png)

## 1.5 单表用户

![image-20191217115334255](J2EE%E4%BD%9C%E4%B8%9A.assets/image-20191217115334255.png)



# 2.书写java代码测试

## 2.1 单表练习

用户实体类：

```java
@Alias("Tuser")
public class Tuser {
    private int id;
    private String userName;
    private String note;
getters   and  setters
```

dao接口：

```java
@Repository
@Mapper
public interface TuserDao {

    public Tuser getTUser(int id,
                          String userName,
                          String note);

}
```

service:

```java
@Service
public class TuserService {
    @Autowired
    private TuserDao td;
    public Tuser getTUser(int id,
                          String userName,
                          String note){
        return td.getTUser(id,userName,note);
    }
}

```

controller:

```java
@Controller
public class TuserController {
    @Autowired
    private TuserService ts;

    @GetMapping("/getTUser")
    @ResponseBody
    public Tuser getTUser(@RequestParam(value = "id",required = true) int id,
                          @RequestParam(value = "userName",required = false) String userName,
                          @RequestParam(value = "note",required = false) String note){
        return ts.getTUser(id,userName,note);
    }
}

```

userMapper.xml:

```xml
<mapper namespace="cy41.demo.dao.TuserDao">
    <select id="getTUser" resultType="Tuser">
        SELECT *
        FROM t_user
        WHERE id = #{id}
        <if test="userName!=null and userName!=''">
            AND user_name = #{userName}
        </if>
        <if test="note!=null and note!=''">
            AND note = #{note}
        </if>
    </select>

</mapper>
```

运行截图：

当只输入id查询时：

![image-20191217152236470](J2EE%E4%BD%9C%E4%B8%9A.assets/image-20191217152236470.png)

输入两个以上参数时：

![image-20191217152256468](J2EE%E4%BD%9C%E4%B8%9A.assets/image-20191217152256468.png)





## 2.2 一对一查询

实体类：

```java
//班级类
@Alias("Cclass")
public class Cclass {
    private int cId;
    private String cName;
    private int teacherId;
    private Teacher teacher;
}

```

```java
//教师类
@Alias("Teacher")
public class Teacher {
    private int tId;
    private String tName;
    private Cclass clas;
}

```

dao接口：

```java
@Mapper
@Repository
public interface OneToOne {
    public Teacher getTeacher(int id);
}
```

service层：

```java
@Service
public class OneToOneService {
    @Autowired
    private OneToOne oo;
    public Teacher getTeacher(int id){
        return oo.getTeacher(id);
    }
}
```

controller：

```java
@Controller
public class OneToOneController {
    @Autowired
    private OneToOneService os;
    @GetMapping("getTeacher/{id}")
    @ResponseBody
    public Teacher getTeacher(@PathVariable("id") int id){
        return os.getTeacher(id);
    }
}
```

OneToOne.xml:

```xml
<mapper namespace="cy41.demo.dao.OneToOne">
    <resultMap id="teacher" type="Teacher">
        <id property="tId" column="t_id"></id>
        <result property="tName" column="t_name"></result>
        <association property="clas" javaType="Cclass">
            <id property="cId" column="c_id"></id>
            <result property="cName" column="c_name"></result>
            <result property="teacherId" column="teacher_id"></result>
        </association>
    </resultMap>
    <select id="getTeacher" resultMap="teacher" resultType="Teacher" parameterType="int">
        SELECT *
        FROM teacher t,class c
        WHERE t.t_id=c.teacher_id
        AND t.t_id = #{id}
    </select>
</mapper>
```

运行截图:

![image-20191217154843940](J2EE%E4%BD%9C%E4%B8%9A.assets/image-20191217154843940.png)

前半部分是教师信息，后半部分是所带班级信息。



## 2.3 一对多查询

实体类：

```java
//部门类;
@Alias("Dept")
public class Dept {
    private int deptno;
    private String dname;
    private String loc;
    private List<Emp> list;
}

```

```java
//员工类;
@Alias("Emp")
public class Emp {
    private int empno;
    private String ename;
    private String job;
    private int mgr;
    private Date hiredata;
    private double sal;
    private double comm;
    private int deptno;
}
```

dao接口:

```java
@Mapper
@Repository
public interface OneToMore {
    public Dept getAllEmp(int id); //获取部门所有员工;
    public Emp getDept(int id); //获取员工所在部门;
}
```

service:

```java
@Service
public class OneToMoreService {
    @Autowired
    private OneToMore om;
    public Dept getAllEmp(int id){
        return om.getAllEmp(id);
    }
    public Emp getDept(int id){
        return om.getDept(id);
    }
}

```

controller:

```java
@Controller
public class OneToMoreController {
    @Autowired
    private OneToMoreService os;

    @GetMapping("/getAllEmp/{id}")
    @ResponseBody
    public Dept getAllEmp(@PathVariable("id") int id){
        return os.getAllEmp(id);
    }

    @GetMapping("/getDept/{id}")
    @ResponseBody
    public Emp getDept(@PathVariable("id") int id){
        return os.getDept(id);
    }
}
```

OneToMore.xml:

```xml
<mapper namespace="cy41.demo.dao.OneToMore">
    <!--一对多;-->
    <resultMap id="getAllEmp" type="Dept">
        <id property="deptno" column="deptno"></id>
        <result property="dname" column="dname"></result>
        <result property="loc" column="loc"></result>
        <collection property="list" ofType="Emp">
            <id property="empno" column="empno"></id>
            <result property="ename" column="ename"></result>
            <result property="job" column="job"></result>
            <result property="mgr" column="mgr"></result>
            <result property="hiredate" column="hiredata"></result>
            <result property="sal" column="sal"></result>
            <result property="comm" column="comm"></result>
            <result property="deptno" column="deptno"></result>
        </collection>
    </resultMap>
    <select id="getAllEmp" resultMap="getAllEmp" resultType="Dept">
        SELECT *
        FROM dept d,emp e
        WHERE d.deptno=e.deptno
        AND d.deptno = #{id}
    </select>
    <resultMap id="getDept" type="Emp">
        <id property="empno" column="empno"></id>
        <result property="ename" column="ename"></result>
        <result property="job" column="job"></result>
        <result property="mgr" column="mgr"></result>
        <result property="hiredate" column="hiredata"></result>
        <result property="sal" column="sal"></result>
        <result property="comm" column="comm"></result>
        <result property="deptno" column="deptno"></result>
        <association property="dept" javaType="Dept">
            <id property="deptno" column="deptno"></id>
            <result property="dname" column="dname"></result>
            <result property="loc" column="loc"></result>
        </association>
    </resultMap>
    <select id="getDept" resultMap="getDept" resultType="Dept" parameterType="int">
        SELECT *
        FROM dept d,emp e
        WHERE d.deptno=e.deptno
        AND e.empno = #{id}
    </select>
</mapper>
```



### 2.3.1 查询部门的所有员工

<img src="J2EE%E4%BD%9C%E4%B8%9A.assets/image-20191217163323240.png" alt="image-20191217163323240" style="zoom:80%;" />

### 2.3.2 查询员工信息及部门

<img src="J2EE%E4%BD%9C%E4%B8%9A.assets/image-20191217163428486.png" alt="image-20191217163428486" style="zoom:200%;" />



# 总结

首先学习了一对一，一对多，多对一映射，复习了数据库相关知识。

其次在debug的过程中学到了许多新的知识，例如：

1. 在多表联查时，若两表的主键名称恰好相同，如果直接查询的话只能查到一条数据，这时候需要对其他表的主键进行起别名的操作就可以查到所有数据。
2. 报错信息，找了好长时间最后发现是单词拼写错误，而报错里早已指出报错原因是找不到该属性。
3. sql元素的使用，之前只是了解了一下并没有进行应用。
4. 不过还是没学会用那个渲染json的所以做的比较丑。

