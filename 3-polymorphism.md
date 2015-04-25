## Polymorphism

---

![polymorphism](img/polymorphism.jpg)

---

### Polymorphism

I don't know.  
I don't want to know.

---

### Polymorphism

- Subtype
- Parametric
- Ad-hoc

---

### OO Polymorphism

- Inheritance hierarchy
- Interfaces

---

### Dependency inversion

("Strategy" interfaces)

---

```java
public interface JsonObj {
    String toJson();
}
```

---

```java
public class JsonString implements JsonObj {
    private final String value;
    
    public JsonString(String value) {
        this.value = value;
    }
    
    public String toJson() {
        return "\"" + value + "\"";
    }
}
```

---

```java
public class JsonList implements JsonObj {
    private final List<? extends JsonObj> list;
    
    public JsonString(List<? extends JsonObj> list) {
        this.list = list;
    }
    
    public String toJson() {
        return "[" +
            list.stream()
                .map(JsonObj::toJson)
                .collect(joining(",")) +
            "]";
    }
}
```

---


```java
JsonObj obj = new JsonList(
      new JsonString("a"),
    new JsonList(
        new JsonString("b"),
        new JsonString("c")),
    new JsonString("d"));

System.out.println(obj.toJson());

// ["a",["b","c"],"d"]
```

---

#### Limitations

- Need wrapper types
- Cannot extend existing types

---

![constrain](img/constrain.jpg)

Too constraining!

---

### Protocols

**A.K.A. Typeclasses**

---

```clojure
(defprotocol Json
  (to-json [this]))
```

---

```clojure
(extend-type String Json
  (to-json [this]
    (str "\"" this "\"")))
```

```clojure
(extend-type List Json
  (to-json [this]
    (str "[" (->> this (map to-json) (string/join ",")) "]")))
```

```clojure
(extend-type nil Json
  (to-json [this] "null"))
```

---

```clojure
(to-json ["a" ["b" "c"] nil "d"])

;;=> ["a",["b","c"],null,"d"]
```

---

![reach](img/reach.jpg)

Why stop there?

---

### Multimethods

---

```clojure
(defmulti greet :country)

(defmethod greet "LT" [person]
  (println "Labas," (:name person) ". Kaip sekasi?"))

(defmethod greet "FR" [person]
  (println "Bonjour," (:name person) "!"))

(defmethod greet :default [person]
  (println "Hi," (:name person)))
```

```clojure
(greet {:name "Jacques" :country "FR"})

;;=> Bonjour, Jacques !
```

---

```clojure
(defmulti say (fn [text n] (even? n)))

(defmethod say true [text n]
  (println text n "is even"))

(defmethod say false [text n]
  (println text n "is odd"))
```

```clojure
(say "Guess what?" 5)

;;=> Guess what? 5 is odd
```
