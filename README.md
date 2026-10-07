# Лаборатори №5: API систем тест — Postman ба Newman

Ч. Мөнхбаяр — B242270045  
Хичээл: F.CSA313 — Программ хангамжийн чанарын баталгаа ба тест

## Зорилго

Багшийн өгсөн локал REST API-г Postman collection-оор системийн түвшинд туршиж, Newman-аар командын мөрөөс автоматжуулах. Лекцийн 5 алхам болох тестийн зорилго, оролтын сонголт, төлөөлөх утга, хүлээгдэж буй үр дүн, автомат oracle-г нэг бүрчлэн ашигласан.

## Орчин ба хэрэгсэл

- Windows 11
- Node.js `v24.11.1`
- Newman `6.2.2`
- Postman Collection Schema `v2.1.0`
- Локал API: `http://localhost:3000`

## Файлын бүтэц

- `server.js` — багшийн өгсөн локал REST API
- `lab05-collection.json` — бие даасан 10 локал тест болон 1 нийтийн API тест
- `lab05-collection-fail.json` — зориуд буруу status oracle-тай collection
- `results/newman-pass.txt` — амжилттай ажиллуулалтын бүтэн гаралт
- `results/newman-fail.txt` — oracle алдааг барьсан ажиллуулалтын бүтэн гаралт
- `results/newman-down.txt` — сервер унтарсан үеийн бүтэн гаралт

## Ажиллуулах

Эхний terminal-д API-г асаана.

```powershell
node server.js
```

Дараагийн terminal-д collection-уудыг ажиллуулна.

```powershell
newman run lab05-collection.json
newman run lab05-collection-fail.json
```

Сервер унтарсан үеийн шалгалтыг хийхдээ `node server.js` процессыг зогсоогоод үндсэн collection-ыг дахин ажиллуулсан.

## Оролтын сонголт ба төлөөлөх утга

| Параметр | Сонголт | Төлөөлөх утга | Сонгосон шалтгаан |
|---|---|---|---|
| `studentID` | Идэвхтэй, байгаа | `B250001` | Амжилттай үндсэн урсгал |
| `studentID` | Идэвхгүй, байгаа | `B250003` | `ERROR_INACTIVE_STUDENT` салбар |
| `studentID` | Байхгүй | `B_MISSING_02` | `ERROR_NO_STUDENT` салбар |
| `coursesTaken` | Бүх урьдачийг үзсэн | `["CS201"]` | Бүртгэл зөвшөөрөгдөх нөхцөл |
| `coursesTaken` | Юу ч үзээгүй | `[]` | Бүх урьдач дутуу байх нөхцөл |
| `coursesTaken` | Зарим урьдачийг үзсэн | `["CS201"]` ба `["CS201", "CS202"]` шаардлагатай | Хэсэгчилсэн дутагдлыг ялгах |
| `courseID` | Байгаа | `CS501`–`CS508` | Setup хийсэн хичээлүүд |
| `courseID` | Байхгүй | `CS_MISSING_04` | `ERROR_NO_COURSE` салбар |
| `prerequisites` | Бүгд хангагдсан | `["CS201"]` | Амжилттай бүртгэл |
| `prerequisites` | Огт байхгүй | `[]` | Хоосон массивын хязгаар |
| `prerequisites` | Зарим нь хангагдаагүй | `["CS201", "CS202"]` | `missing` массивын утгыг шалгах |
| Request body | `courseID` дутуу | `{ "studentID": "B250009" }` | Заавал байх талбарын хязгаар |
| Request body | Буруу JSON | `{"studentID":"B250010",` | Parser алдааны хязгаар |

## Тестийн тодорхойлолт

