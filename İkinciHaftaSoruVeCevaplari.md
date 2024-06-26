### Temel Türler & Null Güvenliği

1. `val` ile `var` arasındaki fark nedir?

    val (value) değerler salt okunur değerlerdir ve ilk atamadan sonra başka bir değer ataması yapılamaz. Ancak değeri başka değişkenlere bağlı ise bağlı olduğu değişkenler değiştiğinde val'ın da değeri değişir. var (variable) ise değerleri değiştirilebilen değişkenlerdir.

2. Bir `var` değişkeni `val` gibi davranmasını nasıl sağlayabiliriz `val` kelimesini kullanmadan? Bunu neden yapmak isteriz? Örnek bir senaryo verin.

    Bir `var` değişkeninin `val` gibi davranmasını yani yeniden atama yapılmamasını sağlamak için setter fonksiyonunu private yaparız. Kotlin'de bir sınıf içinde tanımladığımız değişkenler aslında Java tarafında property'lere denk geldiği için ve varsayılan olarak private ile enkapsüle edilmiş olduğu için setter fonksiyonuna müdahale ederek onun hiç değiştirememesini sağlayabiliriz. Örneğin:

    ```kotlin
    class Cat() {
        var name: String = "Stray cat"

        var pawAmount: Int = 4
            private set

        fun setPawAmount(amount: Int) { // değişkeni bir fonksiyon yardımıyla daha güvenli bir şekilde değiştirmesini sağlayabiliriz.
            if(amount <4) {
                // hata ver
                println("Not enought paws, sorry!")
            } else {
                // kabul et
                pawAmount = amount
            }
        }
    }

    fun main() {
        val cat = Cat()

        cat.pawAmount = 3 // Değiştirmeye izin vermeyecek, hata verecektir.
        
        cat.setPawAmount(5) // Bu yöntemde değiştirmesine izin verebiliriz.
    }
    ```
 
3. "Değişmez" (Immutable) ve "Salt Okunur" (ReadOnly) kavramlarını açıklayın. `val` değişkenler neden aslında "değişmez" değil de "salt okunur" olarak açıklanmalıdır?

    Değişmez (immutable) değerler oluşturulduktan sonra hiçbir şekilde değeri değişmeyen değerlerdir. Salt okunur (read-only) değerler ise bir kere atanabilen (set edilen) değerlerdir ve değerleri başka bir değere bağlı olması halinde değişebilecek değerlerdir. `val` değerler salt okunur değerlerdir çünkü sadece bir kere değer ataması yapılıyor ancak değerleri başka bir değere bağlı ise değişebilir. Örneğin:

    ```kotlin
    fun main() {
        val police = Police()
        police.dispatchPolice()
    }

    class Police() {
        var officerName: String = "Officer Jack"
        var officerRank: Int = 4
        val officerBadge: String
            get() {
                return "Badge: $officerName, Rank: $officerRank"
            }

        fun dispatchPolice() {
            println(officerBadge)
            officerName = "Officer Malfoy"
            officerRank = 5
            println(officerBadge)
            officerName = "Officer Harry"
            officerRank = 3
            println(officerBadge)
        }
    }
    ```

4. "Tip Çıkarımı" (Type inference) kavramını açıklayın. Hangi durumlarda tip belirtmek kesin olarak gereklidir?

    Tip çıkarımı, değişkenlerin veri tipine göre otomatik olarak belirlenmesidir. Örneğin biz `var name = "Ferec"` yazdığımızda, IDE otomatik olarak `name` değişkeninin tipini String olarak atar. Eğer değişkenimizi bir class içerisinde tanımlıyorsak, bir değer ataması yapmamız şarttır.Fakat eğer lokal olarak bir fonksiyonun içerisinde bir değişken tanımlıyorsak, değişkeninin tipini kesin olarak belirtmemiz gerekir.

    ```
        fun runForward() {
            var velocity: Int // Burada başlangıç değeri vermemiz gerekmiyor.
        }

        class Runner() {
            val name: String? = null // Burada başlangıç değeri vermemiz şarttır.
        }
    ```

5. Kotlin'de tüm değişkenlerin sınıf olarak bulunması, "ilkel tip" (primitive type) olmadıkları anlamına gelir mi? Arka planda neler oluyor?

    Kotlin'de tüm değişkenler sınıf olarak tutulur. Fakat bu sınıflar, bytecode'a çevrilirken yapılan özel optimizasyonlar sayesinde ilkel hallerine dönüştürülürler. Kotlinde Int sınıfıyla tutulan veriler, Java tarafında ilkel int tipi olarak tutulur. String hariç bütün değişkenlerde bu şekilde tutulur. String, Java tarafında da sınıf halinde tutulur.

