## Лабортори 6

Оюутаны нэр: Ё.Болормаа
Оюутаны код: B222270050
 
## Хэсэг А: Category-Partition 

А1. Сонголт ба төлөөлөх утгууд

| Параметр | Сонголт|  Төлөөлөх утга |
| --- | --- | --- |
| from | Хүчинтэй (Valid) | байгаа, царцаагүй данс,ID = A |  
|      | Царцаасан (Frozen) | байгаа боловч царцаасан данс ID=F |
|      | Байхгүй (Missing) | системд байхгүй ID = 999|
| to | Хүчинтэй (Valid) | байгаа данс ID = B |
|    | Байхгүй (Missing) | системд байхгүй ID = 999 |
|    | from- той ижил (SameAsFrom) | from -той ижил ID = A |
|amount | Хэвийн (Normal) | 100 үлдэгдэл, лимитэд багтана|
|       | Тэг/сөрөг(Zero) | 0, мөн -5 |
|       | Үлдэгдлээс их (OverBalance) | үлдэгдэл +1, |
|       | Лимитээс их(OverLimit) | өдрийн лимитийн үлдэгдэл +1|
|       | Үлдэгдэлтэй тэнцүү (ExactBalance) | үлдэгдэлтэй тэнцүү |
|currency | Ижил (Same) | дансны валюттай ижил MNT |
|         | Өөр (Different) | дансны валютаас өөр USD |


Далд орчин: Оролтод шууд хамаарахгүй боловч үр дүнд нөлөөлөх хүчин зүйлс:
Дансны үлдэгдэл хэд вэ? , Өдрийн лимит хэр хэд вэ? , Ханш хэд вэ? зэрэг. Тест бүрийн Setup -д эдгээрийг заана 

А2. Хязгаарлалтууд
| Утга | Тэмдэглэгээ | Үндэслэл |
| --- | --- | --- | 
| from = Valid | [property fromValid] | to-ийн SameAsFrom утга энэ шинжээс хамаарна |
| from = Frozen | [ERROR] | ERROR_FROZEN  буцаана |
| from = Missing | [ERROR] | ERROR_NO_FROM буцаана|
| to = Missing | [ERROR] | ERROR_NO_TO буцаана|
| to = SameAsFrom |[ERROR] [IF from = Vaild] | ERROR_SAME_ACCOUNT буцаана; from байхгүй бол "from-той ижил" гэсэн ойлголт утгагүй тул зөвхөн from хүчинтэй үед хэрэглэнэ.|
| amount = Zero | [ERROR] | ERROR_BAD_AMOUNT (amount≤ 0) буцаана |
| amount = OverBalance | [ERROR] | ERROR_INSUFFICIENT буцаана |
| amount = OverLimit | [ERROR] | ERROR_LIMIT буцаана |
| amount = ExactBalance | [SINGLE] | Үлдэгдэлтэй яг тэнцүү дүн нь хил хязгаарын нэг онцгой тохиолдол бусад параметртэй хослуулах шаардлаггүй |
| currency =  Same,Different |  | Хөрвүүлсний дараах дүнгээр үлдэгдэл, лимитийн шалгах дүрмийг баталгаажуулах хэрэгтэй | 

