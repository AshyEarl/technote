[源码链接][source-code]
[参考链接][ref-link1]

```xml
<rules>
  <activity/service/broadcast block="true" log="true">
    <intent-filter/>
    <component-filter name="xxx">
  </activity>
</rules>

<and/or/not>
  <xxx>
</and>

<action/component/component-name/component-package/data/mime-type/scheme/scheme-specific-part/host/path
  equals/isNull/startWith/contains/pattern/regex="xxx"/> 
  
<category name="xxx"/>

<sender type="signature/system/system|signature/userId" />

<sender-package name="" />

<sender-permission name="" />

<port equals="12" min="1" max="5"/>
```
一些核心类
- FilterFactory: 用于构建`Filter`
- Filter: 判断当前的intent及其上下文是否匹配当前filter
  ```java
  /**
  * Does the given intent + context info match this filter?
  *
  * @param ifw The IntentFirewall instance
  * @param resolvedComponent The actual component that the intent was resolved to
  * @param intent The intent being started/bound/broadcast
  * @param callerUid The uid of the caller
  * @param callerPid The pid of the caller
  * @param resolvedType The resolved mime type of the intent
  * @param receivingUid The uid of the component receiving the intent
  */
  boolean matches(IntentFirewall ifw, ComponentName resolvedComponent, Intent intent,
            int callerUid, int callerPid, String resolvedType, int receivingUid);
  ```
## Filter结构
- `FilterList`
  - `AndFilter`(and): 所有的子`Filter`都必须满足
    ```xml
    <!-- 匹配系统uid发起的VIEW action -->
    <and>
      <action equals="com.android.VIEW"/>
      <sender type="system">
    </and>
    ```
  - `OrFilter`(or): 任意一个子`Filter`满足
    ```xml
    <!-- 匹配 action 为 VIEW 或者 HOME -->
    <or>
      <action equals="com.android.VIEW"/>
      <action equals="com.android.HOME"/>
    </or>
    ```
- `NotFilter`(not): 子`Filter`条件取反
  ```xml
  <!-- 匹配所有 action 不是 VI 开头 -->
  <not>
    <action startWith="com.android.VI">
  </not>
  ```
- `StringFilter`: 字符串匹配，构造方法包含一个`ValueProvider`
  
  `ValueProvider`匹配`tag`, `StringFilter`匹配`attr`, `tag`包含下面的类型
  |tag|value|
  |--|--|
  |action|intent.getAction()|
  |component|resolvedComponent.flattenToString()|
  |component-name|resolvedComponent.getClassName()|
  |component-package|resolvedComponent.getPackageName()|
  |data|intent.getData().toString()|
  |mime-type|resolvedType|
  |scheme|intent.getData().getScheme()|
  |scheme-specific-part|intent.getData().getSchemeSpecificPart()|
  |host|intent.getData().getHost()|
  |path|intent.getData().getPath()|
  ```xml
  <!-- action由ValueProvider匹配，equals由EqualsFilter匹配 -->
  <action equals="com.android.VIEW"/>
  ```
   - `EqualsFilter`(equals)
     ```xml
     <!-- 匹配 action 为 VIEW -->
     <action equals="com.android.VIEW"/>
     ```
   - `IsNullFilter`(isNull)
     ```xml
     <!-- 匹配 action 为空-->
     <action isNull="true"/>
     <!-- 当没有其他StringFilter时，默认为isNull=false -->
     <action isNull="false"/>
     <action/>
     ```
   - `StartsWithFilter`(startWith)
     ```xml
     <!-- 匹配 action 以 VI 开头 -->
     <action startWith="com.android.VI"/>
     ```
   - `ContainsFilter`(contains)
     ```xml
     <!-- 匹配 action 包含 VIEW -->
     <action contains="VIEW"/>
     ```
   - `PatternStringFilter`(pattern)
     > new PatternMatcher(pattern, PatternMatcher.PATTERN_SIMPLE_GLOB).match(value);
     ```xml
     <!-- 匹配 action 模式为 com.android.VI* -->
     <action pattern="com.android.VI*"/>
     ```
   - `RegexFilter`(regex)
     > 相比`PatternStringFilter`提供更多的正则支持，详情查看[文档][simple-glob]
     ```xml
     <!-- 匹配 action 模式为 com.android.VI* -->
     <action regex="com.android.VI*"/>
     ```
- `CategoryFilter`(category)
  ```xml
  <!-- intent.getCategories().contains(xxx) -->
  <category name="xxx" />
  ```
