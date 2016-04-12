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

"Class" as a namespace

```clojure
(def Account
  {'deposit  (fn [this amount]
               (update this :balance + amount))

   'withdraw (fn [this amount]
               (if (< amount (this :balance))
                 (update this :balance - amount)
                 this))})
```

```clojure
(def state0 {:balance 0})
(def state1 ((Account 'deposit)  state0 100))
(def state2 ((Account 'withdraw) state1 30))
(def state3 ((Account 'withdraw) state2 20))
```

---

"Constructor" to partially apply _state_ 

```clojure
(defn new-object [clazz initial-state]
  ..........
    ..........
      ..........)
```

```clojure
(def acc (new-object Account {:balance 0}))
(acc 'deposit 100)
(acc 'withdraw 30)
(acc 'withdraw 20)
```

---

"Constructor" to partially apply _state_

```clojure
(defn new-object [clazz initial-state]
  (let [state (atom initial-state)]
    ..........
      ..........))
```

```clojure
(def acc (new-object Account {:balance 0}))
(acc 'deposit 100)
(acc 'withdraw 30)
(acc 'withdraw 20)
```

---

"Constructor" to partially apply _state_

```clojure
(defn new-object [clazz initial-state]
  (let [state (atom initial-state)]
    (fn [method & args]
      ..........)))
```

```clojure
(def acc (new-object Account {:balance 0}))
(acc 'deposit 100)
(acc 'withdraw 30)
(acc 'withdraw 20)
```

---

"Constructor" to partially apply _state_

```clojure
(defn new-object [clazz initial-state]
  (let [state (atom initial-state)]
    (fn [method & args]
      (apply swap! state (clazz method) args))))
```

```clojure
(def acc (new-object Account {:balance 0}))
(acc 'deposit 100)
(acc 'withdraw 30)
(acc 'withdraw 20)
```

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
