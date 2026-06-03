# Factory Method (Fabrika Yöntemi) Tasarım Kalıbı

## Sorun

Lojistik yönetim uygulaması geliştirdiğinizi düşünün. İlk versiyonunuz sadece kamyon taşıması yapabilir ve kodunuzun çoğu `Truck` sınıfına bağlıdır.

Zamanla uygulamanız popüler olur ve gemi taşıması şirketleri uygulamaya katılmak ister. Ancak sorun şu:

- Mevcut kod `Truck` sınıfına sıkıca bağlıdır
- Yeni `Ship` sınıfı eklemek tüm kodları değiştirmeyi gerektirir
- Sonraki taşıma türleri de aynı sorunu yaratacaktır
- Sonunda sınıf türüne göre davranan koşullu ifadelerle dolu "çirkin" kod ortaya çıkar

## Çözüm

**Factory Method**, nesne oluşturma çağrılarını (`new` operatörü) özel bir fabrika metoduna taşımayı önerir.

### Nasıl Çalışır?

1. **Ortak Interface**: Tüm ürünler (`Truck`, `Ship`) aynı interface'i implement eder
2. **Fabrika Metodu**: Creator sınıfı içinde nesneleri oluşturan metot
3. **Polimorfizm**: Her Creator alt sınıfı kendi ürün türünü döner

### Örnek Yapı

```
RoadLogistics (Creator)
    ↓ createTransport()
    → Truck (Product)

SeaLogistics (Creator)
    ↓ createTransport()
    → Ship (Product)
```

## Yapı (Structure)

- **Product (Ürün)**: Tüm ürünlerin izlemesi gereken interface
- **Concrete Products**: Interface'in farklı uygulamaları
- **Creator**: Fabrika metodunu tanımlayan abstract sınıf
- **Concrete Creators**: Creator alt sınıfları

## Pseudocode Örneği

```java
// Creator sınıfı
abstract class Dialog {
    abstract method createButton(): Button
    
    method render() {
        Button okButton = createButton()  // Fabrika metodunu çağır
        okButton.onClick(closeDialog)
        okButton.render()
    }
}

// Concrete Creators
class WindowsDialog extends Dialog {
    method createButton(): Button {
        return new WindowsButton()
    }
}

class WebDialog extends Dialog {
    method createButton(): Button {
        return new HTMLButton()
    }
}

// Product Interface
interface Button {
    method render()
    method onClick(f)
}

// Concrete Products
class WindowsButton implements Button {
    method render() { /* Windows tarzı buton */ }
    method onClick(f) { /* Windows click olayı */ }
}

class HTMLButton implements Button {
    method render() { /* HTML buton */ }
    method onClick(f) { /* Web click olayı */ }
}
```

## Kullanım Durumları

1. **Önceden Bilinmeyen Türler**: Çalışacağınız nesne türleri koşullara bağlıysa
2. **Kütüphane Genişletmesi**: Kullanıcıların framework'ü genişletmesine izin vermek
3. **Kaynak Yönetimi**: Mevcut nesneleri yeniden kullanmak (cache, pool vb.)

## Avantajlar ve Dezavantajlar

### ✅ Avantajlar
- Creator ve concrete ürünler arasında sıkı bağlantı ortadan kalkar
- Single Responsibility Principle: Ürün oluşturma kodu bir yerde
- Open/Closed Principle: Yeni ürün türleri eklenmesi kolay

### ❌ Dezavantajlar
- Kod karmaşıklığı artar (yeni alt sınıflar gerekir)
- Basit durumlar için aşırı mühendislik olabilir

## Diğer Kalıplarla İlişkisi

- **Abstract Factory** ile benzer, daha karmaşık durumlar için
- **Prototype**, **Builder** ile birlikte kullanılabilir
- **Template Method** ile benzer ilişki vardır

## Gerçek Dünya Örneği: Sosyal Ağ Bağlantısı

```php
// Sosyal ağ seçimine göre uygun connector oluştur
interface SocialNetworkConnector {
    public function logIn(): void;
    public function createPost($content): void;
    public function logout(): void;
}

class FacebookPoster {
    public function getSocialNetwork(): SocialNetworkConnector {
        return new FacebookConnector();
    }
}

class LinkedInPoster {
    public function getSocialNetwork(): SocialNetworkConnector {
        return new LinkedInConnector();
    }
}
```

İstemci kodu hangi sosyal ağ olduğunu bilemez, sadece interface üzerinden çalışır.
