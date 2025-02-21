# คู่มือฟังก์ชันมาตรฐาน SQL

## 1. ฟังก์ชันข้อความ (Text Functions)

### การต่อข้อความ (Concatenation)
```sql
-- ใช้ || เพื่อต่อข้อความ
SELECT 'Hi ' || 'there!';  -- result: Hi there!

-- ใช้ได้กับตัวเลขด้วย
SELECT '4' || '2';  -- result: 42
```

### การค้นหารูปแบบ (Pattern Matching)
```sql
-- ใช้ _ แทนตัวอักษร 1 ตัว
-- ใช้ % แทนตัวอักษรกี่ตัวก็ได้
SELECT name FROM names WHERE name LIKE '_atherine';
SELECT name FROM names WHERE name LIKE '%a';
```

### ฟังก์ชันจัดการข้อความ
```sql
-- นับความยาวข้อความ
SELECT LENGTH('LearnSQL.com');  -- result: 12

-- แปลงเป็นตัวพิมพ์เล็ก
SELECT LOWER('LEARNSQL.COM');  -- result: learnsql.com

-- แปลงเป็นตัวพิมพ์ใหญ่
SELECT UPPER('learnsql.com');  -- result: LEARNSQL.COM

-- ตัดข้อความ
SELECT SUBSTRING('LearnSQL.com', 0, 6);  -- result: Learn

-- แทนที่ข้อความ
SELECT REPLACE('LearnSQL.com', 'SQL', 'Python');
-- result: LearnPython.com
```

## 2. ฟังก์ชันตัวเลข (Numeric Functions)

### การคำนวณพื้นฐาน
```sql
-- ใช้ +, -, *, / สำหรับการคำนวณพื้นฐาน
SELECT 60 * 60 * 24 * 7;  -- จำนวนวินาทีใน 1 สัปดาห์
```

### การแปลงประเภทข้อมูล (Casting)
```sql
-- แปลงเป็นจำนวนเต็ม
SELECT CAST(1234.567 AS integer);  -- result: 1234

-- แปลงคอลัมน์เป็น double precision
SELECT CAST(column AS double precision);
```

### ฟังก์ชันคณิตศาสตร์
```sql
-- หารเอาเศษ
SELECT MOD(13, 2);  -- result: 1

-- ปัดเศษ
SELECT ROUND(1234.5678);  -- result: 1235
SELECT ROUND(1234.5678, 3);  -- result: 1234.568

-- ปัดขึ้น/ลง
SELECT CEIL(-13.9);  -- result: -13
SELECT FLOOR(-13.2);  -- result: -14

-- ค่าสัมบูรณ์
SELECT ABS(-12);  -- result: 12

-- รากที่สอง
SELECT SQRT(9);  -- result: 3
```

## 3. การจัดการค่า NULL

### การตรวจสอบ NULL
```sql
-- หาแถวที่มีค่า NULL ในคอลัมน์ price
WHERE price IS NULL

-- หาแถวที่มีค่า NULL ในคอลัมน์ weight
WHERE weight IS NULL
```

### COALESCE Function
```sql
-- แทนค่า NULL ด้วยค่าที่กำหนด
SELECT COALESCE(domain, 'domain missing')
FROM contacts;
```

### NULLIF Function
```sql
-- เปรียบเทียบค่าและคืน NULL ถ้าเท่ากัน
SELECT NULLIF(last_month, 0)
FROM video_plays;
```

## 4. CASE WHEN

### การใช้งานพื้นฐาน
```sql
SELECT 
    CASE fee
        WHEN 50 THEN 'normal'
        WHEN 30 THEN 'reduced'
        WHEN 0 THEN 'free'
        ELSE 'not available'
    END AS tariff
FROM ticket_types;
```

### การใช้เงื่อนไขซับซ้อน
```sql
SELECT
    CASE
        WHEN score >= 90 THEN 'A'
        WHEN score > 60 THEN 'B'
        ELSE 'F'
    END AS grade
FROM test_results;
```

## 5. วันที่และเวลา (Date and Time)

### รูปแบบวันที่
```sql
-- รูปแบบพื้นฐาน: YYYY-MM-DD HH:MM:SS.ssssss+TZ
2021-12-31 14:39:53.662522-05

-- ส่วนประกอบ:
-- YYYY = ปี 4 หลัก
-- MM = เดือน 2 หลัก (01-12)
-- DD = วัน 2 หลัก
-- HH = ชั่วโมง (00-23)
-- MM = นาที
-- SS = วินาที
-- ssssss = ไมโครวินาที
-- TZ = เขตเวลา
```

### ฟังก์ชันวันที่
```sql
-- ดึงวันที่ปัจจุบัน
SELECT CURRENT_DATE;

-- ดึงเวลาปัจจุบัน
SELECT CURRENT_TIME;

-- ดึง timestamp ปัจจุบัน
SELECT CURRENT_TIMESTAMP;

-- คำนวณระยะเวลา
SELECT CAST('2021-12-31 23:59:59' AS timestamp) 
     - CAST('2021-06-01 12:00:00' AS timestamp) 
AS time_diff;

-- เพิ่ม/ลด interval
SELECT event_date + INTERVAL '3' MONTH
FROM calendar
WHERE event_date BETWEEN CURRENT_DATE AND
      CURRENT_DATE + INTERVAL '3' MONTH;
```

## ข้อควรระวัง

1. **การหารด้วยศูนย์**
   - ใช้ NULLIF เพื่อป้องกันการหารด้วยศูนย์
   - ตัวอย่าง: `count / NULLIF(count_all, 0)`

2. **ความแม่นยำของการคำนวณ**
   - ใช้ DECIMAL สำหรับการคำนวณที่ต้องการความแม่นยำ
   - ระวังการใช้ FLOAT/REAL ที่อาจมีความคลาดเคลื่อน

3. **Time Zones**
   - ระบุ time zone ให้ชัดเจนเมื่อทำงานกับเวลา
   - ใช้ AT TIME ZONE เพื่อแปลงระหว่างเขตเวลา

4. **การแปลงประเภทข้อมูล**
   - ตรวจสอบเอกสารของฐานข้อมูลสำหรับรูปแบบที่รองรับ
   - ระบุความแม่นยำเมื่อแปลงเป็นตัวเลขทศนิยม 