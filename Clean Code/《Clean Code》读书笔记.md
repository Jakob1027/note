# Chapater2 命名
## 名副其实
```java
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for(int[] x: theList) {
        if(x[0] == 4) {
            list1.add(x);
        }
    }
    return list1;
}
```
```java
public List<Cell> getFlaggedCells() {
    List<Cell> flaggedCells = new ArrayList<Cell>();
    for(Cell cell: gameBoard) {
        if(cell.isFlagged()) {
            flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```
## 做有意义的区分
- 数字系列命名：a1,a2,......aN
```java
public static void copyChars(char a1[], char a2[]) {
    for(int i = 0;i<a1.length;i++) {
        a2[i] = a1[i];
    }
}
```
如果参数名改为`source`和`destination`，这个函数就会像样许多。
- `Product` `ProductInfo` `ProductData`
## 使用读得出来的名称
## 使用可搜索的名称
## 类名
类名和对象名应该是名词或名词短语，如`Customer`、`WikiPage`、`Account`和`AddressParser`。
## 方法名
方法名应当是动词或动词短语，如`postPayment,deletePage或save`。

---

# Chapater3 函数
## 短小
- 函数的第一规则是要短小，第二条规则是还要更短小。
- 20行封顶最佳。
## 只做一件事
函数应该做一件事。做好这件事。只做这一件事。
如果函数只是做了该函数名下同意抽象层上的步骤，则函数还是自作了一件事。
## `switch`语句
将`switch`语句埋到抽象工厂底下，不让任何人看到。该工厂使用`switch`语句为`Employee`的派生物创建适当的尸体，而不同的函数，如`calculatePay`、`isPayDay`和`deliverPay`等，则借由`Employee`借口多态地接受派遣。
## 使用描述性的名称
- `includeSetupAndTeardownPages`
- `includeSetupPages`
- `includeSuiteSetupPage`
- `includeSetupPage`
## 函数参数
最理想的参数数量是零，其次是一，再次是二，应尽量避免三。
### 一元函数的普遍形式
- 转换：`boolean fileExists("MyFile")`
- 事件：`void passwordAttemptFailedNtimes(int attempts)`
### 标识参数
表示参数丑陋不堪。向函数传入布尔值简直就是骇人听闻的做法。这样做，方法签名立刻变得复杂起来，大声宣布本函数不只做一件事。如果标识为true将会这样做，标识为false则会那样做。
### 参数对象
如果函数看来需要两个、三个或三个以上参数，就说明其中一些参数应该封装为类了。
## 无副作用
```java
public class UserValidator{
    private Cryptographer cryptographer;

    public boolean checkPassword(String userName,String password) {
        User user = UserGateway.findByName(userName);
        if(user != User.NULL) {
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codePhrase,password);
            if("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```
- `Session.initialize()`产生了副作用，既检查了密码，又初始化了会话，且在函数名中没有表达出来。
- 普遍而言，应避免使用输出参数。如果函数必须要修改某种状态，就修改所属对象的状态吧。
## 分隔指令与询问
函数要么做什么事，要么回答什么事，但二者不可兼得。函数应该修改某对象的状态，或是返回该对象的有关信息。两样都干常会导致混乱。
```java
public boolean set(String attribute, String value);
```
该函数设置某个指定属性，如果成功就返回true，如果不存在那个属性则返回false。
应修改成:
```java
if(attributeExists("username")) {
    setAttribute("username","unclebob");
}
```
## Don't repeat yourself!(DRY原则)

---

# Chapater4 注释
## 注释不能美化糟糕的代码
### 用代码来阐述
```java
// Check to see if the employee is eligible for full benefits
if((employee.flags & HOURLY_FLAG) && (employee.age>65))
```
or
```java
if(employee.isEligibleForFullBenefits())
```
### 好注释
- 法律信息
- 提供信息的注释
- 对意图的解释
- 阐释
- 警示
- TODO注释
- 放大
- 公共API中的Javadoc

---

# Chapater5 格式

---

# Chapater6 对象和数据结构
对象暴露行为，隐藏数据。便于添加新对象类型而无需修改既有行为，同时也难以在既有对象中添加新行为。数据结构曝露数据，没有明显的行为。便于向既有数据结构添加新行为，同时也难以向既有函数添加新数据结构。

