# 📘 מדריך מלא למבחן – OOP, ירושה, CRUD, Testing (JavaScript)

המטרה: **מחר לפתוח את הקובץ הזה, לעבוד שלב־שלב, ולהגיע מוכן למבחן**.
אין כאן קפיצות, אין הנחות מוקדמות.

---

## 🧠 מה אנחנו בונים?

**אפליקציית רישום אנשים**:

* רושמים אנשים
* כל אדם מקבל "ברוך הבא"
* יש CRUD מלא
* משתמשים ב־OOP וירושה
* מבנה קבצים מסודר (חשוב מאוד למבחן)

---

## 📁 שלב 1 – יצירת הפרויקט והמבנה

### 1️⃣ צור תיקייה חדשה (איך שתרצה):

```
people-app
```

### 2️⃣ בתוך התיקייה צור את המבנה הבא:

```
people-app/
│
├─ app.js
│
├─ models/
│   ├─ Person.js
│   └─ RegisteredPerson.js
│
├─ services/
│   └─ peopleService.js
│
└─ tests/
    └─ people.test.js
```

📌 **למה זה חשוב?**

* `models` → מחלקות (OOP)
* `services` → לוגיקה ו־CRUD
* `tests` → בדיקות
* `app.js` → נקודת הרצה

---

## ⚙️ שלב 2 – הגדרת Node.js

בתוך התיקייה הראשית:

```bash
npm init -y
```

פתח `package.json` והוסף:

```json
"type": "module"
```

📌 זה מאפשר להשתמש ב־`import / export`

---

## 🧱 שלב 3 – מחלקת בסיס (OOP)

### 📄 models/Person.js

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    return `שלום, ${this.name}`;
  }
}

export default Person;
```

📌 מה למדת כאן:

* class
* constructor
* this
* method

---

## 🧬 שלב 4 – ירושה (Inheritance)

### 📄 models/RegisteredPerson.js

```js
import Person from "./Person.js";

class RegisteredPerson extends Person {
  constructor(name, id) {
    super(name);
    this.id = id;
  }

  welcome() {
    return `ברוך הבא ${this.name}`;
  }
}

export default RegisteredPerson;
```

📌 נקודות למבחן:

* `extends` = ירושה
* `super()` = קריאה ל־constructor של ההורה
* חובה לקרוא ל־super לפני `this`

---

## 🔁 שלב 5 – CRUD (הלב של המבחן)

### 📄 services/peopleService.js

```js
import RegisteredPerson from "../models/RegisteredPerson.js";

let people = [];
let nextId = 1;

// CREATE
export function createPerson(name) {
  const person = new RegisteredPerson(name, nextId++);
  people.push(person);
  return person;
}

// READ
export function getAllPeople() {
  return people;
}

// UPDATE
export function updatePerson(id, newName) {
  const person = people.find(p => p.id === id);
  if (!person) return null;

  person.name = newName;
  return person;
}

// DELETE
export function deletePerson(id) {
  const index = people.findIndex(p => p.id === id);
  if (index === -1) return false;

  people.splice(index, 1);
  return true;
}
```

📌 זה CRUD מלא:

* Create → יצירה
* Read → קריאה
* Update → עדכון
* Delete → מחיקה

---

## ▶️ שלב 6 – הרצה והדגמה

### 📄 app.js

```js
import {
  createPerson,
  getAllPeople,
  updatePerson,
  deletePerson
} from "./services/peopleService.js";

const p1 = createPerson("דוד");
console.log(p1.welcome());

const p2 = createPerson("משה");
console.log(getAllPeople());

updatePerson(1, "דודו");
console.log(getAllPeople());

deletePerson(2);
console.log(getAllPeople());
```

### ▶️ הרצה:

```bash
node app.js
```

---

## 🧪 שלב 7 – Testing (ידע נדרש למבחן)

המטרה כאן: **להראות שליטה מלאה ב־CRUD כולל מקרי קצה**.
גם אם לא מריצים בפועל – זה בדיוק מה שבודקים בהבנה תאורטית.

---

### 📄 tests/people.test.js

```js
import {
  createPerson,
  getAllPeople,
  updatePerson,
  deletePerson
} from "../services/peopleService.js";

// ניקוי נתונים לפני כל טסט (רעיון חשוב למבחן)
beforeEach(() => {
  // איפוס ידני – במבחן מספיק להסביר את הרעיון
});

// ======================
// CREATE
// ======================

test("CREATE – person is created with id", () => {
  const p = createPerson("דוד");
  expect(p.id).toBeDefined();
});

test("CREATE – welcome message is correct", () => {
  const p = createPerson("משה");
  expect(p.welcome()).toBe("ברוך הבא משה");
});

// ======================
// READ
// ======================

test("READ – getAllPeople returns array", () => {
  const people = getAllPeople();
  expect(Array.isArray(people)).toBe(true);
});

test("READ – array length increases after create", () => {
  const before = getAllPeople().length;
  createPerson("יוסף");
  const after = getAllPeople().length;
  expect(after).toBe(before + 1);
});

// ======================
// UPDATE
// ======================

test("UPDATE – updates existing person name", () => {
  const p = createPerson("אברהם");
  updatePerson(p.id, "אבי");
  expect(p.name).toBe("אבי");
});

test("UPDATE – returns null if id not found", () => {
  const result = updatePerson(999, "לא קיים");
  expect(result).toBe(null);
});

// ======================
// DELETE
// ======================

test("DELETE – deletes existing person", () => {
  const p = createPerson("יצחק");
  const result = deletePerson(p.id);
  expect(result).toBe(true);
});

test("DELETE – returns false if id not found", () => {
  const result = deletePerson(12345);
  expect(result).toBe(false);
});
```

---

## 🎯 מקרי קצה שחשוב להגיד במבחן (גם בלי קוד)

אם שואלים אותך בעל־פה:

* מה קורה אם מעדכנים ID שלא קיים? → מחזירים `null`
* מה קורה אם מוחקים ID שלא קיים? → מחזירים `false`
* למה לבדוק אורך מערך? → לוודא ש־CREATE עובד
* למה לבדוק טיפוס מערך? → לוודא ש־READ תקין

---

## 📝 טיפ זהב למבחן

אם אין זמן לכתוב את כל הטסטים:

* כתוב **טסט אחד לכל פעולה**
* וציין במילים שיש גם בדיקות למקרי קצה

זה נחשב הבנה מלאה ✔

---

בהצלחה 💪
אם תרצה – אוכל לצמצם את כל הטסטים האלה ל**תבנית קצרה שאתה משנן בע"פ**.