- `SenderFilter`(sender)
  |type|match|
  |--|--|
  |signature|ifw.signaturesMatch(callerUid, receivingUid)|
  |system|isPrivilegedApp(callerUid, callerPid)|
  |system\|signature|isPrivilegedApp(callerUid, callerPid) \|\| ifw.signaturesMatch(callerUid, receivingUid)|
  |userId|ifw.checkComponentPermission(null, callerPid, callerUid, receivingUid, false)|
  ```xml
  <sender type="signature/system/system|signature/userId" />
  ```
- `SenderPackageFilter`(sender-package)
  ```xml
  int packageUid = pm.getPackageUid(package, PackageManager.MATCH_ANY_USER, UserHandle.USER_SYSTEM);
  UserHandle.isSameApp(packageUid, callerUid);
  <sender-package name="package" />
  ```
- `SenderPermissionFilter`(sender-permission)
  ```xml
  ifw.checkComponentPermission(permission, callerPid, callerUid, receivingUid, true);
  <sender-permission name="permission" />
  ```
- `PortFilter`(port)
  ```xml
  int port = intent.getData().getPort()
  port != -1 && (mLowerBound == NO_BOUND || mLowerBound <= port) &&
                (mUpperBound == NO_BOUND || mUpperBound >= port);
  <port equals="12" min="1" max="5"/>
  ```
## 第一阶段匹配
```java
// 第一阶段匹配使用IntentResolver来进行，
// For the first pass, find all the rules that have at least one intent-filter or
// component-filter that matches this intent
List<Rule> candidateRules;
candidateRules = resolver.queryIntent(getPackageManager().snapshot(), intent, resolvedType,
        false /*defaultOnly*/, 0);
if (candidateRules == null) {
    candidateRules = new ArrayList<Rule>();
}
// 直接通过组件名称(pkg/component)来匹配
resolver.queryByComponent(resolvedComponent, candidateRules);
```
- `intent-filter`
  ```xml
  <intent-filter>
    <!-- addAction(name); -->
    <action name="xxx" />
    <!-- addCategory(name); -->
    <cat name="xxx">
    <!-- addDataType(name); -->
    <staticType name="xxx">
    <!-- addDynamicDataType(name); -->
    <type name="xxx">
    <!-- addMimeGroup(name); -->
    <group name="xxx">
    <!-- addDataScheme(name); -->
    <schema name="xxx">
    <!-- addDataSchemeSpecificPart(ssp, PatternMatcher.PATTERN_LITERAL); -->
    <ssp literal="xxx">
    <!-- addDataSchemeSpecificPart(ssp, PatternMatcher.PATTERN_PREFIX); -->
    <ssp prefix="xxx">
    <!-- addDataSchemeSpecificPart(ssp, PatternMatcher.PATTERN_SIMPLE_GLOB); -->
    <ssp sglob="xxx">
    <!-- addDataSchemeSpecificPart(ssp, PatternMatcher.PATTERN_ADVANCED_GLOB); -->
    <ssp aglob="xxx">
    <!-- addDataSchemeSpecificPart(ssp, PatternMatcher.PATTERN_SUFFIX); -->

    <ssp suffix="xxx">
    <!-- addDataAuthority(host, port); -->
    <auth host="xxx" port="xxx">
    <!-- addDataPath(path, PatternMatcher.PATTERN_LITERAL); -->
    <path literal="xxx">
    <!-- addDataPath(path, PatternMatcher.PATTERN_PREFIX); -->
    <path prefix="xxx">
    <!-- addDataPath(path, PatternMatcher.PATTERN_SIMPLE_GLOB); -->
    <path sglob="xxx">
    <!-- addDataPath(path, PatternMatcher.PATTERN_ADVANCED_GLOB); -->
    <path aglob="xxx">
    <!-- addDataPath(path, PatternMatcher.PATTERN_SUFFIX); -->

  </intent-filter>
  ```
- `component-filter`
  ```xml
  <component-filter name="xxx" />
  ```
## 一些例子
- 不允许除`Settings`和`Player`之外其他的应用打开
  ```xml
  <rules>
  <activity block="true" log="true">
    <intent-filter>
      <action name="android.intent.action.MAIN" />
      <cat name="android.intent.category.LAUNCHER" />
    </intent-filter>
    
    <not>
      <or>
        <component-package equals="com.android.settings" />
        <component-package equals="com.instwall.player" />
      </or>
    </not>
  </activity>
  </rules>
  ```
- 禁止`Player`中的对应组件
  ```xml
  <rules>
  <activity block="true" log="true">
    <component-filter name="com.instwall.player" />
  </activity>
  </rules>
  ```
[source-code]: https://cs.android.com/android/platform/superproject/+/master:frameworks/base/services/core/java/com/android/server/firewall/IntentFirewall.java
[simple-glob]: https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/os/PatternMatcher.java;drc=5ca657189aac546af0aafaba11bbc9c5d889eab3;l=50
[ref-link1]: https://carteryagemann.com/pages/android-intent-firewall.html