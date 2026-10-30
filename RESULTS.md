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