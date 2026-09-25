# Addo Booth — Full Stack

เว็บร้านเติมเกมโทนฟ้าแบบ Full-stack สำหรับ Cloudflare Workers + D1

## มีอะไรให้แล้ว
- สมัครสมาชิก / เข้าสู่ระบบ / ออกจากระบบ
- บัญชีสมาชิกและกระเป๋าเงิน
- ขอเติมเงินและประวัติรายการ (แอดมินตรวจสอบเอง)
- สินค้าเกมและราคา
- สั่งซื้อโดยกรอก UID / Player ID
- ประวัติ/สถานะออเดอร์ฝั่งแอดมิน
- แผงแอดมิน: สินค้า, ราคา, รูปสินค้า, รูป Hero, โลโก้, QR รับเงิน, สมาชิก, เติมเงิน, ออเดอร์
- การหักเงินซื้อสินค้าใช้ D1 batch เพื่อป้องกันการหักเงินผิดพลาดจากคำสั่งซื้อซ้ำ/ยอดไม่พอ
- รหัสผ่านเก็บด้วย PBKDF2 + salt และ session cookie เป็น HttpOnly/Secure

## สำคัญ: บัญชีแอดมิน
บัญชีที่สมัครเป็น **บัญชีแรกของฐานข้อมูล** จะถูกตั้งเป็น `admin` อัตโนมัติ เพื่อให้เริ่มระบบได้ง่าย หลังจากนั้นบัญชีใหม่จะเป็น `user` เท่านั้น

## วิธีติดตั้งบน Cloudflare
1. สร้าง Worker จาก Git repository หรือใช้ Wrangler (โปรเจกต์นี้ไม่ใช่ Static Upload)
2. สร้าง D1 database ชื่อ `addo-booth-db`
3. ใน Worker ไปที่ Bindings → Add binding → D1 database → ตั้ง Variable name เป็น `DB` และเลือก `addo-booth-db`
4. รัน `npx wrangler d1 execute addo-booth-db --remote --file=schema.sql` หนึ่งครั้งเพื่อสร้างตาราง/สินค้าเริ่มต้น
5. Deploy Worker ด้วย `npx wrangler deploy`
6. เปิดเว็บ สมัครบัญชีแรก บัญชีนั้นจะเป็น Admin

Cloudflare เอกสาร D1: https://developers.cloudflare.com/d1/get-started/
Cloudflare เอกสาร Workers Static Assets: https://developers.cloudflare.com/workers/static-assets/

## หมายเหตุเรื่องการเติมเงินจริง/เติมเกมจริง
ระบบนี้เป็นร้านและกระเป๋าเงินแบบ manual approval: ลูกค้าส่งคำขอเติมเงิน แล้วแอดมินตรวจสอบก่อนเพิ่มยอด ระบบยัง **ไม่ได้เชื่อม Payment Gateway อัตโนมัติ** และยังไม่ได้เชื่อม API ผู้ให้บริการเกมเพื่อส่งเพชร/UC/Robux อัตโนมัติ

หากจะเปิดรับเงินจริง ควรตรวจสอบข้อกำหนดของผู้ให้บริการชำระเงินและกฎหมายที่เกี่ยวข้องก่อนใช้งานจริง
