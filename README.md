# trex_research2

## Docker Nedir?

Uygulamaları ve ihtiyaç duydukları tüm gereksinimleri tek bir paket içinde çalıştırmaya yarayan platform. Bu pakete 
container denir.
Tanımlar:
- Image: Bir uygulamanın çalışması için gereken kodları, kütüphaneleri ve ayarları içeren şablon.
- Container: İmajların çalıştırılabilir, aktif ve izole edilmiş halidir.
- Ducker Hub: Geliştiricilerin hazır yazılım imajlarını paylaştığı ve sakladığı genel bir depodur.

## DBeaver Nedir?

Veritabanlarını görsel bir arayüz üzerinden yönetmeyi sağlayan araçtır. Kod yazmadan tablolara göz atmak, veri eklemek, 
sorgu çalıştırmak, ve veritabanının yapısını görmek için kullanılır.
Veriyi kendisi saklamaz, saklandığı yere baglanıp o veriyi görüntülemeyi ve yönetmeyi sağlar.


## Primary Key Nedir?

Bir veritabanı tablosundaki her bir kaydı benzersiz(unique) biçimde tanımlayan sütun yada sütun gruplarıdır.
Tablodaki bir kaydı kesin şekilde ayırt etmeye yarar.

Temel Kuralları:
- Aynı Primary Key değeri iki farklı satırda bulunamaz.
- Her kaydın mutlaka bir Primary Key değeri olmalı.
- Bir tabloda yalnızca bir tane Primary Key degeri olabilir.

 ### Primary Key Oluşturma Örnegi:
 ```
CREATE TABLE Ogrenci (
    OgrenciNo INT PRIMARY KEY,   -- Birincil anahtar: benzersiz, boş olamaz
    Ad NVARCHAR(50),
    Soyad NVARCHAR(50)
);
```


## Foreign Key Nedir?

