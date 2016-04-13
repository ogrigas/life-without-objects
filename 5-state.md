## State management 

---

![state-machine](img/state-machine.png) <!-- .element class="plain" height="360" -->

State Machine / Business Process / Entity

---

"Class" as a namespace

```clojure
(def Account
  {'deposit  (fn [state amount]
               (update state :balance + amount))

   'withdraw (fn [state amount]
               (if (< amount (state :balance))
                 (update state :balance - amount)
                 state))})
```

```clojure
(def state0 {:balance 0})
(def state1 ((Account 'deposit)  state0 100))
(def state2 ((Account 'withdraw) state1 30))
(def state3 ((Account 'withdraw) state2 20))
```

---

Partially apply _state_ with "constructor" 

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

Partially apply _state_ with "constructor"

```clojure
(defn new-object [clazz initial-state]
  (let [state-ref (atom initial-state)]
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

Partially apply _state_ with "constructor"

```clojure
(defn new-object [clazz initial-state]
  (let [state-ref (atom initial-state)]
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

Partially apply _state_ with "constructor"

```clojure
(defn new-object [clazz initial-state]
  (let [state-ref (atom initial-state)]
    (fn [method & args]
      (apply swap! state-ref (clazz method) args))))
```

```clojure
(def acc (new-object Account {:balance 0}))
(acc 'deposit 100)
(acc 'withdraw 30)
(acc 'withdraw 20)
```
