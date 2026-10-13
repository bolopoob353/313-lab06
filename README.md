## Лабортори 6

Оюутаны нэр: Ё.Болормаа
Оюутаны код: B222270050
 
## Хэсэг А: Category-Partition 

А1. Сонголт ба төлөөлөх утгууд

| Параметр | Сонголт|  Төлөөлөх утга |
| --- | --- | --- |
| from | Хүчинтэй (Valid) | байгаа, царцаагүй данс,ID = A |  
| from | Царцаасан (Frozen) | байгаа боловч царцаасан данс ID=F |
| from | Байхгүй (Missing) | системд байхгүй ID = 999|
| to | Хүчинтэй (Valid) | байгаа данс ID = B |
| to | Байхгүй (Missing) | системд байхгүй ID = 999 |
| to | from- той ижил (SameAsFrom) | from -той ижил ID = A |
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

