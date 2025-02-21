# คู่มือการใช้ JOIN ใน SQL

## การใช้นามแฝง (Aliases)

### นามแฝงของตารางและคอลัมน์
นามแฝงเป็นชื่อชั่วคราวที่ให้กับตารางหรือคอลัมน์ในคำสั่ง SQL

```sql
-- ตัวอย่างการใช้นามแฝง
SELECT c.name AS cat_name,
       o.name AS owner_name
FROM cat AS c
JOIN owner AS o ON c.owner_id = o.id;
```

**หมายเหตุ**: คำว่า `AS` เป็นตัวเลือก แต่ควรใช้เพื่อให้โค้ดอ่านง่ายขึ้น

## ประเภทของ JOIN

### INNER JOIN
เชื่อมข้อมูลเฉพาะแถวที่มีค่าตรงกันในทั้งสองตาราง
```sql
SELECT t.toy_name, c.cat_name,
       o.name AS owner_name
FROM toy t
JOIN cat c ON t.cat_id = c.cat_id
JOIN owner o ON c.owner_id = o.id;
```

### LEFT JOIN
เชื่อมข้อมูลโดยเอาข้อมูลทั้งหมดจากตารางซ้าย และข้อมูลที่ตรงกันจากตารางขวา
```sql
SELECT t.toy_name, c.cat_name,
       o.name AS owner_name
FROM toy t
LEFT JOIN cat c ON t.cat_id = c.cat_id
LEFT JOIN owner o ON c.owner_id = o.id;
```

### SELF JOIN
เชื่อมตารางกับตัวมันเอง เช่น แสดงความสัมพันธ์แบบพ่อ-ลูก
```sql
SELECT child.cat_name AS child_name,
       mom.cat_name AS mom_name
FROM cat AS child
JOIN cat AS mom ON child.mom_id = mom.cat_id;
```

### NON-EQUI SELF JOIN
ใช้เงื่อนไขอื่นๆ นอกจากการเท่ากัน เช่น แสดงคู่ของข้อมูลที่แตกต่างกัน
```sql
SELECT a.toy_name AS toy_a,
       b.toy_name AS toy_b
FROM toy a
JOIN toy b ON a.cat_id < b.cat_id;
```

## การใช้ JOIN หลายตาราง

### Multiple Joins
สามารถเชื่อมหลายตารางพร้อมกันได้ โดยเชื่อมทีละคู่ตามลำดับ
```sql
SELECT t.toy_name, c.cat_name, o.name
FROM toy t
JOIN cat c ON t.cat_id = c.cat_id
JOIN owner o ON c.owner_id = o.id;
```

### JOIN with Multiple Conditions
ใช้เงื่อนไขหลายอันในการ JOIN โดยใช้ `AND`
```sql
SELECT c.cat_name,
       o.name AS owner_name,
       c.age AS cat_age,
       o.age AS owner_age
FROM cat c
JOIN owner o 
  ON c.owner_id = o.id
  AND c.age < o.age;
```

## ข้อควรระวัง

1. **การใช้นามแฝง**
   - ถ้าตั้งนามแฝงให้ตาราง ต้องใช้นามแฝงนั้นตลอดทั้ง query
   - ควรตั้งชื่อนามแฝงที่สื่อความหมายและไม่ซ้ำกัน

2. **การเชื่อมหลายตาราง**
   - ระวังลำดับการเชื่อมตาราง อาจส่งผลต่อประสิทธิภาพ
   - ควรใช้ indexes บนคอลัมน์ที่ใช้ในการ JOIN

3. **NULL Values**
   - INNER JOIN จะไม่แสดงแถวที่มีค่า NULL ในคอลัมน์ที่ใช้ JOIN
   - LEFT/RIGHT JOIN จะแสดงค่า NULL สำหรับข้อมูลที่ไม่ตรงกัน

## แนวทางการใช้งานที่ดี

1. ระบุเงื่อนไข JOIN ให้ชัดเจน
2. ใช้นามแฝงที่สื่อความหมาย
3. ระวังการใช้ JOIN หลายตารางที่อาจทำให้ข้อมูลซ้ำ
4. ตรวจสอบผลลัพธ์เสมอว่าได้ข้อมูลครบถ้วนตามต้องการ
5. พิจารณาใช้ indexes เพื่อเพิ่มประสิทธิภาพ 