"N -> M" тооцоо 
| Шат | Юу хасав | Хослолын тоо | 
| --- | --- | --- | 
| Бүрэн олонлог | from 3 * to 3 * amount 5 * currency 2 | 90 | 
|[if fromValid хэрэглэсний дараа| from = Missing үед to = SameAsFrom боломжгүй: 1 × 1 × 5 × 2 = 10 хослол хасагдана| 80 |
|[ERROR], [SINGLE] хэрэглэсний дараа | хүчинтэй хослол 2 (Normal × currency 2 утга) + [ERROR] утга тус бүр 1 удаа 7 + [SINGLE] 1 | 10 |

PICT-ийн моделд ExactBalance-ыг оруулаагүй (Amount-д 4 утга) тул тэнд бүрэн олонлог 3 × 3 × 4 × 2 = 72, хязгаарлалтын дараа 40 (pict transfer.model /o:4). 

Олон алдааны давуу эрэмбэ: from → to → amount (ERROR_NO_FROM, ERROR_FROZEN, ERROR_NO_TO, ERROR_SAME_ACCOUNT, ERROR_BAD_AMOUNT, ERROR_INSUFFICIENT, ERROR_LIMIT), учир нь шалгалт "хэн илгээж байна → хэн авч байна → хэдийг" гэсэн логик дарааллаар явдаг бөгөөд дансны нөхцөл баталгаажихгүй бол дүнг шалгах утгагүй.

A3. Спецификацийн хүснэгт 
| From | To | Amount | Currency | Хүлээгдэх үр дүн |
| Valid | Valid | Normal | Same | 201 OK (happy path) |
| Valid | Valid | Normal | Different | 201 OK |
| Frozen | Valid | Normal | Same | ERROR_FROZEN |
| Missing | Valid | Normal | Same | ERROR_NO_FROM |
| Valid | Missing | Normal | Same | ERROR_NO_TO |
| Valid | SameAsFrom | Normal | Same | ERROR_SAME_ACCOUNT |
| Valid | Valid | Zero | Same | ERROR_BAD_AMOUNT |
| Valid | Valid | OverBalance | Same | ERROR_INSUFFICIENT |
| Valid | Valid | OverLimit | Same | ERROR_LIMIT |
| Valid | Valid | ExactBalance | Same | 201 OK ([SINGLE]) |
| Frozen | Missing | Zero | Same | ERROR_FROZEN  (давхар нөхцөл: from эхэлж шалгагдана) |
| Valid | SameAsFrom | Zero | Same | ERROR_SAME_ACCOUNT (давхар нөхцөл: to нь amount-аас өмнө) |
| Valid | Valid | OverBalance | Different | ERROR_INSUFFICIENT (хөрвүүлсний дараах дүнгээр шалгана) |

Боломжгүй хослол (хүснэгтэд оруулаагүй): Missing | SameAsFrom | * | * — from байхгүй үед "to нь from-той ижил" гэсэн нөхцөл утгагүй тул [if fromValid] хязгаарлалтаар устсан (10 хослол, 90 → 80).

## Хэсэг B PICT 

B1: Браузерын жишээ 
| Хувилбар | Мөр | 
| --- | --- |
| 2 way (pairwise) | 10 | 
| 3 way | 20 | 

Бүрэн олонлог (3*3*2*2*2*2)

2 way: Pict 10 мөр өгсөн. PICT greedy алгоритм ашигладаг: мөр бүрт одоогоор хамрагдаагүй хамгийн олон хосыг хамарсан мөрийг сонгодог боловч өмнөх сонголтуудаа хойно нь өөрчилдөггүй, тиймээс нийт хамгийн цөөн (оновчтой) тоог олох баталгаа байхгүй. Гараар хийхдээ хамралтыг бүхэлд нь харж цөөн мөр олж болно. 
3-way: 20 мөр буюу бүрэн олонлог 144-ийн 14 орчим хувь (ойролцоогоор 7 дахин цөөн).

B2: Шилжүүлгийн модель 

Мөрийн тооны харьцуулалт 
| Хувилбар | Мөр | Happy path | Алдааны тоо ->мөрийн тоо | 
| --- | --- | --- | --- | 
| transfer-nc | 13 | 1 | 0->1, 1->1, 2->8, 3->3 |
| transfer  | 13 | 1 | 0->1, 1->4, 2->7, 3->1| 
| transfer-neg(~) | 16 | 2 | 0->2, 1->14 | 

Бодит кеис 
Тэмдэглэгээ: Данс A,B дансны валют MNT; ханш 1 USD = 3500 MNT.
Тест 1 — happy path (results/transfer.txt: Valid Valid Normal Different)
 - Setup: A идэвхтэй, үлдэгдэл 100000 MNT; B идэвхтэй, 0 MNT; өдрийн лимитийн үлдэгдэл 1000000 MNT.
 - Оролт: {"from":"A","to":"B","amount":10,"currency":"USD"} (10 USD = 35000 MNT).
 - Oracle: статус 201, {"result":"OK"}.
 
Тест 2 — transfer-neg нэг алдаатай мөр (results/transfer-neg.txt:~Frozen Valid Normal Same)
 - Setup: A царцаагдсан, үлдэгдэл 100000 MNT; B идэвхтэй.
 - Оролт: {"from":"A","to":"B","amount":100,"currency":"MNT"}.
 - Oracle: статус 200, {"result":"ERROR_FROZEN"}.
 
 
 