---

# Chapater7 错误处理
## 可控异常
可抗异常的代价就是违反开放/闭合原则。如果你在方法中抛出可控异常，而catch语句在三个层级之上，你就得在catch语句和抛出异常处之间的每个方法签名中声明该异常。这意味着对软件中较低层级的修改，都将波及较高层级的签名。修改好的模块必须重新构建、发布，即便它们自身所关注的任何东西都没改动过。
## 别返回null值
例：集合类，返回空集合替代null。`Collections.emptyList()`
## 别传递null值
## 小结
如果将错误处理隔离看待，独立于主要逻辑之外，就能写出强固而整洁的代码。

---

# Chapater8 边界
## 学习性测试
编写测试来遍览和理解第三方代码。
## 使用尚不存在的代码
使用Adapter模式

---

# Chapater9 单元测试
## 测试带来一切好处
## 整洁的测试
整洁的测试三个要素：可读性，可读性和可读性。
整洁的测试遵循5条规则（F.I.R.S.T）：
- 快速(Fast)
- 独立(Independent)
- 可重复(Repeatable)
- 自足验证(Self-Validating)
- 及时(Timely)

---

# Chapater10 类
## 类的组织
1. 公共静态常亮
2. 私有静态变量
3. 私有实体变量（很少会有公共变量）
4. 公共函数
5. 公共函数调用的私有工具函数紧随在该公共函数后面
## 类应该短小
对于函数，我们通过计算代码行数衡量大小。对于类，我们采用不同的衡量方法，计算**权责**
### 单一权责原则
### 内聚
<p>类应该只有少量实体变量。类中的每个方法都应该操作一个或多个这种变量。通常而言，方法操作的变量越多，就越粘聚到类上。如果一个类中的每个变量都被每个方法所使用，则该类具有最大的内聚性。</p>
<p>保持函数和参数列表短小的策略，有时会导致为一组子集方法所用的实体变量数量增加。出现这种情况时，往往意味着至少有一个类要从大类中挣扎出来。你应当尝试将这些变量和方法分拆到两个或多个类中，让新的类更为内聚。</p>

### 保持内聚性就会得到许多短小的类
拆分函数==>将局部变量提升为实体变量==>丧失内聚性==>为了提高内聚性，将类拆分为多个小类。
```java

```
# Chapater11 系统
## Java代理
```java
public interface Bank {
    Collection<Account> getAccounts();
    void setAccounts(Collection<Account> accounts);
}

public class BankImpl implements Bank {

    private List<Account> accounts;

    @Override
    public Collection<Account> getAccounts() {
        return accounts;
    }

    @Override
    public void setAccounts(Collection<Account> accounts) {
        this.accounts = new ArrayList<>();
        this.accounts.addAll(accounts);
    }
}
```
```java
public class BankProxyHandler implements InvocationHandler {

    private Bank bank;

    public BankProxyHandler(Bank bank) {
        this.bank = bank;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String methodName = method.getName();
        if (methodName.equals("getAccounts")) {
            bank.setAccounts(getAccountsFromDatabase());
            return bank.getAccounts();
        } else if (methodName.equals("setAccounts")) {
            bank.setAccounts((Collection<Account>)args[0]);
            setAccountsToDatabase(bank.getAccounts());
        }
        return null;
    }

    private Collection<Account> getAccountsFromDatabase() {...}

    private void setAccountsToDatabase(Collection<Account> accounts){...}
}
```
```java
Bank bank = (Bank) Proxy.newProxyInstance(
                Bank.class.getClassLoader(),
                new Class[]{Bank.class},
                new BankProxyHandler(new BankImpl()));
```
# Chapater12 迭进
Kent Beck关于简单设计的四条规则（按重要程度排列）：
- 运行所有测试
- 不可重复
- 表达了程序员的意图
- 尽可能减少类和方法的数量
## 不可重复
```java
public class VacationPolicy {
    public void accrueUsDivisionVacation() {
        // code to calculate vacation based on hours worked to date
        // ...
        // code to ensure vacation meets US minimums
        // ...
        // code to apply vacation to payroll record
        // ...
    }
    public void accrueEUDivisionVacation() {
        // code to calculate vacation based on hours worked to date
        // ...
        // code to ensure vacation meets EU minimums
        // ...
        // code to apply vacation to payroll record
        // ...
    }
}
```
```java
public abstract class VavationPolicy {
    public void accrueVacation() {
        calculateBaseVacationHours();
        alterForLegalMinimums();
        applyToPayroll();
    }
    private calculateBaseVacationHours(){...}
    abstract protected void alterForLegalMinimums();
    private void applyToPayroll(){...}
}

public class USVacationPolicy extends VacationPolicy {
    @Override protected void alterForLegalMinimums(){
        // US specific logic
    }
}

public class EUVacationPolicy extends VacationPolicy {
    @Override protected void alterForLegalMinimums(){
        // EU specific logic
    }
}
```
# Chapater13 并发编程
## 并发防御原则
### 单一权责原则
- 并发相关代码有自己的开发、修改和调优生命周期；
- 开发相关代码有自己要对付的挑战，和非并发相关代码不同，而且往往更为困难；
- 即便没有周边应用程序增加的负担，写得不好的并发代码可能的出错方式数量也已经足具挑战性

