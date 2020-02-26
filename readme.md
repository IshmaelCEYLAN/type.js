Bu javascript kütüphanesi türler ve davranışlar tanımlamanızı sağlar. Tanımlanan bir tür başka
türler tarafından miras alınabilir. Tanımlanan davranışlar da türler tarafından kullanılabilir.

Bu kütüphane özünde prototype mekanizmasını kullanır.

# Örnek Kullanım
## Canlılara özgü bazı davranışlar oluşturalım
```javascript
var NefesAlma = Trait( "NefesAlma" ).prototype(
{
    nefesAl: function()
    {
        console.log( "Ohh! Nefes alabiliyorum." );
    }
});
```

Davranışlar, onu kullanan türlerin prototype alanına eklenirler. Dolayısıyla etki alanı
(this sözcüğü) ait oldukları türdür.

## Tür oluşturma
```javascript
var Canli = Type( "Canli" ).use( NefesAlma ).prototype(
{
    construct: function()
    {
        console.log( "Yaşasın! Hayattayım." );
    }
    
    yasa: function()
    {
        console.log( "Canlıyım, yaşıyorum!" );
    }
});
```

`construct` metodu oluşturulan türün kurucu metodudur. Bir türü örneklediğinizde verilen
parametreleri alarak bir defa çalışır. Bu metot içinde kurulumsal işlemleri yapabilir
ilklendirmemiz gereken özelliklere gerekli değerleri sağlayabiliriz.

## Ara tür oluşturalım
```javascript
var Hayvan = Type( "Hayvan" ).extends( Canli ).prototype(
{
    construct: function()
    {
        // önce Canli türünün kurucusu çalışsın
        this.super( "construct" );
        
        // şimdi hayvan türünü ilgilendiren işlemler çalışsın
        console.log( "Bitki değilim, özümde bir hayvan yatıyor." );
    }
});
```
## İnsanlara özgü bazı davranışlar oluşturalım
```javascript
var Konusma = Trait( "Konusma" ).prototype(
{
    konus: function( sozler )
    {
        console.log( "Konuşuyorum!", sozler );
    }
});
```

Davranış (trait) da `construct` metodu tanımlayabilir. Bu metot da yine davranışı
kullanan türün etki alanında çalışacaktır. Davranışa ilişkin ilksel işlemleri
gerçekleştirmek için kullanışlı bir alan sağlar. Türün kurucusu içinde kirlilik
oluşturmak gerekmez.

## Yeni ve güçlü bir tür oluşturalım
```javascript
var Insan = Type( "Insan" ).extends( Hayvan ).use( Konusma ).prototype(
{
    AS:
    {
        // Konusma isimli trait üzerinden alınan konus isimli
        // metodu konusmaYap şeklinde yeniden adlandırabiliriz
        konus: "konusmaYap"
    },
    
    construct: function( isim )
    {
        // önce Hayvan türünün kurucusu çalıştırılsın
        this.super( "construct" );
        
        // artık Insan türünün kurulumsal işlemlerini yapabiliriz
        console.log( "Ve adım da " + isim );
        
        // hemen nefes almaya başlasın yoksa ölür :|
        this.nefesAl();
        // hemen konuşmaya başlasın yoksa yine ölür keh keh :P
        this.konusmaYap( "bunlar ilk sözlerim" );
    }
    
    yasa: function()
    {
        // Bu metot taa canlı türünden bu noktaya kadar miras yoluyla geldi
        // ancak biz burada tekrar metodu tanımladık dolayısıyla mirası reddettik
        // ama reddetmek değil, bu mirası geliştirmek üzerine bir şeyler katmak istiyorum
        this.super( "yasa" );
        
        // şimdi genişleme işlemlerini yapabilirim
        console.log( "İnsan gibi yaşıyorum!" );
    }
});
```

`super` metodu miras alınan türe erişimi sağlar.

#### Ebeveynin belli bir metodunu call etmek
super(String, [...Array])

#### Ebeveynin construct metodunu call etmek
super(...Params)

#### Ebeneynin bağlamına erişmek
super()

## Türü örnekleyelim
```javascript
var ismail = Insan.new();

    ismail.yasa();
    ismail.konusmaYap( "merhaba dünya" );
```

## Tür sınama
```javascript
ismail.is( Canli );
// true

ismail.behave( NefesAlma );
// true
```

## Singleton örnekleme
```javascript
// ## app.js
var insanoglu = Insan.singleton();
    insanoglu.isim = "hıdır";

// ## aynı runtime
// ## islem.js
Insan.singleton().isim;
// hıdır
```
