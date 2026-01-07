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

---

# 🚀 שלב מתקדם – אפליקציית ניהול בנק (רמת מבחן גבוהה)

כאן אנחנו עולים רמה 👇
זה כבר **OOP מתקדם + ירושה + CRUD + private fields + לוגיקה**.

המטרה: להרגיש מוכן גם לשאלות ה"קשות".

---

## 🧠 הרעיון

אפליקציה שמנהלת **חשבונות בנק**:

* יצירת חשבון
* הפקדה
* משיכה
* בדיקת יתרה
* CRUD על חשבונות
* שימוש ב־**private field (#balance)**

---

## 📁 מבנה תיקיות (כמו במבחן)

```
bank-app/
│
├─ app.js
│
├─ models/
│   ├─ Account.js
│   └─ SavingsAccount.js
│
├─ services/
│   └─ bankService.js
│
└─ tests/
    └─ bank.test.js
```

---

## 🧱 מחלקת בסיס עם Private Field

### 📄 models/Account.js

```js
class Account {
  #balance; // private field

  constructor(owner) {
    this.owner = owner;
    this.#balance = 0;
  }

  deposit(amount) {
    if (amount <= 0) return false;
    this.#balance += amount;
    return true;
  }

  withdraw(amount) {
    if (amount <= 0) return false;
    if (amount > this.#balance) return false;
    this.#balance -= amount;
    return true;
  }

  getBalance() {
    return this.#balance;
  }
}

export default Account;
```

📌 נקודות למבחן:

* `#balance` **לא נגיש מבחוץ**
* גישה רק דרך מתודות
* הגנה על נתונים (Encapsulation)

---

## 🧬 ירושה – חשבון חיסכון

### 📄 models/SavingsAccount.js

```js
import Account from "./Account.js";

class SavingsAccount extends Account {
  constructor(owner, interestRate) {
    super(owner);
    this.interestRate = interestRate;
  }

  addInterest() {
    const interest = this.getBalance() * this.interestRate;
    this.deposit(interest);
  }
}

export default SavingsAccount;
```

📌 כאן רואים:

* שימוש במתודות של המחלקה האב
* בלי גישה ישירה ל־#balance

---

## 🔁 CRUD + ניהול אפליקציה

### 📄 services/bankService.js

```js
import Account from "../models/Account.js";
import SavingsAccount from "../models/SavingsAccount.js";

let accounts = [];
let nextId = 1;

// CREATE
export function createAccount(owner, type = "regular") {
  const account = type === "savings"
    ? new SavingsAccount(owner, 0.05)
    : new Account(owner);

  account.id = nextId++;
  accounts.push(account);
  return account;
}

// READ
export function getAllAccounts() {
  return accounts;
}

// UPDATE (deposit / withdraw)
export function depositToAccount(id, amount) {
  const acc = accounts.find(a => a.id === id);
  if (!acc) return false;
  return acc.deposit(amount);
}

export function withdrawFromAccount(id, amount) {
  const acc = accounts.find(a => a.id === id);
  if (!acc) return false;
  return acc.withdraw(amount);
}

// DELETE
export function deleteAccount(id) {
  const index = accounts.findIndex(a => a.id === id);
  if (index === -1) return false;
  accounts.splice(index, 1);
  return true;
}
```

---

## ▶️ הרצה והדגמה

### 📄 app.js

```js
import {
  createAccount,
  depositToAccount,
  withdrawFromAccount,
  getAllAccounts
} from "./services/bankService.js";

const acc1 = createAccount("דוד");
depositToAccount(acc1.id, 500);
withdrawFromAccount(acc1.id, 200);
console.log(acc1.getBalance()); // 300

const acc2 = createAccount("משה", "savings");
depositToAccount(acc2.id, 1000);
acc2.addInterest();
console.log(acc2.getBalance());

console.log(getAllAccounts());
```

---

## 🧪 מקרי קצה למבחן (מאוד חשוב)

* הפקדה שלילית → false
* משיכה מעבר ליתרה → false
* גישה ל־balance מבחוץ → ❌ שגיאה
* מחיקה של חשבון שלא קיים → false

---

## 📝 משפטי זהב למבחן

* "אני משתמש ב־private field כדי להגן על היתרה"
* "אין גישה ישירה לנתונים רגישים"
* "הלוגיקה נמצאת ב־service, לא ב־app"
* "הירושה מאפשרת הרחבה בלי לשבור קוד"

---

אם אתה מבין את החלק הזה – אתה **ברמה גבוהה למבחן** 💪

רוצה עכשיו:

* סימולציית מבחן קשה ❓
* שאלות אמריקאיות עם הטעיות ❌✔
* או תרגיל שאתה צריך לכתוב לבד בלי להסתכל

---

# 🏗️ פרויקט נוסף – מערכת ניהול ספרייה (Library Management)

זה פרויקט **שונה לגמרי** מהבנק, אבל באותה רמה ואף יותר ברור למבחן.
מתאים במיוחד לשאלות על:

* OOP
* ירושה
* private fields
* CRUD
* לוגיקה של מערכת

---

## 🧠 הרעיון הכללי

מערכת שמנהלת **ספרייה**:

* יש ספרים
* יש ספרים מושאלים
* אפשר להוסיף / למחוק / לעדכן ספר
* לכל ספר יש **מלאי פרטי (private)**
* אי אפשר להשאיל יותר ספרים ממה שיש

---

## 📁 מבנה תיקיות

```
library-app/
│
├─ app.js
│
├─ models/
│   ├─ Book.js
│   └─ BorrowableBook.js
│
├─ services/
│   └─ libraryService.js
│
└─ tests/
    └─ library.test.js
```

---

## 📘 מחלקת בסיס – Book

### 📄 models/Book.js

```js
class Book {
  #stock; // private – כמות עותקים

  constructor(title, author, stock) {
    this.title = title;
    this.author = author;
    this.#stock = stock;
  }

  getStock() {
    return this.#stock;
  }

  increaseStock(amount) {
    if (amount <= 0) return false;
    this.#stock += amount;
    return true;
  }

  decreaseStock(amount) {
    if (amount <= 0) return false;
    if (amount > this.#stock) return false;
    this.#stock -= amount;
    return true;
  }
}

export default Book;
```

### 📌 הסבר:

* `#stock` הוא מידע רגיש → private
* אין גישה ישירה מבחוץ
* כל שינוי עובר דרך מתודות

---

## 📗 ירושה – BorrowableBook

### 📄 models/BorrowableBook.js

```js
import Book from "./Book.js";

class BorrowableBook extends Book {
  constructor(title, author, stock) {
    super(title, author, stock);
  }

  borrow() {
    return this.decreaseStock(1);
  }

  returnBook() {
    return this.increaseStock(1);
  }
}

export default BorrowableBook;
```

### 📌 הסבר:

* ירושה מספר רגיל
* שימוש במתודות של האב
* אין גישה ל־#stock ישירות

---

## 🔁 ניהול ספרייה + CRUD

### 📄 services/libraryService.js

```js
import BorrowableBook from "../models/BorrowableBook.js";

let books = [];
let nextId = 1;

// CREATE
export function addBook(title, author, stock) {
  const book = new BorrowableBook(title, author, stock);
  book.id = nextId++;
  books.push(book);
  return book;
}

// READ
export function getAllBooks() {
  return books;
}

// UPDATE
export function updateBookTitle(id, newTitle) {
  const book = books.find(b => b.id === id);
  if (!book) return null;
  book.title = newTitle;
  return book;
}

// DELETE
export function removeBook(id) {
  const index = books.findIndex(b => b.id === id);
  if (index === -1) return false;
  books.splice(index, 1);
  return true;
}

// פעולות מערכת
export function borrowBook(id) {
  const book = books.find(b => b.id === id);
  if (!book) return false;
  return book.borrow();
}

export function returnBook(id) {
  const book = books.find(b => b.id === id);
  if (!book) return false;
  return book.returnBook();
}
```

---

## ▶️ app.js – הרצה

```js
import {
  addBook,
  getAllBooks,
  borrowBook,
  returnBook
} from "./services/libraryService.js";

const b1 = addBook("JavaScript Basics", "Dan", 2);
borrowBook(b1.id);
borrowBook(b1.id);
borrowBook(b1.id); // false – אין מלאי

returnBook(b1.id);

console.log(getAllBooks());
console.log(b1.getStock());
```

---

## 🧪 מקרי קצה שחייבים לדעת

* השאלה מעבר למלאי → false
* החזרת ספר מעלה מלאי
* גישה ל־stock ישירות → שגיאה
* מחיקת ספר שלא קיים → false

---

## 📝 איך להסביר את זה במבחן

* "הפרדתי בין מודלים ללוגיקה"
* "השתמשתי ב־private field כדי להגן על המלאי"
* "הירושה מאפשרת להרחיב פונקציונליות"
* "כל CRUD מטופל בשירות נפרד"

---

💡 אם אתה שולט גם בפרויקט הזה וגם בבנק – אתה **מוכן ברמה גבוהה מאוד**.

רוצה עכשיו:

* סימולציית מבחן רק על הספרייה 📚
* או תרגיל שאתה כותב לבד לפי דרישות ✍️

---

# 🏭 פרויקט מלא – מערכת ניהול מחסן (Inventory Management)

זה פרויקט **רציני ומלא**, ברמה שמתאימה למבחן קשה:

* OOP
* ירושה
* private fields
* CRUD מלא
* שירות נפרד
* טסטים לכל פעולה + מקרי קצה

---

## 🧠 הרעיון

מערכת שמנהלת **מחסן מוצרים**:

* הוספה ומחיקה של מוצרים
* ניהול מלאי (כניסה / יציאה)
* מוצר רגיל ומוצר עם תוקף
* הגנה על הכמות באמצעות `private`

---

## 📁 חלוקה לקבצים (חובה לדעת למבחן)

```
warehouse-app/
│
├─ app.js                 // הרצה והדגמה
│
├─ models/
│   ├─ Product.js         // מחלקת בסיס
│   └─ PerishableProduct.js // ירושה
│
├─ services/
│   └─ warehouseService.js // CRUD ולוגיקה
│
└─ tests/
    └─ warehouse.test.js   // טסטים
```

📌 הסבר חשוב:

* `models` → רק מחלקות
* `services` → לוגיקה ו־CRUD
* `app.js` → לא לוגיקה, רק שימוש
* `tests` → בדיקות

---

## 📦 models/Product.js

```js
class Product {
  #quantity;

  constructor(name, quantity) {
    this.name = name;
    this.#quantity = quantity;
  }

  getQuantity() {
    return this.#quantity;
  }

  addStock(amount) {
    if (amount <= 0) return false;
    this.#quantity += amount;
    return true;
  }

  removeStock(amount) {
    if (amount <= 0) return false;
    if (amount > this.#quantity) return false;
    this.#quantity -= amount;
    return true;
  }
}

export default Product;
```

---

## 🧬 models/PerishableProduct.js

```js
import Product from "./Product.js";

class PerishableProduct extends Product {
  constructor(name, quantity, expiryDate) {
    super(name, quantity);
    this.expiryDate = expiryDate;
  }

  isExpired(today) {
    return today > this.expiryDate;
  }
}

export default PerishableProduct;
```

---

## 🔁 services/warehouseService.js (CRUD מלא)

```js
import Product from "../models/Product.js";
import PerishableProduct from "../models/PerishableProduct.js";

let products = [];
let nextId = 1;

export function resetWarehouse() {
  products = [];
  nextId = 1;
}

// CREATE
export function addProduct(name, quantity, expiryDate = null) {
  const product = expiryDate
    ? new PerishableProduct(name, quantity, expiryDate)
    : new Product(name, quantity);

  product.id = nextId++;
  products.push(product);
  return product;
}

// READ
export function getAllProducts() {
  return products;
}

// UPDATE
export function addStockToProduct(id, amount) {
  const product = products.find(p => p.id === id);
  if (!product) return false;
  return product.addStock(amount);
}

export function removeStockFromProduct(id, amount) {
  const product = products.find(p => p.id === id);
  if (!product) return false;
  return product.removeStock(amount);
}

// DELETE
export function deleteProduct(id) {
  const index = products.findIndex(p => p.id === id);
  if (index === -1) return false;
  products.splice(index, 1);
  return true;
}
```

---

## ▶️ app.js (הרצה)

```js
import {
  addProduct,
  addStockToProduct,
  removeStockFromProduct,
  getAllProducts
} from "./services/warehouseService.js";

const p1 = addProduct("אורז", 10);
removeStockFromProduct(p1.id, 3);
addStockToProduct(p1.id, 5);

const p2 = addProduct("חלב", 5, "2026-01-01");

console.log(p1.getQuantity());
console.log(getAllProducts());
```

---

## 🧪 tests/warehouse.test.js (טסטים רציניים)

```js
import {
  addProduct,
  getAllProducts,
  addStockToProduct,
  removeStockFromProduct,
  deleteProduct,
  resetWarehouse
} from "../services/warehouseService.js";

beforeEach(() => {
  resetWarehouse();
});

// CREATE
test("CREATE – product is added", () => {
  addProduct("אורז", 5);
  expect(getAllProducts().length).toBe(1);
});

// READ
test("READ – returns array", () => {
  expect(Array.isArray(getAllProducts())).toBe(true);
});

// UPDATE
test("UPDATE – add stock", () => {
  const p = addProduct("קמח", 5);
  addStockToProduct(p.id, 3);
  expect(p.getQuantity()).toBe(8);
});

test("UPDATE – cannot remove too much stock", () => {
  const p = addProduct("סוכר", 2);
  const result = removeStockFromProduct(p.id, 5);
  expect(result).toBe(false);
});

// DELETE
test("DELETE – product removed", () => {
  const p = addProduct("שמן", 1);
  deleteProduct(p.id);
  expect(getAllProducts().length).toBe(0);
});

test("DELETE – non existing id", () => {
  const result = deleteProduct(999);
  expect(result).toBe(false);
});
```

---

## 📝 מה להגיד במבחן

* "שמתי את הכמות כ־private כדי למנוע שינוי ישיר"
* "כל CRUD מנוהל דרך service"
* "הוספתי פונקציית reset בשביל טסטים"
* "בדקתי גם מקרי קצה"

---

אם אתה שולט בפרויקט הזה – אתה **ברמה גבוהה מאוד**.
רוצה שאעשה לך עכשיו **סימולציית מבחן קשה רק על המחסן**?
