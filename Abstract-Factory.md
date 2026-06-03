# Abstract Factory (Soyut Fabrika) Tasarım Kalıbı

## Amaç

Abstract Factory, birbirleriyle ilişkili nesne ailelerini üretebildiğiniz, ancak onların somut sınıflarını belirtmemenize izin veren yaratıcı bir tasarım kalıbıdır.

## Sorun

Bir mobilya dükkanı simülatörü geliştirdiğinizi düşünün. Kodunuz şunları içerir:

### İlişkili Ürün Aileleri
- **Sandalye + Kanepe + Kahve Masası**

### Ürün Varyantları
- **Modern**: Modern Sandalye + Modern Kanepe + Modern Masası
- **Victorian**: Victorian Sandalye + Victorian Kanepe + Victorian Masası
- **ArtDeco**: ArtDeco Sandalye + ArtDeco Kanepe + ArtDeco Masası

### Sorunlar
1. **Uyuşmazlık**: Modern kanepe ile Victorian sandalyenin eşleşmemesi
2. **Genişlemezlik**: Yeni ürün ailesi eklemek tüm kodu değiştirmeyi gerektirmesi
3. **Bakım Zorluğu**: Mobilya katalogları sık güncellendiğinde kod değişimi

## Çözüm

### 1. Adım: Ürün İçin Interface Oluştur

Her ürün türü için ayrı interface tanımla:

```java
interface Chair {
    void sitOn();
}

interface Sofa {
    void lieDown();
}

interface CoffeeTable {
    void putObject();
}
```

### 2. Adım: Varyantları Implement Et

Her varyant türünü implement et:

```java
class ModernChair implements Chair { }
class VictorianChair implements Chair { }

class ModernSofa implements Sofa { }
class VictorianSofa implements Sofa { }
```

### 3. Adım: Abstract Factory Interface

Tüm ürünleri oluşturan factory interface'i tanımla:

```java
interface FurnitureFactory {
    Chair createChair();
    Sofa createSofa();
    CoffeeTable createCoffeeTable();
}
```

### 4. Adım: Concrete Factories

Her varyant için ayrı factory oluştur:

```java
class ModernFurnitureFactory implements FurnitureFactory {
    public Chair createChair() {
        return new ModernChair();
    }
    
    public Sofa createSofa() {
        return new ModernSofa();
    }
    
    public CoffeeTable createCoffeeTable() {
        return new ModernCoffeeTable();
    }
}

class VictorianFurnitureFactory implements FurnitureFactory {
    public Chair createChair() {
        return new VictorianChair();
    }
    
    public Sofa createSofa() {
        return new VictorianSofa();
    }
    
    public CoffeeTable createCoffeeTable() {
        return new VictorianCoffeeTable();
    }
}
```

## Yapı (Structure)

```
┌─────────────────────────────────────────────────┐
│            FurnitureFactory                     │
│  (Abstract Factory Interface)                   │
│  + createChair()                                │
│  + createSofa()                                 │
│  + createCoffeeTable()                          │
└─────────────────┬───────────────────────────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
┌───────▼──────────┐ ┌─────▼──────────┐
│ ModernFactory    │ │ VictorianFact. │
└──────────────────┘ └────────────────┘
        │                   │
   Ürünler:            Ürünler:
   ├─ ModernChair      ├─ VictorianChair
   ├─ ModernSofa       ├─ VictorianSofa
   └─ ModernTable      └─ VictorianTable
```

## Pseudocode

```java
// Abstract Factory Interface
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Concrete Factories
class WinFactory implements GUIFactory {
    public Button createButton() {
        return new WinButton();
    }
    
    public Checkbox createCheckbox() {
        return new WinCheckbox();
    }
}

class MacFactory implements GUIFactory {
    public Button createButton() {
        return new MacButton();
    }
    
    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}

// Abstract Products
interface Button {
    void paint();
}

interface Checkbox {
    void paint();
}

// Concrete Products
class WinButton implements Button {
    public void paint() {
        // Windows tarzı buton
    }
}

class MacButton implements Button {
    public void paint() {
        // macOS tarzı buton
    }
}

// İstemci Kodu
class Application {
    private GUIFactory factory;
    private Button button;
    
    public Application(GUIFactory factory) {
        this.factory = factory;
    }
    
    public void createUI() {
        this.button = factory.createButton();
    }
    
    public void paint() {
        button.paint();  // OS'e bağlı olacak
    }
}

// Başlatma
class Main {
    public static void main(String[] args) {
        GUIFactory factory;
        
        if (isWindows()) {
            factory = new WinFactory();
        } else {
            factory = new MacFactory();
        }
        
        Application app = new Application(factory);
    }
}
```

## Kullanım Durumları

### 1. **İlişkili Ürün Aileleri**
Kodunuz birbirleriyle uyumlu olması gereken ürünlerle çalışıyorsa

### 2. **Çoklu Varyantlar**
Aynı arayüzü takip eden ancak farklı uygulamalara sahip olan ürünler

### 3. **Kütüphane/Framework Genişletmesi**
Kullanıcıların yeni varyantlar eklemesine izin vermek

### 4. **Platform Bağımsızlığı**
Windows, macOS, Linux gibi platformlar arasında geçiş yapmak

## Uygulama Adımları