**建议：** 分离开发相关代码与其他代码。
### 推论：限制数据作用域
**建议：** 谨记数据封装；严格限制对可能被共享的数据的访问。
### 推论：使用数据复本
### 推论：线程应尽可能地独立
**建议：** 尝试将数据分解到可被独立线程（可能在不同处理器上）操作的独立子集。
## 了解执行模型
- 生产者-消费者模型
- 读者-作者模型
- 哲学家进餐

**建议：** 学习这些基础算法，理解其解决方案。
## 警惕同步方法之间的依赖
同步方法之间的依赖会导致并发代码中的狡猾缺陷。`Java`语言有`synchronized`概念，可以用来保护单个方法。然而，如果同一共享类中有多个同步方法，系统就可能写的不太正确了。

**建议：** 避免使用一个共享对象的多个方法。

有时必须使用一个共享对象的多个方法。在这种情况发生时，有3种写对代码的手段：
- **基于客户端的锁定**————客户端代码在调用第一方法前锁定服务器，确保锁的范围覆盖了调用最后一个方法的代码；
- **基于服务端的锁定**————在服务端内创建锁定服务端的方法，调用所有方法，然后解锁。让客户端代码调用新方法；
- **适配服务端**————创建执行锁定的中间层。这是一种基于服务端的锁定的例子，但不修改原始服务端代码。
## 保持同步区域微小
## 很难编写正确的关闭代码
**建议：** 尽早考虑关闭问题，尽早令其工作正常。这会花费比你预期更多的时间。检视既有算法，因为这可能会比想象中难得多。
## 小结
并发代码很难写正确。加入多线程和共享数据后，简单的代码也会变成噩梦。要编写并发代码，就得严格地编写整洁的代码，否则将面临微细和不频繁发生的失败。

第一要诀是遵循单一权责原则。将系统切分为分离了线程相关代码和线程无关代码的POJO。确保在测试线程相关代码时只是在测试，没有做其他事情。线程相关代码应该保持短小和目的几种。

了解兵法问题的可能原因：对共享数据的多线程操作，或使用了公共资源池。类似平静关闭或停止循环之类边界情况尤其棘手。

学习类库，了解基本算法。理解类库提供的与基础算法类似的解决问题的特性。

学习如何找到必须锁定的代码区域并锁定之。不要锁定不必要锁定的代码。避免从锁定区域中调用其他锁定区域。这需要深刻理解某物是否已共享。尽可能减少共享对象和共享范围。修改对象的设计，向客户代码提供共享数据，而不是迫使客户代码管理共享状态。

问题会跳出来。那种在早期没跳出来的问题往往是偶发的。这种所谓偶发问题，通常仅在高负载下出现或偶然出现。所以，你要能在不同平台上、以不同配置持续重复运行线程代码。跟随TDD三要则而来的可测试性意味着某种程度的可插拔性，从而提供了在大量不同配置下运行代码的必要支持。

如果花点时间装置代码，就能极大地提升发现错误代码的机会。可以手工做，也可以使用某种自动化技术。尽早这么做。在将线程代码投入生产环境前，就要尽可能多地运行它。

只要采用了整洁的做法，做对的可能性就有翻天覆地的提高。


