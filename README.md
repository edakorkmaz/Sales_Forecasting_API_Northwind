# Sales_Forecasting_API_Northwind
 FastAPI, PostgreSQL ve scikit-learn kullanılarak geliştirilen Northwind verisiyle satış tahmini API projesi.

GELECEĞİ YAZAN KADINLAR SATIŞ TAHMİNİ API PROJESİ (NORTHWİND)

Bu proje, Northwind veritabanındaki sipariş verilerini kullanarak bir Makine Öğrenmesi modeli geliştirmeyi ve bu modeli FAST API aracılığıyla dış sistemlere sunmayı amaçlamaktadır. Tahmin modeli, ürün bazlı geçmiş satış verilerini analiz ederek gelecekteki satış miktarını öngörür.
________________________________________
📌 1.PROJE AMACI

Northwind veritabanındaki sipariş verileriyle ürün bazlı satış tahminleri yapan bir makine öğrenmesi modeli eğitilmiştir. Ardından, bu model FastAPI framework'ü üzerinden bir kullanıma sunulmuştur. Böylece dış sistemler, belirli bir ürün için tahmin talebinde bulunarak satış adedi veya satış tutarı gibi değerlerin öngörüsünü alabilir.
________________________________________
📌 2. GÖREV LİSTESİ

 A. Veri Tabanı ve Veri İşleme
 
1. **Northwind veritabanı kurulumu** ve bağlantı testi  
2. Tablo inceleme (Orders, Order_Details, Products, Customers, Categories)  
3. Pandas ile verilerin çekilmesi ve birleştirilmesi  
4. Aylık veya ürün bazlı satış özet verisi oluşturma  
5. Eksik veri kontrolü ve temizliği  
6. Özellik mühendisliği (ay bilgisi, ürün fiyatı, müşteri segmentasyonu vb.)

B. Makine Öğrenmesi Modeli

1. Hedef değişken: (örnek: *ürün bazlı satış miktarı*)  
2. Eğitim ve test verisinin hazırlanması (`train_test_split`)  
3. Model seçimi (örnek: DecisionTreeRegressor)  
4. Modelin eğitimi ve testi  
5. Model başarım metriklerinin raporlanması (R², RMSE vs.)  
6. Eğitilmiş modelin `.pkl` olarak kaydedilmesi


C. API Geliştirme

1. **FastAPI** ile temel yapı kurulumu  
2. Endpoint’ler:
   | Endpoint         | Method | Açıklama                                |
   |------------------|-------------|-----------------------------------------|
   | `/products’		| GET	| Ürün listesini döner 		|
   | `/predict’			| POST	| Tahmin yapılmasını sağlar		|
   | `/retrain’			| POST	| Modeli yeniden eğitir (opsiyonel)	|
   | `/sales_summary’	| GET	| Satış özet verisini döner		|
________________________________________

🧠3. KULLANILAN TEKNOLOJİLER

•	Python 3.x: Proje dili 

•	FastAPI & Pydantic: API geliştirme ve veri doğrulama 

•	pandas, numpy: Veri işleme kütüphaneleri 

•	scikit-learn (DecisionTreeRegressor, train_test_split, metrics): Makine Öğrenmesi 

•	joblib: Model serileştirme (kaydetme/yükleme) 