| № | Тохиолдол | Setup | Хүлээгдэж буй status | Хүлээгдэж буй oracle |
|---:|---|---|---:|---|
| 1 | Идэвхтэй оюутан бүх урьдачийг хангасан | Student + course | `201` | `result = OK`, `registrationID` нь тоо |
| 2 | Оюутан байхгүй | Course | `200` | `result = ERROR_NO_STUDENT` |
| 3 | Оюутан идэвхгүй | Inactive student + course | `200` | `result = ERROR_INACTIVE_STUDENT` |
| 4 | Хичээл байхгүй | Student | `200` | `result = ERROR_NO_COURSE` |
| 5 | Урьдач нөхцөл огт хангаагүй | Student + course | `200` | `ERROR_PREREQUISITES`, `missing = ["CS201"]` |
| 6 | Урьдач нөхцөлийн заримыг хангаагүй | Student + course | `200` | `ERROR_PREREQUISITES`, `missing = ["CS202"]` |
| 7 | Оюутан ба хичээл хоёулаа байхгүй | Setup хийхгүй | `200` | `ERROR_NO_STUDENT` түрүүлнэ |
| 8 | Урьдач нөхцөлгүй хичээл, хоосон `coursesTaken` | Student + course | `201` | `result = OK`, `registrationID` нь тоо |
| 9 | `courseID` талбар дутуу | Student | `400` | `result = ERROR_BAD_REQUEST` |
| 10 | Request body буруу JSON | Setup хийхгүй | `400` | `result = ERROR_BAD_JSON` |
| 11 | JSONPlaceholder хэрэглэгчдийн жагсаалт | Setup хийхгүй | `200` | Массив, эхний нэр `Leanne Graham` |

Тест бүр өөр ID ашиглаж, шаардлагатай өгөгдлөө өөрийн folder доторх `PUT` хүсэлтүүдээр бэлтгэдэг. Иймээс ажиллуулах дараалалд найдахгүй бөгөөд collection-ыг давтан ажиллуулахад тогтвортой. Амжилттай хариуны `registrationID` өсдөг учраас яг утгыг нь биш зөвхөн тоо эсэхийг шалгасан.

## Newman үр дүн

| Ажиллуулалт | Requests | Request failed | Assertions | Assertion failed | Exit code | Нотолгоо |
|---|---:|---:|---:|---:|---:|---|
| Үндсэн collection | 24 | 0 | 27 | 0 | `0` | [newman-pass.txt](results/newman-pass.txt) |
| Буруу oracle | 24 | 0 | 27 | 1 | `1` | [newman-fail.txt](results/newman-fail.txt) |
| Сервер унтарсан | 24 | 23 | 27 | 24 | `1` | [newman-down.txt](results/newman-down.txt) |

`lab05-collection-fail.json`-д эхний амжилттай хүсэлтийн бодит `201` status-ыг зориуд `200` гэж хүлээлгэсэн тул энэ нь oracle mismatch юм. Сервер унтарсан ажиллуулалтад локал 23 хүсэлт `ECONNREFUSED 127.0.0.1:3000` болсон тул энэ нь assertion-ийн буруу хүлээлт биш, API интерфейс хүрэхгүй байсны алдаа юм. Хоёр тохиолдолд Newman `exit 1` буцаасан учраас CI quality gate алдааг барих боломжтой.

## Нийтийн API-тай харьцуулалт

JSONPlaceholder-ийн GET тест setup шаардахгүй тул бичихэд хялбар байсан ч локал API шиг бүрэн хянагдсан өгөгдөлтэй биш бөгөөд гадаад сүлжээний бэлэн байдлаас хамаарна.

## Дүгнэлт

Хамгийн их бодол шаардсан хэсэг нь оролтын сонголтуудыг давхардуулахгүй ангилж, бодит төлөөлөх утгатай холбох байв. Байхгүй оюутан ба байхгүй хичээлийг зэрэг өгөхөд сервер эхлээд `ERROR_NO_STUDENT` буцаадгийг тестээр тогтоосон. Идэвхгүй оюутны ард хичээлийн болон урьдачийн алдаа байж болох ч өмнөх шалгалт түрүүлж буцдаг тул бүх алдааг нэг хүсэлтээр тусгаарлан шалгах боломжгүй байлаа. Иймээс тест бүрт өөр ID болон өөрийн `PUT` setup ашиглаж харилцан хамаарлыг арилгасан. `registrationID` өсдөг тул яг утгыг биш тоо эсэхийг шалгасан. PASS ажиллуулалтаар 27 assertion бүгд амжилттай болсон. Зориуд буруу status oracle нэг assertion-ыг унагаж Newman `exit 1` өгсөн нь quality gate алдааг барьж байгааг харуулсан. Сервер унтарсан үед `ECONNREFUSED` гарсан нь oracle зөрөөгүй, API интерфейс хүрэхгүй байсныг харуулсан. JSONPlaceholder setup шаардахгүй хялбар байсан ч гадаад сүлжээнээс хамаарсан. Туршилтаар санаандгүй согог илрээгүй, харин шалгалтын дараалал болон хязгаарын зан төлөвийг баталгаажуулсан.

