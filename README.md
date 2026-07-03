# trex_research2

## Docker Nedir?

Uygulamaları ve ihtiyaç duydukları tüm gereksinimleri tek bir paket içinde çalıştırmaya yarayan platform. Bu pakete 
container denir.
Tanımlar:
- Image: Bir uygulamanın çalışması için gereken kodları, kütüphaneleri ve ayarları içeren şablon.
- Container: İmajların çalıştırılabilir hali.
- Ducker Hub: yazılım imajlarının paylaşıldığı ve saklandığı genel bir depo.

## DBeaver Nedir?

Veritabanlarını görsel bir arayüz üzerinden yöneten araçtır. Kod yazmadan tablolara göz atmak, veri eklemek, 
sorgu çalıştırmak, ve veritabanının yapısını görmek için kullanılır.
Veriyi kendisi saklamaz veriyi görüntülemeyi ve yönetmeyi sağlar.


## Primary Key Nedir?

Bir veritabanı tablosundaki her bir kaydı benzersiz(unique) biçimde tanımlayan sütun.
Tablodaki bir kaydı kesin şekilde ayırt etmeye yarar.

Temel Kuralları:
- Aynı Primary Key değeri iki farklı satırda bulunamaz.
- Her kaydın mutlaka bir Primary Key değeri olmalı.
- Bir tabloda yalnızca bir tane Primary Key degeri olabilir.

 ### Primary Key Oluşturma Örnegi:
 ```
CREATE TABLE Ogrenci (
    OgrenciNo INT PRIMARY KEY,  
    Ad NVARCHAR(50),
    Soyad NVARCHAR(50)
);
```


## Foreign Key Nedir?

