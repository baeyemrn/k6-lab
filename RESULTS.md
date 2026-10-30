Step0.1
http_req_duration..............: avg=2.45s min=2.45s med=2.45s max=2.45s p(90)=2.45s p(95)=2.45s
Step0.2
จำนวน Requests = 63
Request rate = 2.793562/s
p(95) = 1.94s

why the rate stays below 5 requests per
second although 5 users run for the whole 20 seconds. Look at what one virtual user does in one
iteration, and how long that takes.
ตอบ มี Virtual Users 5 คนก็จริง แต่แต่ละคนไม่ได้ส่ง Request อย่างต่อเนื่อง เพราะในหนึ่ง Iteration ต้องรอ Response จาก Server และมีการพัก 1 วินาทีด้วยคำสั่ง sleep(1) ทำให้จำนวน Requests ต่อวินาทีต่ำกว่า 5 ได้

Step0.3
iterations = 20

Step 0.6
k6 run 06-thresholds.js : Exit Code = running (0m31.2s), 0/3 VUs, 66 complete and 0 interrupted iterations
default ✓ [======================================] 3 VUs  30s

k6 run -e P95=300 06-thresholds.js : Exit Code = running (0m31.2s), 0/3 VUs, 45 complete and 0 interrupted iterations
default ✓ [======================================] 3 VUs  30s
ERRO[0031] thresholds on metrics 'http_req_duration' have been crossed 

Step 0.7
ผลลัพธ์รอบแรก
p(95) = 1.31s failed
p(99) = 1.46s passed

exit code = 99

Step 0.8
### คำอธิบาย Bug Hunt
สาเหตุของ Bug เกิดจากการกำหนดชื่อ Ingredient และ Tool ไม่ตรงกับข้อมูลที่ API รองรับ โดยจากการใช้ไฟล์ `explore.js` เพื่อสำรวจข้อมูล API พบว่าชื่อ Ingredient ที่ถูกต้องคือ `"Pepperoni"` และชื่อ Tool คือ `"Knife"` ซึ่งตัวอักษรตัวแรกเป็นตัวพิมพ์ใหญ่ แต่ในโค้ดเดิมกำหนดเป็น `"pepperoni"` และ `"knife"` ทำให้ API อาจไม่สามารถตรวจสอบหรือยกเว้น Ingredient และ Tool เหล่านี้ได้อย่างถูกต้อง

จึงแก้ไขโค้ดให้ชื่อข้อมูลตรงกับที่ API รองรับ ดังนี้
- `excludedIngredients: ['Pepperoni']`
- `excludedTools: ['Knife']`

หลังจากแก้ไขแล้ว การทดสอบสามารถตรวจสอบได้ว่า Pizza ที่สร้างขึ้นไม่มี Pepperoni เป็นส่วนประกอบ และไม่ได้ใช้ Knife ในการทำ Pizza

การทดสอบหลาย Iterations ช่วยเพิ่มโอกาสในการพบผลลัพธ์ที่ไม่ตรงกับความต้องการของลูกค้า เนื่องจากในแต่ละ Iteration อาจได้ Pizza ที่มีส่วนประกอบแตกต่างกัน หากทดสอบเพียงครั้งเดียวอาจไม่พบปัญหาบางกรณี แต่เมื่อทดสอบหลายครั้งจะช่วยให้ตรวจสอบผลลัพธ์ได้หลากหลายมากขึ้น และเพิ่มโอกาสในการค้นพบ Bug ที่อาจเกิดขึ้นได้

Step A2

Request Rate = 0.7/s
p(95) Response Time = 828.66ms
Virtual Users = 3
Request Duration  = 3 นาที

Step A3
Response Time เพิ่มขึ้นหรือไม่ เมื่อเพิ่ม Users จาก 3 เป็น 5?
ตอบ  ลดลงเหลือ p(95)=544.29ms