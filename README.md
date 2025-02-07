# MySQL บน Docker

## 📌 คู่มือการติดตั้ง MySQL ด้วย Docker

คู่มือนี้จะช่วยให้คุณติดตั้งและใช้งาน MySQL ผ่าน Docker โดยใช้ Docker Compose

---

## ✅ ข้อกำหนดเบื้องต้น

- ติดตั้ง Docker และ Docker Compose บนระบบของคุณ
- มีความเข้าใจพื้นฐานเกี่ยวกับคำสั่ง Command Line

---

## 🚀 ขั้นตอนการติดตั้ง MySQL บน Docker

### 1️⃣ สร้างไดเรกทอรีสำหรับเก็บข้อมูลของ MySQL

รันคำสั่งต่อไปนี้เพื่อสร้างโฟลเดอร์สำหรับเก็บข้อมูล:

```sh
mkdir -p ~/mysql-docker/my-db
```

---

### 2️⃣ สร้างไฟล์ `docker-compose.yml`

สร้างไฟล์ `docker-compose.yml` ภายในไดเรกทอรีที่สร้างขึ้น:

```sh
touch ~/mysql-docker/docker-compose.yml
```

เปิดไฟล์ `docker-compose.yml` และเพิ่มเนื้อหาดังนี้:

```yaml
version: '3.8'

services:
  db:
    image: mysql:8.0.33
    restart: always
    container_name: db-mysql
    environment:
      MYSQL_DATABASE: 'db'
      MYSQL_USER: 'adcmdev'
      MYSQL_PASSWORD: 'ADCMDEV@p@ssw0rd!'
      MYSQL_ROOT_PASSWORD: 'r@@tp@ssw0rd!'
      TZ: Asia/Bangkok
    ports:
      - '3306:3306'
    volumes:
      - ./my-db:/var/lib/mysql
```

---

### 3️⃣ เริ่มต้นใช้งาน MySQL

ไปที่ไดเรกทอรีที่สร้างไว้แล้วรันคำสั่งนี้:

```sh
cd ~/mysql-docker
docker-compose up -d
```

ตรวจสอบว่า Container กำลังทำงานอยู่ด้วยคำสั่ง:

```sh
docker ps
```

---

### 4️⃣ เชื่อมต่อไปยังฐานข้อมูล MySQL

สามารถเชื่อมต่อไปยัง MySQL ผ่าน Command Line ได้โดยใช้คำสั่ง:

```sh
docker exec -it db-mysql mysql -u adcmdev -p
```

ใส่รหัสผ่าน: `ADCMDEV@p@ssw0rd!`

---

### 5️⃣ หยุดและลบ Container MySQL

หากต้องการหยุดและลบ Container MySQL ให้ใช้คำสั่งนี้:

```sh
docker-compose down
```

---

### 6️⃣ เปลี่ยน Authentication Method

เพื่อเปลี่ยน Authentication Method ของผู้ใช้ `root` และ `adcmdev` เป็น `mysql_native_password` ให้รันคำสั่ง SQL ต่อไปนี้:

```sh
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'r@@tp@ssw0rd!';
FLUSH PRIVILEGES;
```

```sh
ALTER USER 'adcmdev'@'%' IDENTIFIED WITH mysql_native_password BY 'ADCMDEV@p@ssw0rd!';
FLUSH PRIVILEGES;
```

สามารถรันคำสั่งเหล่านี้ผ่าน MySQL Client หรือใช้คำสั่งต่อไปนี้ผ่าน Docker:

```sh
docker exec -it db-mysql mysql -u root -p -e "ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'r@@tp@ssw0rd!'; ALTER USER 'adcmdev'@'%' IDENTIFIED WITH mysql_native_password BY 'ADCMDEV@p@ssw0rd!'; FLUSH PRIVILEGES;"
```

จากนั้น Restart MySQL Container:

```sh
docker-compose restart db
```

---

## 🔧 การตั้งค่าเพิ่มเติม

### 📁 การจัดเก็บข้อมูลแบบถาวร (Persistent Data)

ไฟล์ `docker-compose.yml` ถูกตั้งค่าให้ใช้ Volume ของ Docker สำหรับเก็บข้อมูล MySQL:

```yaml
volumes:
  - ./my-db:/var/lib/mysql
```

ข้อมูลจะไม่หายไปแม้จะปิด Container

### 📁 การกำหนดค่าตัวแปรสภาพแวดล้อม

ค่าต่อไปนี้ถูกตั้งค่าใน `docker-compose.yml`:

- `MYSQL_DATABASE`: กำหนดชื่อฐานข้อมูลเริ่มต้น
- `MYSQL_USER`: กำหนดชื่อผู้ใช้สำหรับเชื่อมต่อฐานข้อมูล
- `MYSQL_PASSWORD`: รหัสผ่านของผู้ใช้
- `MYSQL_ROOT_PASSWORD`: รหัสผ่านของ root user
- `TZ`: ตั้งค่า Timezone ของเซิร์ฟเวอร์

### 🌐 การกำหนดค่า MySQL เพิ่มเติม

สามารถสร้างไฟล์ `my.cnf` และ Mount เป็น Volume ใน `docker-compose.yml` เพื่อปรับแต่งการตั้งค่า MySQL:

```yaml
volumes:
  - ./my-db:/var/lib/mysql
  - ./my.cnf:/etc/mysql/conf.d/my.cnf
```

---

## ⚠️ การถอนการติดตั้ง MySQL บน Docker

### 1️⃣ หยุดและลบ Container MySQL

```sh
docker-compose down -v
```

### 2️⃣ ลบไดเรกทอรีข้อมูล (ถ้าต้องการ)

```sh
rm -rf ~/mysql-docker
```

---

## 🔍 การแก้ไขปัญหา

หากเกิดปัญหา ให้ตรวจสอบ Log ของ Container ด้วยคำสั่ง:

```sh
docker-compose logs
```

---

## 🔒 ข้อควรระวังด้านความปลอดภัย

- ควรใช้รหัสผ่านที่ปลอดภัยและซับซ้อนกว่านี้ในระบบจริง
- ควรใช้ Docker Secrets หรือไฟล์ `.env` แยกเพื่อเก็บข้อมูลสำคัญ
- จำกัดการเข้าถึงพอร์ต 3306 เฉพาะในเครือข่ายที่ปลอดภัย

---

## 📚 แหล่งข้อมูลเพิ่มเติม

- [Docker Hub - MySQL](https://hub.docker.com/_/mysql)
- [เอกสาร MySQL](https://dev.mysql.com/doc/)

