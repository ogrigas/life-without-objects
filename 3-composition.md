## Composition

---

![composition](img/composition.jpg)

---

## Dependency inversion

- Good for decoupling
- Good for isolated testing

---

### OO dependencies

```java
public class ProfilePage {

    String render(Repository repo, int customerId) {
        return toHtml(repo.loadProfile(customerId));
    }
}
```

```java
Repository repo = new SqlRepository();
ProfilePage page = new ProfilePage();
```

```java
String html = page.render(repo, customerId);
```

---

### FP dependencies

```clojure
(defn render-page [repository-fn customer-id]
  (to-html (repository-fn customer-id)))
```

```clojure
(defn load-from-sql [customer-id]
  (...))
```

```clojure
(render-page load-from-sql customer-id)

```

---

### Basic FP polymorphism

All functions implement the "Strategy" pattern

---

### OO composition

```java
Repository repo = new SqlRepository();
ProfilePage page = new ProfilePage(repo);
```

```java
page.render(customerId);
```

---

### Function closure

```clojure
(defn inject [f arg1]
  (fn [arg2] (f arg1 arg2)))
```

```clojure
(def render-from-db
  (inject render-page load-from-sql))
```

```clojure
(render-from-db customer-id)
```

---

### Partial application

```clojure
(def render-from-db
  (partial render-page load-from-sql))
```

```clojure
(render-from-db customer-id)
```

---

**"Object is a collection of partially-applied functions."**

( _J.B. Rainsberger_ )

---

## Structural patterns

- Adapter
- Decorator
- ...

---

### "Adapter" pattern

```clojure
(defn to-view-model [profile] (...))

(render-page (comp to-view-model load-from-sql) id)
```

---

### "Decorator" pattern

```clojure
(defn traceable [f]
  (fn [& args]
    (log/trace "Called with params:" args)
    (apply f args)))

(render-page (traceable load-from-sql) id)
```