Veritabanlarında iki tabloyu birbirine bağlamak ve tablolar arasındaki veri tutarlılığını sağlamak için kullanılan sütun 
veya sütun grubudur.(Başka bir tablodaki primary key'i referans alır.)
Bir tabloya ancak bağlı oldugu tablodan bir kayda işaret eden veri eklenebilir.

### Foreign Key Oluşturma Örneği:

- sütunun yanında tanımlama:
```
CREATE TABLE Notlar (
    NotID INT PRIMARY KEY,
    OgrenciNo INT FOREIGN KEY REFERENCES Ogrenci(OgrenciNo),
    Puan INT
);
```
Notlar tablosundaki OgrenciNo, Ogrenci tablosunun primary key'ine baglanır.

- Aynı satırda isim vererek:
 ```
CREATE TABLE Notlar (
    NotID INT PRIMARY KEY,
    OgrenciNo INT,
    Ders NVARCHAR(50),
    Puan INT,
    CONSTRAINT FK_Notlar_Ogrenci FOREIGN KEY (OgrenciNo)
        REFERENCES Ogrenci(OgrenciNo)
);
```
CONSTRAINT FK_Notlar_Ogrenci ile foreign key'e kendi ismi verilir. İlerde silmek değiştirmek kolaylaşır.

- Var olan tabloya sonradan eklenerek:
```
ALTER TABLE Notlar
ADD CONSTRAINT FK_Notlar_Ogrenci FOREIGN KEY (OgrenciNo)
    REFERENCES Ogrenci(OgrenciNo);
```
Tablo zaten oluşturulmuşsa ilişki böyle kurulur.

- ON DELETE / ON UPDATE davranışları:
```
CREATE TABLE Notlar (
    NotID INT PRIMARY KEY,
    OgrenciNo INT,
    Puan INT,
    CONSTRAINT FK_Notlar_Ogrenci FOREIGN KEY (OgrenciNo)
        REFERENCES Ogrenci(OgrenciNo)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```
1. ON DELETE: Bir ögrenci silinirse ona ait tüm kayıtlar silinir.
2. ON UPDATE: Bir öğrencinin numarası değişirse notlardaki numara da otomatik güncellenir.
3. NO ACTİON: Varsayılan engeller,
4. SET NULL: Bağlı degeri boşaltır.
 
- Foreign Key'i silme:
```
ALTER TABLE Notlar
DROP CONSTRAINT FK_Notlar_Ogrenci;
```

- Veri bütünlüğünün Kanıtı:
Geçerli kayıt eklendiğinde sorun çıkmaz.
```
INSERT INTO Ogrenci VALUES (1, N'Nazlı', N'Demir');
INSERT INTO Notlar  VALUES (100, 1, N'Veritabanı', 90);
```
Ancak var olmayan bir ögrenci eklemeye çalışırsak,
```
INSERT INTO Notlar VALUES (101, 999, N'Matematik', 70);
SQL Server bu işlemi Error 547 ile reddeder: “The INSERT statement conflicted with the FOREIGN KEY constraint”.
```


## API (Application Programming Interface)

## API Nedir? WinForms'tan Farkları Nelerdir?

API, iki yazılımın birbiriyle konuşmasını, veri alıp göndermesini sağlayan bir arayüzdür.
Bir API sayesinde, bir programın iç kodunu bilmeden onun sunduğu belirli istek noktaları üzerinden o programın verisini 
veya işlevini kullanabiliriz.

Uygulama/kullanıcı bir şey ister, API isteği alır getirir, sunucu/veritabanı isteği işler veriyi hazırlar.

WinForms ise .NET ile masaüstü uygulamaları geliştirmek için kullanılan bir arayüzdür.
Butonlar, metin kutuları, formlar gibi görsel bileşenlerle kullanıcının dogrudan etkileşime girdigi 
programdır.Kullanıcının bilgisayarında çalışır.

WinForm, kullanıcının gördüğü ve tıkladığı ön yüz. API ise ekranı olmayan, veri sağlayan arka plan servisidir.
Birbirini tamamlayan katmanlardır.Bir WinForms uygulaması, API'ye istek göndererek veri çekebilir.

| Özellik | API | WinForms | 
|---------|-----|----------|
| Amaç | Veri/İşlev sunmak | Kullanıcıya arayüz sunmak |
| Görsel Arayüz | Yok | Var |
| Çalıştığı yer | Sunucu | Kullanıcının bilgisayarı |
| İletişim | HTTP + JSON | Ekran üzerinden kullanıcıyla |
| Kim Kullanır | Diğer Uygulamalar | Son kullanıcı |
| Rol | Backend | Frontend/Client | 

## Client ve Server Nedir? WinForms Bir Client Midir?

### Server(sunucu)
İstekleri karşılayan, veriyi tutan ve işleyen taraftır. Gelen işlemlere cevap verir.
### Client(istemci)
Sunucudan bir şey isteyen taraftır. İsteği gönderir sunucunun döndüğü cevabı kullanır. Web tarayıcısı, mobil 
uygulama,veya masaüstü bir program client olabilir.   

Client istek gönderir -> Server işler -> Server cevap döner -> Client cevabı kullanır

### WinForms bir Client midir? 
Bir WinForms uygulaması bir API'ye istek gönderip cevabı kullandığında client rolündedir. Kendi içinde veriyi sunmaz 
veriyi sunucudaki API'den ister. Bu yüzden WinForms API'nin bir istemcisi olarak çalışır.


### API Neden Kullanılır?

- Bağlantı Kurar: Farklı uygulamaların birbiriyle veri alışverişi yapmasını sağlar.
- Güvenlik Sağlar: Kullanıcı doğrudan veritabanına erişmez, sadece API'nin izin verdiği işlemleri yapabilir.
- Karmaşıklığı Gizler: Arka planın  nasıl çalıştığını bilmeye gerek kalmaz sadece isteğin nasıl gönderileceği bilinir.
- Tekrar Kullanılabilir: Aynı API'yi web sitesi, mobil uygulama ve başka sistemler ortak kullanabilir.

### API Nasıl Çalışır?

API iletişimi istek(request) ve cevap(response) mantığıyla çalışır. İstek gönderen tarafa istemci(client), isteği 
karşılayan tarafa sunucu(server) denir.

Akış şöyledir: İstemci API'ye bir istek gönderir -> Sunucu bu isteği işler -> Sunucu bir cevap döner -> İstemci gelen 
cevabı kullanır.

### Temel Kavramlar

- Endpoint: İsteğin gönderildigi adres.
- Request: İstemcinin sunucuya gönderdiği talep.
- Response: Sunucunun geri dönderdiği sonuç.

### REST API ve HTTP Metotları 

Web’de en yaygın API türü REST API’dir. REST API’ler, internetin de kullandığı HTTP protokolü üzerinden çalışır ve 
yapılacak işlemi HTTP metotları ile belirtir.

| Metot | Yaptığı iş |
|-------|------------|
| GET | Veri okur, getirir. |
| POST | Yeni veri ekler. |
| PUT | Var olan veriyi günceller. |
| DELETE | Veriyi siler. |

Bu dört işlem veritabanındaki temel işlemlerle birebir örtüşür ve CRUD olarak bilinir:
- Create (oluştur)
- Read (oku)
- Update (güncelle)
- Delete (sil)

### HTTP Durum Kodları (Status Codes)

| Kod | Anlamı |
|-----|--------|
| 200 OK | İstek başarılı, veri döndü. |
| 201 Created | Yeni kayıt başarıyla oluşturuldu. | 
| 400 Bad Request | İstek hatalı, eksik gönderildi. |
| 401 Unauthorized | Yetki yok, giriş/kimlik doğrulama istiyor. | 
| 404 Not Found | İstenen kayıt veya adres bulunamadı. |
| 500 Server Error | Sunucu tarafında bir hata oluştu. | 


### Veri Formatı:JSON 

API'ler veriyi genellikle JSON formatında gönderir. JSON, hem insanların okuyabildiği hem de proramların kolayca 
işleyebildiği anahtar deger çiftlerinden oluşan bir metin biçimidr.
```
{
    "ogrenciNo": 1,
    "ad": "Ceyda",
    "soyad": "Yıldız",
    "notlar": [
        { "ders": "Veritabanı", "puan": 90 }
    ]
}
```

### API ile Veritabanı İlişkisi

API, veritabanı ve kullanıcı arasındaki köprüdür. Kullanıcı doğrudan veritabanına bağlanmaz arada API bulunur.
Kullanıcı / Uygulama -> API -> Veritabanı

Örnek akış:
Kullanıcı GET /ogrenciler/1 isteği gönderir → API bu isteği alır ve arka planda veritabanına SELECT * FROM Ogrenci WHERE 
OgrenciNo = 1 sorgusunu çalıştırır → Veritabanı sonucu API’ye döner → API bu sonucu JSON’a çevirip kullanıcıya gösterir.


## Bir API Nasıl Test Edilir ve Kullanılır?

Bir API'nin doğru çalışıp çalışmadığını kontrol etmek için ona istek gönderilir ve dönen cevap incelenir.
Bunun için bir kaç yol var:
- Postman: İstek gönderip cevabı görsel olarak incelemek için kullanılan test aracı.
- Swagger: API'nin kendi içinde gelen tarayıcı üzerinden test etme imkanı sunan otomatik dokümantasyon aracıdır.
- Web Tarayıcısı: Sadece GET istekleri için tarayıcıya adresi yazarak sonucu görmek mümkün.
- Kod ile: Bir uygulamanın içinden API'ye istek göndererek.

### Test Ederken İzlenen Adımlar
1. Endpoint belirlenir
2. HTTP metodu seçilir: GET, POST, PUT veya DELETE
3. Gerekliyse veri eklenir: POST için gönderilecek JSON
4. İstek gönderilir ve dönen cevap ile durum kodu incelenir.

## Swagger Nedir Nasıl kullanılır?

Bir API'nin sahip oluğu tüm uç noktaları otomatik olarak listeleyen ve tarayıcı üzerinden test etmeye sağlayan test aracıdır.
Swagger sayesinde API'nin:
- Hangi adreslere sahip oldugunu
- Her adresin hangi metodu kullandığını
- Ne tür veri beklediğini
tek bir sayfada görebilir. 

### Swagger Nasıl Kullanılır?
1. API projesi çalıştırılır.
2. Açılan sayfada tüm endpointler listelenir.
3. Try it out butonuna tıklanır.
4. Gerekli bilgiler girilir(parametre veya JSON body)
5. Execute butonuna basılır ve dönen cevap ile durumm kodu ekranda görünür.


## Postman Nedir ve Nasıl Kullanılır?

API'lere istek gönderip dönen cevabı incelemeyi sağlayan ayrı olarak kurulan bir API test aracıdır.

### Postman Nasıl Kullanılır?
1. Postman indirilir ve kurulur ardından açılır.
2. New → HTTP Request ile yeni bir istek oluşturulur.
3. İstenen HTTP metodu seçilir: GET, POST, PUT, DELETE.
4. URL (endpoint) yazılır
5. Gerekiyorsa: Body sekmesinden gönderilecek JSON verisi, Headers sekmesinden başlık bilgileri eklenir.
6. Send butonuna basılır; dönen cevap ve durum kodu alt bölümde görüntülenir.

### Swagger ile Postman Farkı
- Swagger API'nin içinde gelir. API'yi geliştirenin sunduğu hazır bir test sayfasıdır.
- Postman ayrı kurulan bagımsız uygulama. Her türlü API'ye istek göndermek için kullanılır.

### Bir API Nerede Çalışır?
Bir API sunucu üzerinden çalışır.
- Geliştirme ekranı: API önce geliştiricinin kendi ekranında çalışır.
- Web sunucusu: .NET API'leri Kestrel gibi web sunucuları üzerinde çalışır. İstegi alıp API koduna iletir.
- Yayın ortamı: API bir sunucuya veya buluta yüklenir.
- Container içinde:DOcker container üzerinde çalışabilir.
   
API kullanıcının bilgisayarında değil bir sunucuda çalışır ve HTTP üzerinden gelen istekleri karşılar. Client'lar bu sunucudaki API'ye baglanarak veriyi alır.
















  