•	dataclasses, typing: Python standard library (tip güvenliği ve data class'lar) 

•	datetime, random: Python standard library (zaman ve rastgelelik) 

•	PostgreSQL: Veri tabanı  
________________________________________

🧠4. VERİ TABANI BAĞLANTISI VE TEMEL VERİ İŞLEME

Projede, **psycopg2** sürücüsüyle **PostgreSQL** veritabanına bağlanmak için **SQLAlchemy** kütüphanesi kullanılmıştır. Aşağıdaki kod örneği, Northwind veritabanındaki tablolardan veri çekilmesini ve temel veri hazırlama adımlarını gösterir:
Yapılan Adımlar:
1.	Tabloları Okuma: orders, order_details, products, categories, customers tabloları ayrı ayrı çekildi ve head() ile gözlemlendi.
2.	Birleştirilmiş Sorgu: JOIN işlemleriyle orders, order_details ve products birleştirildi, veri df adlı DataFrame’de tutuldu.
3.	Zaman Özellikleri: order_date sütunu datetime türüne çevrilerek month ve year özellikleri eklendi.
4.	Satış Özeti: sales_summary adında bir grupby oluşturularak ürün bazında aylık ve yıllık toplam satış (quantity) hesaplandı, CSV’ye kaydedildi.
5.	Eksik Veri Kontrolü: isnull().sum() fonksiyonuyla tablolar tarandı, dropna() ile boş satırlar kaldırıldı.
6.	Yeni Özellik: TotalRevenue = quantity * unit_price eklenerek veri zenginleştirildi.
7.	Bu aşamada elde edilen veya df DataFrame’i, sonraki aşamalarda makine öğrenmesi modeli eğitimi için kullanılmıştır.

 ![image](https://github.com/user-attachments/assets/d0c9d701-3142-4315-b6b1-03ab34fe7b17)


Northwind verilerinin veri tabanından çekilmesinin ardından, ham verinin genel yapısını anlamak ve temel dağılımını görmek için bir dizi keşifsel veri analizi (Exploratory Data Analysis, EDA) yaptık. Bu aşamada:
- En çok satılan ürünleri tespit ederek, ürün satışlarına dair hızlı bir fikir edindik,
- Aylık satış verilerine bakarak, zaman içerisindeki dalgalanmaları yakaladık,
- Bazı özet istatistikler ve basit görselleştirmelerle verinin temel özelliklerini ortaya koyduk. 

A.	En Çok Satılan 10 Ürün Grafiği
Bu grafikte, veri tabanında toplam satış miktarına göre en çok satılan 10 ürün listelenmiştir. Yatay eksende “Toplam Satış Miktarı” (örneğin adet cinsinden), dikey eksende ürün isimleri görünmektedir.
 ![image](https://github.com/user-attachments/assets/0f19e4b7-2257-409d-b9d1-1f2cc8907a52)
B.	 Zaman İçerisinde Aylık Satış Miktarı
Aşağıdaki çizgi grafiğinde, 1996-1998 yılları arasında her ayın **toplam satış miktarı (adet cinsinden) gösterilmektedir. Veriler Northwind veritabanından çekilerek `month` ve `year` kolonlarına göre gruplandırılmıştır.
 ![image](https://github.com/user-attachments/assets/c5ea6cc2-b702-457c-a41a-222d352b1f2f)
______________________________
🧠5. MODEL EĞİTME VE YENİDEN EĞİTME

Bu projede iki farklı hedef değişken üzerinde çalıştık:
•	Hedef Değişken 1: quantity → Bir regresyon problemi
•	Hedef Değişken 2: Customer_Type → Bir sınıflandırma problemi

1) Regresyon (Hedef: quantity)
•	Amaç: Sipariş başına kaç adet ürün satılacağını tahmin etmek.
•	Özellikler: product_id, unit_price, month, year, TotalRevenue
•	Model: DecisionTreeRegressor

2) Sınıflandırma (Hedef: Customer_Type, Sales Category)
•	Amaç: Müşterinin “Yeni” (New Customer) mi, yoksa “Eski/Returning” mi olduğunu tahmin etmek.
•	Model:  	DecisionTreeClassifier
	 	RandomForestClassifier
		KNeighborsClassifier

Toplam Model Sayısı
Projede 4 model denenmiştir:
1.	DecisionTreeRegressor (regresyon)
2.	DecisionTreeClassifier (sınıflandırma)
3.	RandomForestClassifier (sınıflandırma)
4.	KNeighborsClassifier (sınıflandırma)


A.	DECİSİON TREE MODELİ

a)	Customer Type

Aşağıdaki görsel, “Customer_Type” (müşterilerin yeni mi eski mi olduğunu) sınıflandırmak üzere eğittiğimiz Decision Tree modelinin yapısını temsil eder. Aşağıdaki diyagram, veri setindeki özelliklere göre nasıl dallanma yaptığını ve her düğümde hangi kararı aldığını göstermektedir.
 ![image](https://github.com/user-attachments/assets/b51537be-d48f-4d1d-a104-2e685a57e8bc)


b)	Sales Category

Aşağıdaki görsel, satış kategorisi tahmini için Decision Tree modelimizin yapısını gösterir. Aşağıdaki dallanma, hangi özellik değerlerinin hangi şekilde ayrıştırıldığını göstermektedir.

![image](https://github.com/user-attachments/assets/c7d18b84-8389-44dc-858e-e00620aaeea1) 

Sales Category’nin karar ağacı metrikleri aşağıdaki resimde sunulmuştur.
 
![image](https://github.com/user-attachments/assets/5204bee1-2bc6-46f2-9882-fa6ca5794c92)


B.	RANDOM FOREST MODELİ

a)	Customer Type

Bu grafikte, Random Forest modelimizin her bir özelliğe (feature) ne kadar önem atadığını görebilirsiniz. Feature Importance değeri yüksek olan özellik, modelin tahmin sürecinde daha belirleyici bir rol oynar.
Aşağıdaki görselde:
1.	‘year’ modeldeki en önemli özellik olarak öne çıkıyor.
2.	‘TotalRevenue’ onu yakından takip ediyor.
3.	‘month’ ve ‘quantity’ ise diğerlerine göre biraz daha düşük önem değerlerine sahip.
4.	Bu durum, modelin “yıl” bilgisini ve “toplam gelir (totalRevenue)” bilgisini tahmin yaparken güçlü bir sinyal olarak kullandığını göstermektedir.

 ![image](https://github.com/user-attachments/assets/4c5cba00-596a-494e-979d-eb0335bcac22)


Bu projede iki aşamalı bir eğitim süreci bulunmaktadır:

İlk Eğitim: Başlangıçta elimizdeki Northwind veritabanı üzerinden veriler çekilerek bir makine öğrenmesi modeli (DecisionTreeRegressor) eğitilir. Model dosyası (sales_model.pkl) olarak kaydedilir.

Postman'dan Veri Alarak Yeniden Eğitim: Daha sonra, yeni veriler Postman üzerinden API'ye (/retrain) gönderildiğinde, bu veriler kaydedilip retrain endpoint'iyle model yeniden eğitilir. Böylece model güncel verilerle sürekli öğrenmiş olur.

POST /retrain: Biriktirilen veya anlık gelen veriyi kullanarak modeli yeniden eğitir ve güncellenmiş *.pkl dosyasını oluşturur.
1.	model.fit komutuyla model ilk defa eğitilir ve sales_model.pkl oluşturulur.
2.	Postman'dan /predict çağrıları yapılır
3.	Postman'dan /retrain çağrısı yapılarak biriken verilerle model güncellenir.
Bu yaklaşım, modelinizin canlı veriden öğrenerek kendini iyileştirmesine olanak tanır.
________________________________________

🧪6.  API İstekleri

1. GET /products
GET : http://127.0.0.1:8000/products 
Bu endpoint, mevcut ürün listesini JSON formatında döndürür.
2. POST /predict
POST : http://127.0.0.1:8000/predict 

2. POST /retrain
POST : http://127.0.0.1:8000/retrain
{ "data": [ { 	"product_id": 1, 
"unit_price": 12.5, 
"month": 4, 
"year": 2025, 
"TotalRevenue": 100.0, 
"quantity": 10 }, 

{ "product_id": 2, 
"unit_price": 8.5, 
"month": 3, 
"year": 2025, 
"TotalRevenue": 85.0, 
"quantity": 7 } ] }

 ![image](https://github.com/user-attachments/assets/8adf4c2e-05a4-421f-95ca-302f04d2e665)

________________________________________

🧪 7. Postman Dokümantasyonu

Aşağıda Postman Documenter aracılığıyla oluşturulmuş bir API dokümantasyon sayfası mevcuttur. Postman, oluşturulan koleksiyonlardaki uç noktaları (endpoint), istek yapılarını, parametreleri ve örnek yanıtları otomatik olarak güzel bir arayüzle bu linkte sunmaktadır. İlgili linke tıklayarak, uç noktaların tam URL’lerini, HTTP metotlarını, parametre ve header bilgileri görüntülenebilmektedir. 

https://documenter.getpostman.com/view/41937581/2sB2cVe1xT 

Aşağıdaki resimde, Postman üzerinden paylaşılan API dokümantasyon sayfasının görünümünü görüyorsunuz. Sol tarafta “My API” başlığı altında tanımlanmış uç noktalar (`/products`, `/predicts`, `/retrain`) listeleniyor. Sağ tarafta ise bu uç noktayla ilgili örnek `cURL` isteği ve potansiyel cevap (response) bölümleri yer alıyor. `Run in Postman` düğmesiyle koleksiyonu doğrudan kendi Postman hesabınıza aktarabilirsiniz. Böylece proje API’sini hızlıca test edebilir, farklı parametrelerle örnek istekler gönderebilirsiniz.

 ![image](https://github.com/user-attachments/assets/9ccf0c77-d628-45a3-b2b3-3e0a6be40363)
 
________________________________________

🧪 8. Test ve Dağıtım

1. API uç noktalarının **Postman** ve **Swagger** üzerinden test edilmesi  
2. API’ye **örnek talepler** gönderilmesi  
3. Hata yönetimi ve validasyon (ör. `HTTPException`, `pydantic`)  
4. **`requirements.txt`** ile proje bağımlılıklarının dışa aktarılması

________________________________________

⚙️ 9. Nasıl Çalıştırılır?

1.	Projeyi kopyalayın:
git clone [https://github.comsales_forecasting_api](https://github.com/FatmaBeg/sales-forecasting-api/tree/main/sales_forecasting_api)
2.	Gerekli kütüphaneleri yükleyin:
pip install -r requirements.txt
3.	API'yi başlatın:
uvicorn sales:app --reload
4.	Swagger Arayüzü: http://127.0.0.1:8000/docs 
________________________________________

⚙️ 10. Hata Yönetimi ve Exception Kullanımı

Projede, FastAPI'nin HTTPException sınıfı kullanılarak hata yönetimi sağlanır. Böylece API uç noktalarında bir hata oluştuğunda, kullanıcıya anlamlı ve HTTP standartlarına uygun yanıt döndürülür.
1.	Prediction Hatası (500)
   
	except Exception as e:
    raise HTTPException(status_code=500, detail=f"Prediction failed: {str(e)}")

Bu blok, /predict gibi uç noktalarda tahmin işlemi sırasında beklenmeyen bir hata olduğunda tetiklenir.

2.	Model Eğitimi Hatası (500)
   
except Exception as e:
    raise HTTPException(status_code=500, detail=f"Model eğitimi sırasında hata oluştu: {str(e)}")

Model eğitimi ya da yeniden eğitme (retrain) süreci sırasında bir hata meydana geldiğinde kullanılır.

3.	Veri Bulunamadı (404)
   
if df_sales.empty:
    raise HTTPException(status_code=404, detail="No sales data found.")

/sales_summary uç noktasında veritabanında satış bilgisi yoksa 404 Not Found döndürülmesi sağlanır.