6. "Tip Güvenliği" (Type Safety) kavramını açıklayın.

    Kotlin'de "tip güvenliği" (type safety), kod üzerinde türlerle ilişkili hataları minimuma indirmesini sağlayan bir özelliktir. Bu, Kotlin'in tür sistemi tarafından sağlanır ve bizim veri türlerini doğru ve güvenli bir şekilde kullanmamızı sağlar. Kotlin, tür güvenliğini çeşitli yollarla sağlar. Null güvenliği, tip çıkarımı, tip kontrolleri, smart cast gibi özelliklerle kodumuzu minimum hata ile yazarız.

7. Bir değişkeni nullable yapmak için ne yapmalıyız?

    Kotlin'de bir değişkeni nullable yapabilmek için türün sonuna `?` koymamız gerekir. Örneğin:

    ```kotlin
        var age: Int? = calculateAge(1990)
    ```
    Yukardaki kodda `age` değişkeni null değer alabilir (nullable).

8. "Null Güvenliği" (Null Safety) kavramını açıklayın.

    Kotlin'de bulunan null güvenliği konsepti, kod yazarken NullPointerException hatalarını almamızın önüne geçmeyi amaçlar. Bu sayede null gelmemesi gereken değerleri eğer değer null ise hata verebilecek işleme sokmayız ve hata ayıklama işlemlerimizi daha da karmaşıklaştırmamış oluruz. Kotlin'de null güvenliğini Java'da olan if/else yapısı yanında let scope fonksiyonu, elvis fonksiyonu ile sağlayabiliriz. Null olup olmadığını belirlemede `?` ve `!!` operatörlerini kullanırız. Tabii burada `!!` operatörünü kullanırken son derece dikkatli olmalıyız çünkü bu IDE'ye o değerin null olmadığını belirtir. `?` ve `!!` kullanımı aslında işlemimizin kritikliğine göre değişir. Eğer kullanıcıya göstermemiz gereken hesaplama çok kritikse o zaman uygulamanın çökmesini de göze alarak `!!` kullanırız. Ancak işlem çok kritik değilse o hâlde `?` kullanabiliriz.

9. Bir değişkene null değer atanır ve tip belirtilmezse Kotlin bu değişkeni nasıl yorumlar?

    Bir değişkene null değeri atayıp, o değişkenin tipini belirtmezsek Kotlin bu değişkenin tipini Nothing? olarak belirler. Nothing o işlevin hiçbir zaman bir değer döndürmeyeceğini ifade eder. Nothing? sadece null değer alabilir ve başka hiçbir değeri temsil etmez.

10. İlkel bir değişkenin nullable olması ile null değer alamaması arasında bellek yönetimi açısından nasıl farklar vardır?

    İlkel tipler Stack denilen hafıza yapısında tutulur. Obje tipleri ise değerleri Heap'te, adresleri ise Stack'te tutulur. Heap, Stack'e göre nispeted daha yavaş çalışır. Bir değerin nullable yapılması, onun bir obje tipine çevirdiği için ilkel bir tipe göre daha nispeten daha yavaş çalışmasını ve daha fazla bellek harcamasını sağlar. 

