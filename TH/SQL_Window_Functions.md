# คู่มือฟังก์ชัน Window ใน SQL

## ความแตกต่างระหว่าง Aggregate Functions และ Window Functions

- **Aggregate Functions** จะรวมข้อมูลหลายแถวให้เป็นค่าเดียว
- **Window Functions** คำนวณผลลัพธ์โดยอ้างอิงกับแถวปัจจุบัน โดยไม่รวมข้อมูล

## ไวยากรณ์พื้นฐาน (Syntax)
```sql
SELECT column1, column2,
       window_function() OVER (
           PARTITION BY column_x
           ORDER BY column_y
           frame_clause
       ) AS window_result
FROM table_name;
```

## Window Frame

Window Frame คือชุดของแถวที่เกี่ยวข้องกับแถวปัจจุบัน โดยจะถูกประเมินแยกกันในแต่ละ partition

### ตัวเลือกสำหรับ Frame
- `UNBOUNDED PRECEDING` - ตั้งแต่แถวแรกของ partition
- `n PRECEDING` - n แถวก่อนแถวปัจจุบัน
- `CURRENT ROW` - แถวปัจจุบัน
- `n FOLLOWING` - n แถวหลังแถวปัจจุบัน
- `UNBOUNDED FOLLOWING` - จนถึงแถวสุดท้ายของ partition

### รูปแบบของ Frame
```sql
-- ตัวอย่างการระบุ frame
ROWS BETWEEN n PRECEDING AND CURRENT ROW
RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
GROUPS BETWEEN 1 PRECEDING AND 1 FOLLOWING
```

## การแบ่งกลุ่มและการเรียงลำดับ

### PARTITION BY
แบ่งข้อมูลเป็นกลุ่มย่อยๆ ที่จะใช้คำนวณ window function
```sql
-- ตัวอย่างการใช้ PARTITION BY
SELECT city, month, sold,
       SUM(sold) OVER (PARTITION BY city) as city_total
FROM sales;
```

### ORDER BY
กำหนดลำดับของแถวในแต่ละ partition สำหรับการคำนวณ
```sql
-- ตัวอย่างการใช้ ORDER BY
SELECT city, month, sold,
       ROW_NUMBER() OVER (PARTITION BY city ORDER BY sold DESC) as rank
FROM sales;
```

## ตัวอย่างการใช้งาน Window Functions

### การจัดลำดับ (Ranking)
```sql
-- ลำดับแบบต่อเนื่อง
SELECT city, sold,
       ROW_NUMBER() OVER (ORDER BY sold DESC) as continuous_rank,
       RANK() OVER (ORDER BY sold DESC) as rank_with_gaps,
       DENSE_RANK() OVER (ORDER BY sold DESC) as rank_without_gaps
FROM sales;
```

### การเปรียบเทียบกับแถวอื่น
```sql
-- ดูข้อมูลแถวก่อนหน้าและถัดไป
SELECT city, month, sold,
       LAG(sold) OVER (ORDER BY month) as prev_month_sales,
       LEAD(sold) OVER (ORDER BY month) as next_month_sales
FROM sales;
```

### การคำนวณผลรวมสะสม
```sql
-- ผลรวมสะสมของยอดขายตามเดือน
SELECT month, sold,
       SUM(sold) OVER (ORDER BY month) as running_total
FROM sales;
```

## ข้อควรระวัง

1. **ลำดับการทำงาน**
   - Window functions ทำงานหลังจาก WHERE, GROUP BY, HAVING
   - แต่ก่อน ORDER BY และ LIMIT

2. **Performance**
   - การใช้ PARTITION BY กับข้อมูลจำนวนมากอาจส่งผลต่อประสิทธิภาพ
   - ควรใช้ indexes ที่เหมาะสมกับคอลัมน์ที่ใช้ใน PARTITION BY และ ORDER BY

3. **Frame Specification**
   - ถ้าไม่ระบุ frame clause, ค่าเริ่มต้นจะขึ้นอยู่กับการใช้ ORDER BY
   - ถ้ามี ORDER BY: RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
   - ถ้าไม่มี ORDER BY: RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING

## ข้อจำกัดการใช้งาน

