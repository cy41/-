## #与$

```java
#{}为常量的输出
${}为变量的输出
```

## 标签

1. ```java
    [[${cy.name}]] //输出，可放在标签之中例：<h1>[[${cy.name}]]</h1>
    ```

2. ```java
    th:text="${}" 可以给html标签赋值，相当于写在标签中间;
    ```

3. ```java
    th:each="emp,stat:${emps}" 生成若干个标签，相当于java中的foreach;
    ```

4. ```java
    三目运算符  ? :
    ```

5. ```java
    多分支：
        th:switch    th:case
    	
    ```

6. 

## 对象

```java
#strings 提供的所有方法与java中的String一致
比如要把${cy.name}小写输出
[[ ${#strings.toLowerCase(cy.name)} ]]
```

