# 内置对象

## 1 request

1. ```java
    getParameter("name")//获取url中的参数或表格
    ```

2. ```java
    setAttribute("name",value)//映射   getAttribute("name")//获取
    ```

3. setCharacterEncoding("utf-8")

4. ```java
    request.getRequestDispatcher("url").forward(request,response);//请求转发
    ```

## 2 response

1. sendRedirect("url") 重定向，request失效

## 3 session

1. get/set/remove  Attribute
2. invalidate() 销毁session



# jdbc

## 1 Connection

1. close()
2. prepareStatement("sql")    PreparedStatement

## 2 DriverManager

getConnection(url,name,password)    Connection

## 3 Statamet

1. executeQuery("sql")   ResultSet  返回结果集
2. executeUpdate("sql") int  返回sql影响的行数

## 4 PreparedStatement

各种set方法。

## 5 ResultSet

各种get方法以及next方法



# 实操

## 1 查询

直接  conn.prepareStatement("sql").executeQuery(); 获取到ResultSet对象，然后变成list，请求转发。

## 2 修改 删除 更新

PreparedStatement ps=con.prepareStatement("sql");

然后修改问号  ps.set........

然后int row=ps.executeUpdata();即可。