# Nashorn

## Nashorn? <a href="#nashorn" id="nashorn"></a>

**JAVA로 개발한 JavaScript 엔진**

Java 8 부터 추가

기존에 쓰던 Rhino 보다 더 뛰어난 성능으로 만들기 위해 많은 기술이 사용되었음.

### Command <a href="#command" id="command"></a>

JDK 1.8 에는 JavaScript 파일을 실행하는 데 사용할 수 있거나 인수 없이 시작할 경우 REPL 로 사용할 수 있는 jjs 라는 command 가 포함되어 있음.

```
$ $JAVA_HOME/bin/jjs hello.js

Hello World
```

hello.js

```
print("Hello World");
```

런타임에 지시 할 수 있음.

```
$ $JAVA_HOME/bin/jjs

jjs> print("Hello World")
Hello World
```

shell script 처럼 사용 가능

```
#!$JAVA_HOME/bin/jjs

var greeting = "Hello World";
print(greeting);
---
$ ./hello.js
Hello World
```

### ScriptEngine 으로 만들 수 있음. <a href="#scriptengine-ec-9c-bc-eb-a1-9c-eb-a7-8c-eb-93-a4-ec-88-98-ec-9e-88-ec-9d-8c" id="scriptengine-ec-9c-bc-eb-a1-9c-eb-a7-8c-eb-93-a4-ec-88-98-ec-9e-88-ec-9d-8c"></a>

```
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");

Object result = engin.eval(
    "var greeting = 'hello world';" +
    "print(greeting);" +
    "greeting"
);
```

### Script 에 데이터 전달 <a href="#script-ec-97-90-eb-8d-b0-ec-9d-b4-ed-84-b0-ec-a0-84-eb-8b-ac" id="script-ec-97-90-eb-8d-b0-ec-9d-b4-ed-84-b0-ec-a0-84-eb-8b-ac"></a>

Binding 개체를 정의하고 이를 eval 함수에 두번째 매개 변수로 전달하여 데이터를 전달 할 수 있음.

```
Bindings bindings = engine.createBindings();
bindings.put("count",3);
bindings.put("name","baeldung");

String script =  "var greegting = 'Hello ';"+
    "for(var i = count; i>0;i--) {" + 
    "greeting+=name + ' '" +
    "}" +
    "greeting";

Object bindingsResult = engine.eval(script, bindings);
```

### JavaScript 함수 호출 <a href="#javascript-ed-95-a8-ec-88-98-ed-98-b8-ec-b6-9c" id="javascript-ed-95-a8-ec-88-98-ed-98-b8-ec-b6-9c"></a>

Java 코드에서 JavaScript 함수를 호출 하는 것도 가능하다.

```
engin.eval("function composeGreeting(name){" +
    "return 'Hello ' + name" +
    "}");

Invocable invocable = (Invocable) engine;

Object funcResult = invocable.invokeFunction("composeGreeting","baeldung");


Hello baeldung 호출 됨.
```

### Java 객체 사용 <a href="#java-ea-b0-9d-ec-b2-b4-ec-82-ac-ec-9a-a9" id="java-ea-b0-9d-ec-b2-b4-ec-82-ac-ec-9a-a9"></a>

JVM에서 실행되므로 JavaScript 코드 내에서 Java 사용 가능

Java 객체를 사용하여 수행됨.

```
Object map = engin.eval("var HashMap = Java.type('java.util.HashMap');" +
    "var map = new HashMap();" +
    "map.put('hello', 'world')" + 
    "map"
);
```

### For-Each 를 사용 <a href="#for-each-eb-a5-bc-ec-82-ac-ec-9a-a9" id="for-each-eb-a5-bc-ec-82-ac-ec-9a-a9"></a>

```
String script = "var list = [1, 2, 3, 4, 5];" +
  "var result = '';" +
  "for each (var i in list) {" +
  "result+=i+'-';" +
  "};" +
  "print(result);";

engine.eval(script);
```

### 중괄호 생략 <a href="#ec-a4-91-ea-b4-84-ed-98-b8-ec-83-9d-eb-9e-b5" id="ec-a4-91-ea-b4-84-ed-98-b8-ec-83-9d-eb-9e-b5"></a>

```
function increment(in) ++in
```

### try-catch <a href="#try-catch" id="try-catch"></a>

try catch 됨

try{ throw "BOOM"; }catch(e if typeof e === 'string') { print("String ") }