1. **Ürün Matrisini Harita Et**: Ürün türleri vs. varyantlar
2. **Abstract Product Interface'leri Tanımla**: Her ürün türü için
3. **Abstract Factory Interface**: Tüm creation metodları
4. **Concrete Factories Implement Et**: Her varyant için
5. **İstemci Kodunu Güncelle**: Factory üzerinden kullan

## Avantajlar ve Dezavantajlar

### ✅ Avantajlar
- **Uyum Garantisi**: Factory'den gelen ürünler her zaman uyumlu
- **Sıkı Bağlantı Yok**: İstemci somut sınıflara bağımlı değil
- **Single Responsibility**: Ürün oluşturma kodu izole edilmiş
- **Open/Closed Principle**: Yeni varyantlar kolay eklenir

### ❌ Dezavantajlar
- **Kod Karmaşıklığı**: Çok sayıda interface ve sınıf
- **Aşırı Mühendislik**: Basit projeler için gereksiz olabilir

## Diğer Kalıplarla İlişkisi

| Kalıp | İlişki |
|-------|--------|
| **Factory Method** | Daha basit, Abstract Factory'ye evrilebilir |
| **Builder** | Kompleks nesneler için adım adım oluşturma |
| **Prototype** | Abstract Factory ile birlikte kullanılabilir |
| **Singleton** | Factory'ler singleton yapılabilir |

## Gerçek Dünya Örneği: Web Template Render Sistemi

### Senaryo
Web uygulaması farklı template motorlarını (Twig, PHP Template) desteklemeli, ancak her template motoru için uygun template'ler oluşturmalı.

```php
// Abstract Factory
interface TemplateFactory {
    public function createTitleTemplate(): TitleTemplate;
    public function createPageTemplate(): PageTemplate;
    public function getRenderer(): TemplateRenderer;
}

// Twig Factory
class TwigTemplateFactory implements TemplateFactory {
    public function createTitleTemplate(): TitleTemplate {
        return new TwigTitleTemplate();
    }
    
    public function createPageTemplate(): PageTemplate {
        return new TwigPageTemplate(
            $this->createTitleTemplate()
        );
    }
    
    public function getRenderer(): TemplateRenderer {
        return new TwigRenderer();
    }
}

// PHP Template Factory
class PHPTemplateFactory implements TemplateFactory {
    public function createTitleTemplate(): TitleTemplate {
        return new PHPTemplateTitleTemplate();
    }
    
    public function createPageTemplate(): PageTemplate {
        return new PHPTemplatePageTemplate(
            $this->createTitleTemplate()
        );
    }
    
    public function getRenderer(): TemplateRenderer {
        return new PHPTemplateRenderer();
    }
}

// Abstract Products
interface TitleTemplate {
    public function getTemplateString(): string;
}

interface PageTemplate {
    public function getTemplateString(): string;
}

// Concrete Products - Twig Versions
class TwigTitleTemplate implements TitleTemplate {
    public function getTemplateString(): string {
        return "<h1>{{ title }}</h1>";
    }
}

class TwigPageTemplate implements PageTemplate {
    private $titleTemplate;
    
    public function __construct(TitleTemplate $titleTemplate) {
        $this->titleTemplate = $titleTemplate;
    }
    
    public function getTemplateString(): string {
        $title = $this->titleTemplate->getTemplateString();
        return "<div class='page'>$title<p>{{ content }}</p></div>";
    }
}

// Concrete Products - PHP Versions
class PHPTemplateTitleTemplate implements TitleTemplate {
    public function getTemplateString(): string {
        return "<h1><?= \$title; ?></h1>";
    }
}

class PHPTemplatePageTemplate implements PageTemplate {
    private $titleTemplate;
    
    public function __construct(TitleTemplate $titleTemplate) {
        $this->titleTemplate = $titleTemplate;
    }
    
    public function getTemplateString(): string {
        $title = $this->titleTemplate->getTemplateString();
        return "<div class='page'>$title<p><?= \$content; ?></p></div>";
    }
}

// Renderers
interface TemplateRenderer {
    public function render(string $template, array $args): string;
}

class TwigRenderer implements TemplateRenderer {
    public function render(string $template, array $args): string {
        // Twig rendering
        return \Twig::render($template, $args);
    }
}

class PHPTemplateRenderer implements TemplateRenderer {
    public function render(string $template, array $args): string {
        extract($args);
        ob_start();
        eval(' ?>' . $template . '<?php ');
        $result = ob_get_contents();
        ob_end_clean();
        return $result;
    }
}

// İstemci Kodu
class Page {
    private $title;
    private $content;
    
    public function __construct($title, $content) {
        $this->title = $title;
        $this->content = $content;
    }
    
    public function render(TemplateFactory $factory): string {
        $pageTemplate = $factory->createPageTemplate();
        $renderer = $factory->getRenderer();
        
        return $renderer->render(
            $pageTemplate->getTemplateString(),
            ['title' => $this->title, 'content' => $this->content]
        );
    }
}

// Kullanım
$page = new Page('Hoş Geldiniz', 'Bu bir test sayfasıdır.');

// PHP Template ile render et
echo $page->render(new PHPTemplateFactory());

// Twig Template ile render et
echo $page->render(new TwigTemplateFactory());
```

### Avantajları
✅ Template motoru değişse bile istemci kodu değişmez  
✅ Her template motoru için uygun template'ler otomatik oluşturulur  
✅ Yeni template motoru eklemek kolaylaştırılmış  