Veritabanlarında iki tabloyu birbirine bağlamak için kullanılır. tablolar arasındaki veri tutarlılığını sağlar.
(Başka bir tablodaki primary key'i referans alır.)
Bir tabloya sadece bağlı oldugu tablodan kayda işaret eden veri eklenebilir.

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
Ancak var olmayan bir ögrenci eklenemez.
```
INSERT INTO Notlar VALUES (101, 999, N'Matematik', 70);
SQL Server bu işlemi Error 547 ile reddeder: “The INSERT statement conflicted with the FOREIGN KEY constraint”.
```


## API (Application Programming Interface)

## API Nedir? WinForms'tan Farkları Nelerdir?

API, iki yazılımın birbiriyle konuşmasını ve veri alıp göndermesini sağlayan bir arayüzdür.
Bir API sayesinde, bir programın iç kodunu bilmeden o programın verisini kullanabiliriz.

Uygulama/kullanıcı bir şey ister, API isteği alır getirir, sunucu/veritabanı isteği işler veriyi hazırlar.

WinForms ise .NET ile masaüstü uygulamaları geliştirmek için kullanılan bir arayüzdür.
Butonlar, metin kutuları, formlar gibi görsel bileşenlerle kullanıcıyla etkileşimdedir. kullanıcının bilgisayarından çalışır.

WinForm, kullanıcının gördüğü ön yüz. API ise veri sağlayan arka plan servisidir.
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
Sunucudan bir şey isteyen taraftır. İsteği gönderir sunucunun döndüğü cevabı kullanır. 
   
Client istek gönderir -> Server işler -> Server cevap döner -> Client cevabı kullanır

### WinForms bir Client midir? 

Bir WinForms bir API'ye istek gönderip cevabı kullanırsa bu onun client yapar. 
veriyi sunucudaki API'den ister. WinForms API'nin bir istemcisi olarak çalışır.


### API Neden Kullanılır?

- Bağlantı Kurar: Farklı uygulamaların birbiriyle veri alışverişi yapmasını sağlar.
- Güvenlik Sağlar: Kullanıcı doğrudan veritabanına erişemez, sadece API'nin izin verdiği işlemleri yapabilir.
- Karmaşıklığı Gizler: Sadece isteğin nasıl gönderileceği bilinir.
- Tekrar Kullanılabilir: Aynı API'yi başka sistemler ortak kullanabilir.

### API Nasıl Çalışır?

API iletişimi istek(request) ve cevap(response) mantığıyla çalışır. 
İstek gönderen tarafa istemci(client), isteği 
karşılayan tarafa sunucu(server) denir.

İstemci API'ye bir istek gönderir -> Sunucu bu isteği işler -> Sunucu bir cevap döner -> İstemci gelen 
cevabı kullanır.

### Temel Kavramlar

- Endpoint: İsteğin gönderildigi adres.
- Request: İstemcinin sunucuya gönderdiği talep.
- Response: Sunucunun geri dönderdiği sonuç.

### REST API ve HTTP Metotları 

Web’de en yaygın API türüdür. REST API’ler HTTP protokolü üzerinden çalışır ve 
yapılacak işlemi HTTP metotları ile belirtir.

| Metot | Yaptığı iş |
|-------|------------|
| GET | Veri okur, getirir. |
| POST | Yeni veri ekler. |
| PUT | Var olan veriyi günceller. |
| DELETE | Veriyi siler. |

Bu dört işlem veritabanındaki temel işlemlerdir. CRUD olarak bilinir:
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

API'ler veriyi JSON formatında gönderir. JSON, hem insanların okuyabildiği hem de proramların kolayca 
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
Kullanıcı GET /ogrenciler/1 isteği gönderir 
→ API bu isteği alır ve arka planda veritabanına SELECT * FROM Ogrenci WHERE OgrenciNo = 1 sorgusunu çalıştırır
→ Veritabanı sonucu API’ye döner 
→ API bu sonucu JSON’a çevirip kullanıcıya gösterir.


## Bir API Nasıl Test Edilir ve Kullanılır?

Bir API'nin doğru çalışıp çalışmadığını kontrol etmek için istek gönderilir ve gelen cevap incelenir.
Bunun yolları şöyledir:
- Postman: İstek gönderip cevabı görsel olarak incelemek için kullanılır.
- Swagger: API'nin kendi tarayıcısı üzerinden test edilme imkanı olan araç.
- Web Tarayıcısı: Sadece GET istekleri için tarayıcıya adresi yazarak sonuc görülür.
- Kod ile: Uygulamadan API'ye istek göndererek.

### Test Ederken İzlenen Adımlar
1. Endpoint(adres) belirlenir
2. HTTP metodu seçilir: GET, POST, PUT veya DELETE
3. Gerekliyse veri eklenir: POST için gönderilecek JSON
4. İstek gönderilir ve gelen cevap incelenir.

## Swagger Nedir Nasıl kullanılır?

Bir API'nin tüm uç noktalarını otomatik olarak listeleyen ve tarayıcı üzerinden test etmemizi sağlayan araç.
Swagger sayesinde API'nin:
- Hangi adreslere sahip oldugunu
- Her adresin hangi metodu kullandığını
- Ne tür veri beklediğini tek bir sayfada görebilir. 

### Swagger Nasıl Kullanılır?
1. API projesi çalışır.
2. Sayfada tüm endpointler(adresler) listelenir.
3. Try it out butonuna tıklanır.
4. Gerekli bilgiler girilir.
5. Execute butonuna basılır ve dönen cevap incelenir.


## Postman Nedir ve Nasıl Kullanılır?

API'lere istek gönderip gelen cevabı incelememizi sağlar. Ayrı olarak kurulan bir API test aracıdır.

### Postman Nasıl Kullanılır?
1. Postman açılır.
2. New → HTTP Request ile yeni bir istek oluşturulur.
3. İstenen HTTP metodu seçilir.(GET, POST, PUT, DELETE)
4. URL (endpoint) yazılır.
5. Gerekiyorsa Body sekmesinden gönderilecek JSON verisi eklenir.
6. Send butonuna basılır; dönen cevap görüntülenir.

### Swagger ile Postman Farkı
- Swagger API'nin içinde gelir. API'yi geliştirenin sunduğu hazır bir test sayfasıdır.
- Postman bagımsız uygulama. Her türlü API'ye istek göndermek için kullanılır.

### Bir API Nerede Çalışır?
Bir API sunucu üzerinden çalışır.
- Geliştirme ekranı: API önce geliştiricinin ekranında çalışır.
- Web sunucusu: .NET API'leri Kestrel gibi web sunucuları üzerinde çalışır. İstegi alıp API koduna iletir.
- Yayın ortamı: API bir sunucuya veya buluta yüklenir.
- Container içinde:DOcker container üzerinde çalışabilir.
   
API kullanıcının bilgisayarında değil bir sunucuda çalışır ve HTTP üzerinden gelen istekleri karşılar. Client'lar bu sunucudaki API'ye baglanarak veriyi alır.
















  

