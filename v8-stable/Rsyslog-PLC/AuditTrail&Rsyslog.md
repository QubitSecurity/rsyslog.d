

# 🔥 전체 목표

```text id="p3k9n8"
✔ AIX audit 로그 → 외부(SIEM) 전송
✔ /audit/trail 디스크 증가 문제 해결
```

---

# ✅ 방법 1 — trail 유지 + 주기적 정리

## 🔹 방법

👉 **audit 로그를 로컬(trail)에 저장하면서, 동시에 외부로 전송**

## 🔹 설명

👉 로그를 파일로 보관하기 때문에 **유실 없이 안정적** <br>
👉 대신 **디스크 사용 증가 → cron으로 정리 필요**

---

## 📌 구조

```text id="1u7w5m"
audit → bin → trail(로컬 저장)
      → stream → syslog → (rsyslog) → SIEM
```

---

## 1️⃣ audit 설정

```bash id="qkz4hf"
vi /etc/security/audit/config
```

```text id="8z2c8c"
start:
    binmode = on
    streammode = on
```

---

## 2️⃣ streamcmds 설정 (핵심 ⭐)

```bash id="9j4bws"
vi /etc/security/audit/streamcmds
```

```bash id="iq7y1p"
/usr/sbin/auditpr -v | /usr/bin/logger -p local0.info
```

👉 audit → syslog 전달 <br>
👉 `auditstream` 직접 실행 ❌

---

## 3️⃣ rsyslog 설정 (옵션 / 권장 👍)

```bash id="k3j6tz"
vi /etc/rsyslog.conf
```

### ✔ 전체 로그 전송

```bash id="u6u0fc"
*.* @@10.10.10.180:514
```

### ✔ audit만 전송

```bash id="a0u41t"
local0.* @@10.10.10.180:514
```

👉 둘 중 하나만 사용 <br>
👉 같이 쓰면 **audit 로그 중복 전송**

---

## 4️⃣ rsyslog 큐 설정 (권장)

```bash id="h3c2p8"
$ActionQueueType LinkedList
$ActionQueueFileName auditqueue
$ActionQueueMaxDiskSpace 1g
$ActionResumeRetryCount -1
$ActionQueueSaveOnShutdown on
```

---

## 5️⃣ 서비스 재시작

```bash id="hz7v8o"
audit shutdown
audit start

stopsrc -s syslogd
startsrc -s syslogd
```

---

## 6️⃣ trail 정리 (필수)

```bash id="yxnt9f"
crontab -e
```

```bash id="r1mjrv"
0 * * * * find /audit -name "trail*" -mtime +3 -exec rm -f {} \;
```

---
<!--
## ✔ 특징 요약

* 안정성 최고 (로그 유실 없음)
* 디스크 사용 있음
* 운영 환경에 가장 적합
-->
---

# ✅ 방법 2 — trail 없이 stream 전송

## 🔹 방법

👉 **audit 로그를 파일로 저장하지 않고 바로 외부로 전송**

## 🔹 설명

👉 디스크 문제는 해결하며, Rsyslog 기반으로 로그 전송

---

## 📌 구조

```text id="9m8d2a"
audit → stream → syslog → (rsyslog) → SIEM
```

---

## 1️⃣ audit 설정

```bash id="0qf7gx"
vi /etc/security/audit/config
```

```text id="c5cn6r"
start:
    binmode = off
    streammode = on
```

---

## 2️⃣ streamcmds 설정 (필수 ⭐)

```bash id="8y6yqk"
vi /etc/security/audit/streamcmds
```

```bash id="c6g6n5"
/usr/sbin/auditpr -v | /usr/bin/logger -p local0.info
```

---

## 3️⃣ rsyslog 설정 (옵션 / 권장 👍)

```bash id="r6s5dz"
vi /etc/rsyslog.conf
```

### ✔ 전체 로그 전송

```bash id="7dztb0"
*.* @@10.10.10.180:514
```

### ✔ audit만 전송

```bash id="8n9l3o"
local0.* @@10.10.10.180:514
```
👉 둘 중 하나만 사용 
👉 같이 쓰면 **audit 로그 중복 전송**

---

## 4️⃣ rsyslog 큐 설정 (권장 ⭐)

```bash id="f9u7bn"
$ActionQueueType LinkedList
$ActionQueueFileName auditqueue
$ActionQueueMaxDiskSpace 1g
$ActionResumeRetryCount -1
$ActionQueueSaveOnShutdown on
```


---

## 5️⃣ 서비스 재시작

```bash id="h7o7we"
audit shutdown
audit start

stopsrc -s syslogd
startsrc -s syslogd
```

---
<!--
## ✔ 특징 요약

* 디스크 사용 거의 없음
* 구조 단순
* 로그 유실 가능성 있음

---

# 🔥 local0 vs *.* 정리

| 설정         | 의미                  |
| ---------- | ------------------- |
| `local0.*` | audit 로그만 전송        |
| `*.*`      | 모든 로그 전송 (audit 포함) |

---

## ❗ 주의

```bash id="d9j9i2"
*.* @@10.10.10.180:514
local0.* @@10.10.10.180:514
```



---


# 🔥 최종 추천

```text id="3m4b9q"
binmode = on
streammode = on
```

* streamcmds 사용
* rsyslog는 선택적으로 구성
* trail은 cron으로 정리

---

# 🔥 한 줄 결론

👉 **“안정성을 원하면 trail 유지 + 정리, 디스크가 최우선이면 stream 전송, 실무에서는 둘을 함께 쓰는 혼합 방식이 가장 적절”**
-->
