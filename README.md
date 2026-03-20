# Lab 03: Product Management System (ระบบจัดการสินค้า)

โปรแกรมจำลองระบบจัดการสินค้าที่พัฒนาด้วยภาษา Dart โดยประยุกต์ใช้หลักการ Object-Oriented Programming (OOP) ได้แก่ Encapsulation, Inheritance และ Polymorphism

## 📋 รายละเอียดโครงสร้างโค้ด (Code Description)

### 1. Class แม่: Product
เป็นคลาสหลักที่เก็บคุณสมบัติพื้นฐานของสินค้าทุกประเภท โดยมีการใช้ **Encapsulation** เพื่อปกป้องข้อมูลสำคัญ:
* **Private Property (`_id`):** กำหนด `_id` เป็น private เพื่อป้องกันการแก้ไขค่าโดยตรงจากภายนอก และสร้าง `getter` เพื่อให้อ่านค่าได้อย่างเดียว
* **Validation via Setter:** ตัวแปร `price` ถูกควบคุมผ่าน `setter` เพื่อตรวจสอบความถูกต้อง โดยจะป้องกันไม่ให้กำหนดราคาสินค้าเป็นค่าติดลบหรือ 0
* **Method `restock()`:** สำหรับเพิ่มจำนวนสินค้า โดยมี Logic จัดการกรณีที่สินค้ายังไม่มีสต็อก (null) ให้เริ่มนับจาก 0
* **Method `applyDiscount()`:** คำนวณราคาสินค้าใหม่ตามเปอร์เซ็นต์ส่วนลดที่ระบุ

### 2. Class ลูก: DigitalProduct (สืบทอดจาก Product)
* **Inheritance:** สืบทอดคุณสมบัติทั้งหมดมาจากคลาส `Product`
* **Extension:** เพิ่มคุณสมบัติเฉพาะคือ `fileSizeMB` (ขนาดไฟล์)
* **Overriding:** ทำการ Override เมธอด `showInfo()` ให้แสดงข้อมูลพื้นฐานจากแม่ และแสดงข้อมูลเฉพาะ (Type: Digital และ File Size) เพิ่มเติม

### 3. Class ลูก: FoodProduct (สืบทอดจาก Product)
* **Inheritance:** สืบทอดคุณสมบัติทั้งหมดมาจากคลาส `Product`
* **Extension:** เพิ่มคุณสมบัติเฉพาะคือ `expireDate` (วันหมดอายุ)
* **Overriding:** ทำการ Override เมธอด `showInfo()` ให้แสดงข้อมูลพื้นฐานจากแม่ และแสดงข้อมูลเฉพาะ (Type: Food และ Expire Date) เพิ่มเติม

### 4. การทำงานหลัก (main) และ Polymorphism
* มีการสร้างสินค้าทั้ง 3 รูปแบบ (`Product`, `DigitalProduct`, `FoodProduct`)
* **Polymorphism Implementation:** เก็บวัตถุทั้งหมดลงใน `List<Product>` และวนลูปสั่งคำสั่ง `showInfo()`
* ผลลัพธ์คือโปรแกรมสามารถเรียกใช้เมธอดที่ถูกต้องของวัตถุแต่ละประเภทได้เอง (เช่น เรียก `showInfo` ของ DigitalProduct เมื่อเจอสินค้าดิจิทัล)

---

## 💻 Source Code

```dart
void main() {
  print('--- เริ่มต้นโปรแกรม ---');

  // ชิ้นที่ 1: สินค้าทั่วไป (Product)
  Product p1 = Product(id: 'P002', name: 'Gaming Mouse', price: 890.0, stock: 30);
  print('New Product created: ${p1.id} (${p1.name})');

  // ชิ้นที่ 2: สินค้าดิจิทัล (DigitalProduct)
  DigitalProduct d1 = DigitalProduct(
    id: 'D002',
    name: 'Online Course Python',
    price: 499.0,
    fileSizeMB: 250.0,
  );
  print('New Product created: ${d1.id} (${d1.name})');

  // ชิ้นที่ 3: สินค้าอาหาร (FoodProduct)
  FoodProduct f1 = FoodProduct(
    id: 'F002',
    name: 'Chocolate Bar',
    price: 45.0,
    stock: 100,
    expireDate: '2026-12-31',
  );
  print('New Product created: ${f1.id} (${f1.name})');

  print('\n--- ทดสอบ Setter Price (ป้องกันค่าติดลบ) ---');
  p1.price = -500; // ควรแสดงข้อความเตือนและไม่เปลี่ยนค่า

  print('\n--- ทดสอบ Method ต่างๆ ---');
  print('ลดราคา Gaming Mouse 10%');
  p1.applyDiscount(10);

  print('เพิ่มสต็อก Chocolate Bar +50');
  f1.restock(50);

  print('\n------------------------------------------------');
  print('--- แสดงผลข้อมูลสินค้าทั้งหมด (Polymorphism) ---');
  print('------------------------------------------------');

  List<Product> productList = [p1, d1, f1];

  for (var item in productList) {
    item.showInfo();
    print('----------------');
  }
}

class Product {
  String _id;
  String name;
  double _price;
  int? stock;

  Product({
    required String id,
    required this.name,
    required double price,
    this.stock,
  }) : _id = id,
       _price = price;

  String get id => _id;
  double get price => _price;

  set price(double newPrice) {
    if (newPrice > 0) {
      _price = newPrice;
    } else {
      print('ราคาไม่ถูกต้อง (ต้อง > 0) -> ไม่เปลี่ยนค่า');
    }
  }

  void applyDiscount(double percent) {
    if (percent >= 0 && percent <= 100) {
      _price = _price - (_price * (percent / 100));
    } else {
      print('เปอร์เซ็นต์ส่วนลดไม่ถูกต้อง');
    }
  }

  void restock(int amount) {
    stock = (stock ?? 0) + amount;
  }

  void showInfo() {
    print('ID: $_id');
    print('Name: $name');
    print('Price: $_price');
    print('Stock: ${stock ?? "ยังไม่ระบุสต็อก"}');
  }
}

class DigitalProduct extends Product {
  double fileSizeMB;

  DigitalProduct({
    required String id,
    required String name,
    required double price,
    int? stock,
    required this.fileSizeMB,
  }) : super(id: id, name: name, price: price, stock: stock);

  @override
  void showInfo() {
    super.showInfo();
    print('Type: Digital');
    print('File Size: $fileSizeMB MB');
  }
}

class FoodProduct extends Product {
  String expireDate;

  FoodProduct({
    required String id,
    required String name,
    required double price,
    int? stock,
    required this.expireDate,
  }) : super(id: id, name: name, price: price, stock: stock);

  @override
  void showInfo() {
    super.showInfo();
    print('Type: Food');
    print('Expire Date: $expireDate');
  }
}
```