1. Window functions ใช้ได้เฉพาะใน:
   - SELECT clause
   - ORDER BY clause
   
2. ไม่สามารถใช้ใน:
   - WHERE clause
   - GROUP BY clause
   - HAVING clause

3. GROUPS frame specification รองรับเฉพาะใน PostgreSQL 11 ขึ้นไป

## คำย่อที่ใช้บ่อย

- `n PRECEDING` = ระหว่าง n แถวก่อนหน้าและแถวปัจจุบัน
- `CURRENT ROW` = แถวปัจจุบัน
- `n FOLLOWING` = ระหว่างแถวปัจจุบันและ n แถวถัดไป
- `UNBOUNDED PRECEDING` = ตั้งแต่แถวแรกถึงแถวปัจจุบัน
- `UNBOUNDED FOLLOWING` = ตั้งแต่แถวปัจจุบันถึงแถวสุดท้าย 

## ฟังก์ชัน Window ที่สำคัญ

### Ranking Functions (ฟังก์ชันจัดลำดับ)
- `ROW_NUMBER()` - ให้เลขลำดับแถวที่ไม่ซ้ำกันในแต่ละ partition
- `RANK()` - จัดลำดับโดยมีช่องว่างเมื่อค่าเท่ากัน
- `DENSE_RANK()` - จัดลำดับโดยไม่มีช่องว่างเมื่อค่าเท่ากัน

ตัวอย่าง:
```sql
SELECT city, price,
       ROW_NUMBER() OVER (ORDER BY price) as row_number,
       RANK() OVER (ORDER BY price) as rank,
       DENSE_RANK() OVER (ORDER BY price) as dense_rank
FROM sales;
```

### Distribution Functions (ฟังก์ชันการกระจาย)
- `PERCENT_RANK()` - คำนวณตำแหน่งเปอร์เซ็นต์ของแถว (0 ถึง 1)
- `CUME_DIST()` - คำนวณการกระจายสะสมของค่าในกลุ่ม

ตัวอย่าง:
```sql
SELECT city, sold,
       PERCENT_RANK() OVER (ORDER BY sold) as percent_rank,
       CUME_DIST() OVER (ORDER BY sold) as cume_dist
FROM sales;
```

### Analytic Functions (ฟังก์ชันวิเคราะห์)
- `LEAD(expr [, offset [, default]])` - ดึงค่าจากแถวถัดไป
- `LAG(expr [, offset [, default]])` - ดึงค่าจากแถวก่อนหน้า
- `FIRST_VALUE(expr)` - ค่าแรกในกรอบหน้าต่าง
- `LAST_VALUE(expr)` - ค่าสุดท้ายในกรอบหน้าต่าง
- `NTH_VALUE(expr, n)` - ค่าลำดับที่ n ในกรอบหน้าต่าง

ตัวอย่าง:
```sql
-- ดูยอดขายเดือนก่อนและเดือนถัดไป
SELECT month, sold,
       LAG(sold, 1) OVER (ORDER BY month) as prev_month,
       LEAD(sold, 1) OVER (ORDER BY month) as next_month
FROM sales;

-- ดูยอดขายสูงสุดและต่ำสุดในแต่ละกลุ่ม
SELECT city, month, sold,
       FIRST_VALUE(sold) OVER (
           PARTITION BY city 
           ORDER BY month
           RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) as first_sale,
       LAST_VALUE(sold) OVER (
           PARTITION BY city 
           ORDER BY month
           RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
       ) as last_sale
FROM sales;

-- ดูยอดขายลำดับที่ 2 ของแต่ละเมือง
SELECT city, month, sold,
       NTH_VALUE(sold, 2) OVER (
           PARTITION BY city 
           ORDER BY sold DESC
           RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
       ) as second_highest_sale
FROM sales;
```

### NTILE Function
- `NTILE(n)` - แบ่งข้อมูลออกเป็น n กลุ่มเท่าๆ กัน

ตัวอย่าง:
```sql
-- แบ่งเมืองตามยอดขายเป็น 4 กลุ่ม
SELECT city, sold,
       NTILE(4) OVER (ORDER BY sold) as quartile
FROM sales;
``` 