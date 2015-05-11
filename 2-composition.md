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

    public String render(Repository repository, int customerId) {
        return toHtml(repository.loadProfile(customerId));
    }
}
```

```java
Repository repository = new Repository();
ProfilePage page = new ProfilePage();
```

```java
String html = page.render(repository, customerId);
```

---

### FP Dependencies

```clojure
(defn render-page [repository-fn customer-id]
  (to-html (repository-fn customer-id)))
```

```clojure
(defn load-profile [customer-id]
  (...))
```

```clojure
(render-page load-profile customer-id)
```

---

### OO Composition

```java
ProfilePage pageInjected = new ProfilePage(new Repository());
```

```java
pageInjected.render(customerId);
```

---

### Function closure

```clojure
(def render-injected
  (fn [customer-id] (render-page load-profile customer-id)))
```

```clojure
(render-injected customer-id)
```

---

### Partial application

```clojure
(def render-injected (partial render-page load-profile))
```

```clojure
(render-injected customer-id)
```

---

### "Adapter" pattern

```clojure
(defn to-view-model [profile] (...))

(render-page (comp to-view-model load-profile) customer-id)
```

---

### "Decorator" pattern

```clojure
(defn with-logging [f]
  (fn [& args]
    (log/debug "Called with params" args)
    (def [result (apply f args)]
      (log/debug "Returned" result)
      result)))

(render-page (with-logging load-profile) customer-id)
```