11. Nullable bir değişkenin bir değere sahip olması ile null olması arasında bellek yönetimi açısından nasıl bir fark vardır? Null değer almış bir değişken bellekte yer kaplamaz diyebilir miyiz?

    Nullable bir değişkenin bir değere sahip olması, değişkenin değerinin Heap'te, adresinin de Stack'te tutulması demektir. Eğer null değer almışsa, adres hâla Stack'te tutulur ancak Heap'teki değer silinir. Bu durumda bellekte (Stack'te) hâla yer kaplamaktadır.

12. Nullable bir değişkenle çalışırken hangi operatörleri kullanırız? Bu operatörlerin kullanım farkları nelerdir? Hangisini ne zaman kullanmak daha anlamlıdır?

    Nullable değerlerle çalışırken temel olarak 3 operatörümüz vardır. Bunlar `?.` (safe call operator), `?:` (Elvis operator) ve `!!` (non-null assert operator)'dür. 
    * `?.` operatörünü değerin null olup olmadığını kontrol etmek için kullanırız. Eğer değer null değilse işlemi gerçekleştirir. Bu şekilde null değerleri güvenli bir şekilde işleyebiliriz ve olası hataların önüne geçebiliriz.
    * `?:` operatörünü, eğer değer null ise varsayılan bir işlemi çalıştırmak veya değeri vermek için kullanırız. Bu if/else yapısının kısa bir hâlidir.
    * `!!` operatörünü, nullable değişkenin null olmadığına dair bir garanti verir. Ancak değişken eğer null ise NullPointerException alırız. Bu yüzden dikkatli kullanmak gereklidir.

### Sayılar

1. Kaç farklı tipte "number" sınıfı miras alan "alt sınıf" (child class) vardır? Bunların değer aralıkları neden önemlidir?

    Number classını miras alan 6 adet alt sınıf vardır. Bunlar: `Byte`, `Short`, `Int`, `Long`, `Double` ve `Float` sınıflarıdır. Bu veri tiplerinin değerleri bizim için önemlidir çünkü bu veri tiplerinin alabileceği belirli değer aralıkları vardır. Aldıkları değer aralıklarına oranla bellekte farklı boyutlarda yer kaplarlar. Projelerimizde maksimum performansı alabilmek için, veri tiplerinin değer aralıklarına uygun bir şekilde çalışmalıyız. Örneğin daha büyük sayıları temsil etmek için Long veri tipini veya daha küçük sayıları temsil etmek için Byte veya Short veri tipini kullanmalıyız. Ayrıca kullandığımız veri tiplerinde değer aralıklarını aşmamaya dikkat etmeliyiz.

2. Eğer bir değişkene tip belirtimi yapılmaz ve bir değer atanırsa, Kotlin tip çıkarımını nasıl yapar?

    Kotlin derleyicisi, atanan değere göre uygun veri tipini çıkarır. Örneğin atanan değer bir sayı ise bunun değer aralığına bakarak, değişkenin tipini belirler. Sayılar özelinde, küçük sayıları biz özellikle belirtmediğimiz taktirde Int olarak kabul edecektir. Sayı Int aralığından çıktığında Long tipini çıkaracaktır.

3. Float değişken oluştururken `F` ve `f` harfleri varken, Long değişken oluştururken neden küçük `l` harfi yoktur?

    Bazı fontlarda 1(bir)'in yazımı ile küçük L harfinin yazımı birbirine çok benzediği için küçük l harfi karışıklık çıkartmaması için kullanılmaz.

4. Tek duyarlıklı (Single precision) ve Çift duyarlıklı (Double precision) kavramlarını açıklayın.

    Tek duyarlıklı sayılar, 32 bitlik bir kayan nokta formatını kullanırken, çift duyarlıklı sayılar 64 bitlik formatı kullanır. Tek duyarlıklı sayılar daha az bellek kullanırlar ancak daha düşük hassasiyete sahiptirler. Çift duyarlıklı sayılar ise daha fazla bellek kullanırlar ve daha yüksek hassasiyete sahiptirler.

5. Double ve Float değişkenlerle çalışırken ondalık ayıracı olarak hangi işaretler kullanılır? Bu ayıraçların kullanımında nelere dikkat etmek gerekir?

    Double ve Float değişkenlerle çalışırken, ondalık ayrıracı olarak nokta(.) kullanılır. Örneğin:

    ```kotlin
        val floatNumber: Float = 5.13f
        val doubleNumber: Double = 5.13
    ```

6. Double ve Float değişkenler ondalık kısımda kaç basamağa kadar işlem yaparlar? Bu sınırın üzerinde gelen ondalık bilgileri için nasıl davranırlar? Hangi durumlar için Float ve hangi durumlar için Double kullanılmalıdır?

    Float sayılar ondalıklı kısımda 6-7 basamağa kadar olan işlemleri yaparlar. Double sayılar ise ondalıklı kısımda 15-16 basamağa kadar olan işlemleri yaparlar. Bu sınırın üzerinde gelen ondalıklı kısımları var ise, bu sayılar yuvarlanır veya fazlalık olan kısım kesilir. Daha fazla hassasiyet gereken işlemlerimiz var ise double veri tipini kullanmamız bizim için daha uygun olacaktır. Örneğin, finansal veya bilimsel hesaplamalar gibi hassas verilerin işlendiği uygulamalarda kullanılabilir. Float veri tipini ise,daha düşük bellek kullanımı ve daha hızlı hesaplama gereken uygulamalarda kullanabiliriz. Örneğin oyunlar veya sinyal işleme gibi uygulamalarda kullanabiliriz.

7. Ondalık (Decimal), Onaltılık (Hexadecimal) ve İkilik (Binary) değişkenleri Kotlin'de nasıl tanımlayabilirsiniz?

    ```kotlin 
        val decimalNumber = 1990
        val hexadecimalNumber = 0x7F // ondalık sayı olarak 127'ye denk gelir.
        val binaryNumber = 0b10010 // ondalık sayı olarak 18'e denk gelir.
    ```

8. Sekizlik (Octal) değişkenler Java'da nasıl tanımlanır? Kotlin'de Sekizlik değişken tanımlanabilir mi?

    Java'da sayıların başına 0 ön eki getirilerek sekizlik sayılar ifade edilir. Ancak Kotlin'de bu geçerli değildir.Kotlin'de doğrudan bir sayısal ifadeyi sekizlik olarak tanımlayamayız. Bunun için Integer sınıfından parseInt veya toInt fonksiyonlarını kullanabiliriz. Örneğin:

    ```kotlin 
        fun main() {
            val numberToParse = 120

            octalToDecimalUsingParseInt(numberToParse.toString()) // çıktı 80'dir.

            octalToDecimalUsingToInt(numberToParse.toString()) // çıktı 80'dir.
        }

        fun octalToDecimalUsingParseInt(octal: String): Int {
            return Integer.parseInt(octal, 8)
        }

        fun octalToDecimalUsingToInt(octal: String): Int {
            return octal.toInt(8)
        }
    ```


9. "Geleneksel Notasyon" (Conventional Notation) gösterimi nasıl yapılır?

    Kotlin'de geleneksel notasyon, Double sayılar için "172.3, 172.3e10" şeklinde ve Float sayılar için "F veya f: 172.3f, 172.3F" şeklinde yapılabilir.

10. Sayısal değişkenlerde alt çizgi (underscore) nasıl kullanılır? Kotlin bunu nasıl yorumlar?

    Alt çizgi büyük sayıların okunabilirliğini arttırmak için kullanılır. IDE tarafından okunmazlar. `1_000_000` şekilde tanımlanan sayı, `1000000` şeklinde yorumlanır.

11. `==` ile neyi karşılaştırırız? `===` ile neyi karşılaştırırız?

    `==` operatörü iki değeri karşılaştırır ve değerler eşit ise `true` döndürür. `===` operatörü iki referansı karşılaştırır ve iki nesne de bellekte aynı yeri işaret ediyorsa `true` döndürür.

12. `===` operatörü ile karşılaştırma yaparken Byte değer aralığı neden önemlidir? Kotlin bu aralığa göre neden özel bir davranış sergiler?

    `===` operatörü ile -128 ile 127 arasındaki byte değerler için bellekte tek bir bölüm oluşturulur. Bu performans optimizasyonu için yapılmıştır. Bu aralık dışındaki değerler için her seferinde yeni bir yer ayırımı yapılır. Bu durumda `===` operatörü ile -128 ile 127 değer aralığındaki değerleri karşılaştırırken bellekteki yer aynı olduğu için değer farklı bile olsa `true` döner. Bu aralık dışındaki değerler için hem değer hem de bellekteki adresin karşılaştırmasını yapar.

13. Sayısal değişkenlerde hangi matematiksel operatörler kullanılabilir?

    Kotlin'de sayılar arasında toplama, çıkarma, çarpma, bölme ve mod alma işlemlerini yapabileceğimiz operatörler kullanılabilir. Ayrıca Kotlin Primitives veya FloorDivMod sınıfında tanımlı olan çeşitli extension fonksiyonlarını da kullanabiliriz. Örneğin:

    ```kotlin
        val sumWithOperator = 5 + 2
        val sumWithFunction = 5.plus(2)

        val subtractWithOperator = 5 - 2
        val subtractWithFunction = 5.minus(2)

        val multiplyWithOperator = 5 * 2
        val multiplyWithFunction = 5.times(2)

        val divisionWithOperator = 5 / 2
        val divisionWithFunction = 5.div(2)

        val modWithOperator = 5 % 2
        val modWithFunction = 5.mod(2) // mod fonksiyonu Kotlin'in FloorDivMod sınıfında tanımlı
    ```

14. Sayısal değişkenlerde hangi karşılaştırma operatörleri kullanılabilir?
    
    ```kotlin
        == (eşittir) -> İki değer birbirine eşitse true döndürür.
        != (eşit değildir) -> İki değer birbirine eşit değilse true döndürür.
        < (küçüktür) -> Sol taraftaki değer sağ taraftakinden küçükse true döndürür.
        > (büyüktür) -> Sol taraftaki değer sağ taraftakinden büyükse true döndürür.
        >= (büyük veya eşittir) -> Sol taraftaki değer sağ taraftakinden büyük veya eşitse true döndürür.
        <= (küçük veya eşittir) -> Sol taraftaki değer sağ taraftakinden küçük veya eşitse true döndürür. 
        in (aralığındadır) -> Sol taraftaki değer sağ taraftaki aralıkta (örneğin: a..b) ise true döner.
        !in (aralığında değildir) -> Sol taraftaki değer sağ taraftaki aralıkta (örneğin: a..b) ise false döner.
    ```

15. Bit düzeyinde operatörler (Bitwise operators) nelerdir? Ne amaçla kullanılır? Kotlin'de bunları nasıl kullanabilirsiniz?

        Bit düzeyinde operatörler, verilerin içindeki tekil bitlere doğrudan erişim sağlamamızı sağlayan operatörlerdir. Bu operatörler genellikle düşük seviyeli programlama, veri sıkıştırma, veri şifreleme ve performans optimizasyonu gibi alanlarda kullanılır. Bit düzeyinde operatörler şunlardır:

        1.  AND (&): İki biti AND işlemine tabi tutar. İki bit de 1 ise sonuç 1 olur, diğer durumlarda sonuç 0 olur.
        2.  OR (|): İki biti OR işlemine tabi tutar. En az bir bit 1 ise sonuç 1 olur, diğer durumda sonuç 0 olur.
        3.  XOR (^): İki biti XOR (exlusive OR) işlemine tabi tutar. İki bit farklı ise sonuç 1 olur, aynı ise sonuç 0 olur.
        4.  NOT (~): Bir bitin tersini alır, yani 0 ise 1 yapar, 1 ise 0 yapar.
        5.  Left Shift (<<): Belirli sayıda bitleri sola kaydırır. Sol tarafa eklenen boş bitler sıfır ile doldurulur.
        6.  Right Shift (>>): Belirli sayıda bitleri sağa kaydırır. Sağ tarafa eklenen boş bitler işaret bitinin değerine göre doldurulur (signed right shift).

        ```kotlin
            val a = 5 // 0101
            val b = 3 // 0011

            val andResult = a and b // 0101 & 0011 = 0001 (1)
            val orResult = a or b // 0101 | 0011 = 0111 (7)
            val xorResult = a xor b // 0101 ^ 0011 = 0110 (6)
            val notResult = a.inv() // ~0101 -> 1010 (-6)
            val leftShiftResult = a shl 1 // 0101 << 1 = 1010 (10)
            val rightShiftResult = a shr 1 // 0101 >> 1 = 0010 (2)
        ```

16. Kotlin'de büyük sayılarla çalışırken hangi ek türlerden yararlanılır ve bu türlerin sınırları nelerdir?

    Kotlin'de büyük sayılarla çalışırken,BigInteger ve BigDecimal sınıflarından yararlanılır. Bunun yanında ilkel tiplere atanmış MAX_VALUE değerleri de mevcuttur. BigInteger ve BigDecimal özelinde, sınırlar JVM'in atadığı belleğe göre değişebilir. Bu değer çok büyük olduğu için hem hafıza hem de çalışma hızı bakımından ağırlık oluşturabilirler. İlkel tiplere atanmış MAX_VALUE değerleri, direkt bu değerlerin içine girip görülebilir. Int, Double ve Long için MAX_VALUE değerleri aşağıdaki gibidir.

    ```java
        public const val MAX_VALUE: Int = 2147483647
        public const val MAX_VALUE: Double = 1.7976931348623157E308
        public const val MAX_VALUE: Long = 9223372036854775807L
    ```

17. Double ve Float değişkenler kullanılırken "yuvarlama" davranışı nasıldır? Bu nasıl değiştirilebilir?

    Kotlin'de varsayılan olarak ondalıklı sayılar, en yakın tam sayıya doğru yuvarla mekanizması ile çalışır. Bu işlem IEEE 754 standartına uygun gerçekleşir. Bu IEEE 754 standartına uygun olarak gerçekleşir. Finansal hesaplamalarda veya hassas yuvarlama gerektiren diğer senaryolarda BigDecimal'ın setScale metodunu kullanabiliriz. Örneğin:

    ```kotlin
        val doubleNumber = 1.5323512

        val roundedBigDecimal = doubleNumber.toBigDecimal().round(MathContext.DECIMAL64).setScale(3, RoundingMode.HALF_UP) // Burada ondalıklı sayının üçüncü ondalık basamağına bakarak yuvarlama yapar ve 5 veya daha büyükse yukarı yuvarlar, aksi takdirde aşağı yuvarlar.
    ```

    Bunun dışında Kotlin'in sağladığı `roundToInt()` veya `roundToLong()` fonksiyonlarını da kullanabiliriz. Kişiselleştirilmiş yuvarlama fonksiyonları da yazabiliriz.

### İşaretsiz Sayılar

1. "İşaretsiz" (Unsigned) değişkenler ne demektir? İşaretli olanlarla aralarındaki fark nedir?

    İşaretli (signed) değişkenler sıfırın yanında hem pozitif hem de negatif sayıları temsil eder ancak işaretsiz (unsigned) sayılar yalnızca sıfır ve pozitif
    sayıları temsil edebilir.

2. "İşaretsiz" değişkenler nasıl bir sınıf yapısında tutulurlar? Bu neden önemlidir?

    İşaretsiz değişkenler kendilerine özel sınıflarda tutulurlar (Örneğin; "UInt", "ULong"). İşaretsiz değişkenlerin bu şekilde tutulması önemlidir çünkü bu yapı, programcılara özellikle bellek kullanımı ve veri manipülasyonu gibi konularda daha fazla esneklik sağlar. Ayrıca, işaretsiz değişkenler, belirli durumlarda işaretli değişkenlere göre daha hızlı hesaplamalar yapmayı sağlayabilir, çünkü işaret bitiyle ilgilenmek gerekmez.

3. "İşaretsiz" değişkenlerin harf gösterimi nasıldır?

    İşaretsiz değişkenler, sayının sonuna `u` veya `U` eklenerek gösterilir. Örneğin:

    ```kotlin
        val unsignedNumber1 = 53u
        val unsignedNumber2 = 67U
    ```

4. "`val a1 = 42u` ve `val a2 = 0xFFFF_FFFF_FFFFu`" değişkenlerin tipleri ne olur? Neden?

    `a1` ismiyle tanımlanan değişken `UInt` tipindedir ve 32 bitlik bir işaretsiz tam sayıyı ifade eder. `a2` isminde tanımlanan değişken `ULong` tipindedir ve 64 bitlik bir işaretsiz tam sayıyı ifade eder. Bunu sebebi Kotlin dilindeki tip çıkarımı (type inference) sayesinde değerlerin aralığına göre derleyicinin uygun tipleri atamasıdır.

5. "İşaretsiz" "Long" harf gösterimi nasıl yapılır?

    İşaretsiz Long tipinde değişken oluşturmak için değerin sonuna "uL" ya da "UL" eklenmelidir. Örneğin: 


    ```kotlin
        val unsignedLong1 = 3288182375uL
        val unsignedLong2 = 3288182375UL
    ```

6. "İşaretsiz" değişkenlerin kullanım amaçları nelerdir?

    İşaretsiz değişkenlerin tercih sebepleri aşağıdakiler olabilir:
    
    * İşaretli değişkenlere göre daha geniş veri aralıklarını daha az bellek kullanarak saklamaları. Örneğin, `UInt`: 0 ile 2^32 - 1 arasındaki tam sayıları temsil eder. Yani, 0 ile 4,294,967,295 arasındaki değerleri alabilir. Int: -2^31 ile 2^31 - 1 arasındaki tam sayıları temsil eder. Yani, -2,147,483,648 ile 2,147,483,647 arasındaki değerleri alabilir.
    * Sadece sıfır veya pozitif değer alabilecek değerlerin saklanması. Örneğin yaş bilgisi, TC kimlik numarası, ayakkabı numarası vb.
    * Bazı durumlarda, işaretsiz değişkenler işlemleri daha hızlı gerçekleştirebilir çünkü işaret bitiyle ilgilenmek gerekmez, bu da işlemci tarafından daha etkin bir şekilde gerçekleştirilebilir.

7. "İşaretsiz" değişkenlerle yapılan matematiksel işlemlerde, özellikle büyük sayılarla çalışırken karşılaşılabilecek taşma (overflow) ve taşma olmaması (underflow) durumları için Kotlin nasıl bir yönetim sağlar?

    Eğer bir işlem sonucu, o değişkenin alabileceği maksimum değeri aşarsa, taşma durumu oluşur. Benzer şekilde, bir işlem sonucu, değişkenin alabileceği minimum değerden küçükse, underflow durumu gerçekleşir. Bu durumlarla karşılaşıldığında, Kotlin derleyicisi genellikle bir hata fırlatır ve programın beklenmeyen davranışlar sergilemesini engeller. Bu özellik, işaretsiz değişkenlerle yapılan matematiksel işlemlerde veri bütünlüğünü korumak ve beklenmeyen sonuçları önlemek için önemlidir. Böylece, programların daha güvenli ve tutarlı olmasını sağlar.

8. "İşaretsiz" değişkenlerin sınırlamaları nelerdir?

    ``` 
    UByte -> 8 bitlik yer kaplar. 0 - 255

    UShort -> 16 bitlik yer kaplar. 0 - 65,535 

    UInt -> 32 bitlik yer kaplar. 0 - 4,294,967,295 (2^32 - 1) 

    ULong -> 64 bitlik yer kaplar. 0 - 18,446,744,073,709,551,615 (2^64 - 1) 
    ```

9. "İşaretsiz" değişken türleri (UInt, ULong vs.) kullanırken, Java API'leri ile uyumluluk konusunda ne gibi sorunlar olabilir? Bunları çözmek için neler yapabilirsiniz?

    Birçok Java API'ı, işaretli değişkenlerle çalışır ve işaretsiz değişkenleri doğrudan kabul etmez. Bu durumda işaretsiz değişkenleri, işaretli değişkenlere çevirerek kullanmamız gerekecektir.

### Tür Dönüşümü

1. `is` ve `!is` operatörlerinin kullanımını açıklayın.

    * `is` operatörü, bir nesnenin belirli bir türe ait olup olmadığını kontrol eder. Eğer nesne o türe aitse, ifade `true` döner, aksi halde `false` döner.

    * `!is` operatörü ise bir nesnenin belirli bir türe ait olmadığını kontrol eder. Eğer nesne o türe ait değilse, ifade `true` döner, aksi halde `false` döner.

    ```kotlin
        fun getAgeOrAddress(input: Any) {
            if (input is String) {
                println("$input is the address")
            } else {
                println("$input is not an address")
            }

            if (input !is Int) {
                println("$input is not an age value")
            } else {
                println("$input is the age")
            }
        }
    ```

2. "Akıllı Dönüşüm" (Smart Cast) ne demektir? Farklı kod örnekleri ile açıklayın. Bu özelliğin sınırlamaları nelerdir?

    Akıllı Dönüşüm (Smart Cast), bir nesnenin türünün kontrol edildiği bir akış kontrolü ifadesinde, `is` operatörü kullanıldıktan sonra, Kotlin'in otomatik olarak bu nesneyi belirtilen türe dönüştürmesi ve bu türe özgü işlemleri gerçekleştirmenizi sağlamasıdır. Örneğin:

    ```kotlin
        fun findTheSuspect(clue: Any) {
            if (clue is String) {
                println("$clue is the murder letter. Its length is ${clue.length}")
            } else {
                if (clue is Date) {
                    println("$clue is the murder date. The murder date is ${clue.time}")
                } else {
                    println("$clue is useless!")
                }
            }
        }
    ```

    Üstteki örnekte `clue: Any` değerini `String` ve `Date` değerlerine akıllı dönüştürdük ve o sınıflara özel özellikleri kullandık.

3. "Güvenli & Güvensiz" operatörler nelerdir?

    Kotlin'de güvenli dönüşüm operatörü (safe cast) ve güvensiz dönüşüm (unsafe cast) operatörü bulunmaktadır. Bu operatörler, bir nesneyi belirli bir türe dönüştürmek için kullanılır.

    Güvenli Dönüşüm Operatörü: `as?` şeklinde kullanılır. Bu operatörün kullanımında eğer dönüşüm başarısız olursa `null` değeri atanır. Örneğin;

    ```kotlin
        val randomNumberString = "6"
        val randomNumber: Int? = randomNumberString as? Int

        println(randomNumber) // Bu örnekte randomNumberString bir String olduğu için randomNumber değişkeninin değeri `null` olur.
    ```

    Güvensiz Dönüşüm Operatörü : `as` şeklinde kullanılır.Bu operatörün kullanımında da eğer başarısız olunursa `ClassCastException` hatası fırlatılır.


    ```kotlin
        val randomNumberString = "6"
        val randomNumber: Int = randomNumberString as Int

        println(randomNumber) // Bu örnekte randomNumber nullable olmadığı için `ClassCastException` hatası fırlatılır.
    ```       

4. Sayısal değişkenlerde örtük tip genişletme (implicit widening conversions) ne demektir? Kotlin'de bu neden yapılamaz?

    Sayısal değişkenlerde örtük tip genişletme, bir veri türünün daha küçük bir türden daha büyük bir türe dönüştürülmesidir. Bu dönüşüm, genellikle otomatik olarak gerçekleşir ve programcının belirli bir dönüşüm işlemi gerçekleştirmesine gerek kalmaz. Örneğin, bir `byte` türünün bir `int` türüne otomatik olarak genişletilmesi gibi.

    Kotlin'de örtük tip genişletme yapılamaz çünkü Kotlin, Java'dan farklı olarak daha sıkı bir tür sistemi sunar. Bu sıkı tür sistemi, tür uyumsuzluklarını belirlemekte daha hassas olduğundan, otomatik genişletme işlemleri mümkün değildir. Bu nedenle, Kotlin'de sayısal değerler arasında tür dönüşümleri açıkça belirtilmelidir, yani örtük genişletme desteklenmez. Bu, tür uyumluluğunun daha net ve daha güvenli bir şekilde kontrol edilmesini sağlar ve tür hatalarını önler. Örneğin:

    ```kotlin
        val randomByte: Byte = 7
        val randomNumber: Int = randomByte.toInt() // Byte tipindeki veriyi açık bir şekilde Int'e çevirdik.
    ```       

5. `val b: Byte = 1` ile `val i: Int = b` ve son olarak `print(b == i)` gibi bir kod yazıldığında çıktı ne olur? Neden böyle bir çıktı aldığınızı açıklayın.

    Bu kod derlenme hatası verir çünkü üstteki soruda bahsettiğimiz gibi Kotlin'de örtük tip genişletme yoktur. Dolayısı ile `val i: Int = b` hata verecektir. Ayrıca `print(b == i)` ifadesi de hata verir çünkü farklı iki tip değişkeni `==` ile karşılaştıramayız.

6. `val b: Byte = 1` ile `val i: Int = b.toInt()` ve son olarak `print(b == i)` gibi bir kod yazıldığında çıktı ne olur? Neden böyle bir çıktı aldığınızı açıklayın.

    Bu kod, `print(b == i)` ifadesinden dolayı derlenme hatası verir çünkü farklı iki tip (`Byte` ve `Int`) değişkeni `==` ile karşılaştıramayız.

7. Sayısal değişkenlerde açık dönüşüm (Explicit Type Conversion) yaparken hangi fonksiyonları kullanabilirsiniz?

    toByte(), toShort(), toInt(), toLong(), toFloat(), toDouble(), toUByte(), toUShort(), toUInt(), toULong(), toDuration(), toBigDecimal(), toBigInteger() gibi fonksiyonlar kullanılabilir.

8. `val result = 1L + 3`" // "Long + Int" gibi bir işlemin sonucunda "result" değişkeninin tipi ve değeri ne olur? Neden böyle olduğunu açıklayın.

    Bu sonucun değeri 4L ve tipi de Long olacaktır. Bunun sebebi, işlemin sonucunun daha büyük olan Long aralığına düşmesinden dolayıdır. 

9. `val result = Int.MAX_VALUE + Int.MAX_VALUE` gibi bir işlemin sonucunda "result" değişkeninin tipi ve değeri ne olur? Neden böyle olduğunu açıklayın.

    Sonuçtaki değer Int tipinden olur ancak bir Int'in alabileceği maksimum değeri 2 kere topladığımız için  taşma (overflow) olur. Kotlin'de varsayılan olarak taşma kontrolü yapılmadığı için `result` değerine anlamsız bir sayı atanır.

10. `val x = 5 / 2` `println(x == 2)` gibi bir işlemin sonucu ve tipi nedir? Neden böyle olduğunu açıklayın.

    İki adet `Int` sayıyı böldüğümüzde sonuç da bir `Int` sayı olacaktır. Bu durumda `x` değişkeninin değeri 2,tipi ise `Int` olacaktır. Print de bize `true` değerini yazdıracaktır.

11. `val x = 5L / 2` `println(x == 2L)` gibi bir işlemin sonucu ve tipi nedir? Neden böyle olduğunu açıklayın.

    `Long` bir sayıyı `Int` bir sayıya böldüğümüzde sonuç büyük olanın tipinde yani `Long` tipinde olur. Değişkenin değeri ise 2L olacaktır. Print de bize `true` değerini yazdıracaktır.

12. `val x = 5 / 2.toDouble()` `println(x == 2.5)` gibi bir işlemin sonucu ve tipi nedir? Neden böyle olduğunu açıklayın.

    `Int` bir sayıyı `Double` bir sayıya böldüğümüzde sonuç büyük olanın tipinde yani `Double` tipinde olur. Değişkenin değeri ise 2.5 olacaktır. Print de bize `true` değerini yazdıracaktır.

13. Kotlin'de tür dönüşümü yapılırken, dönüşümün başarısız olması durumunda TypeCastException nasıl ele alınır ve bu tür hataların önüne geçmek için hangi önlemler alınabilir?

    TypeCastException'a karşı try-catch blokları kullanabiliriz. Güvenli dönüşüm operatörü'nü (safe cast operator, `as?`) kullanabiliriz. `is` ile tip denetimi (type checking) yapabiliriz.