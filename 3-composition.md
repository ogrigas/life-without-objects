## Composition

---

![composition](img/composition.jpg)

---

### Composition

Avoiding hard-coded dependencies

---

### OO Dependencies

```java
public class ProfilePage {

    String render(Repository repo, int customerId) {
        return toHtml(repo.loadProfile(customerId));
    }
}
```

```java
Repository repo = new Repository();
ProfilePage page = new ProfilePage();
```

```java
String html = page.render(repo, customerId);
```

---

### FP Dependencies

```clojure
(defn render-page [repository-fn customer-id]
  (to-html (repository-fn customer-id)))
```

```clojure
(defn load-from-db [customer-id]
  (...))
```

```clojure
(render-page load-from-db customer-id)
```

---

### OO Composition

```java
ProfilePage page = new ProfilePage(new Repository());
```

```java
page.render(customerId);
```

---

### Function closure

```clojure
(defn inject [f arg1]
  (fn [arg2] (f arg1 arg2)))

(def render-injected
  (inject render-page load-from-db))
```

```clojure
(render-injected customer-id)
```

---

### Partial application

```clojure
(def render-injected
  (partial render-page load-from-db))
```

```clojure
(render-injected customer-id)
```

---

**"Object is a collection of partially-applied functions."**

- J.B. Rainsberger

---

### "Adapter" pattern

```clojure
(defn to-view-model [profile] (...))

(render-page (comp to-view-model load-from-db) id)
```

---

### "Decorator" pattern

```clojure
(defn traceable [f]
  (fn [& args]
    (log/trace "Called with params" args)
    (let [result (apply f args)]
      (log/trace "Returned" result)
      result)))

(render-page (traceable load-from-db) id)
```
