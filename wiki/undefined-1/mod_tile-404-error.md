# Mod\_tile 404 error

중요한 mapnik 이 없으면 http://localhost/0/0/0.png 로 접속 시 404가 뜸



### **📌 1️⃣ 원인 분석**

* `mod_tile`은 **Mapnik**을 사용하여 OpenStreetMap 데이터를 렌더링하는데,\
  **현재 컨테이너에는 Python용 Mapnik 바인딩(`python3-mapnik`)이 설치되지 않은 상태.**
* `pip install mapnik`을 실행하려고 했지만, **Python에서 직접 설치할 수 있는 패키지가 아니라 `apt`를 사용해야 함.**
* `mapnik`이 없으면 타일을 생성할 수 없어서 **404 오류가 계속 발생할 가능성이 높음.**

***

### **✅ 2️⃣ 해결 방법**

#### **✔️ 방법 1: `mod_tile` 컨테이너 내부에서 `python3-mapnik` 설치 (임시 해결)**

컨테이너 내부에서 **수동으로 `python3-mapnik`을 설치**해서 문제를 해결할 수 있어.

1️⃣ **`mod_tile` 컨테이너 내부로 접속**

```bash
docker exec -it mod_tile bash
```

2️⃣ **Mapnik 및 Python3 지원 패키지 설치**

```bash
apt update
apt install -y python3-mapnik mapnik-utils
```

3️⃣ **Python에서 다시 확인**

```bash
python3
```

```python
pimport mapnik
print(mapnik.__version__)
```

✅ **정상적으로 실행되면 Mapnik이 제대로 설치된 것.**

🚀 **이제 다시 타일을 요청해보고 404 오류가 해결되는지 확인!**

```
http://localhost/osm/0/0/0.png
```
