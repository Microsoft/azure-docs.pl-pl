---
title: Walidacja danych wejściowych — Microsoft Threat Modeling Tool — Azure | Microsoft Docs
description: Dowiedz się więcej na temat sprawdzania poprawności danych wejściowych w Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b0be672c0768b4facb6518c777d4fe56eb28aa9
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515680"
---
# <a name="security-frame-input-validation--mitigations"></a>Ramka zabezpieczeń: sprawdzanie poprawności danych wejściowych | Środki zaradcze 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Wyłącz obsługę skryptów XSLT dla wszystkich transformacji przy użyciu niezaufanych arkuszy stylów](#disable-xslt)</li><li>[Upewnij się, że każda Strona, która może zawierać zawartość z kontrolowanymi przez użytkownika, jest niedostępna z automatycznego wykrywania MIME](#out-sniffing)</li><li>[Ograniczanie lub wyłączanie rozpoznawania jednostek XML](#xml-resolution)</li><li>[Aplikacje wykorzystujące http.sys wykonywanie weryfikacji niezgodności adresów URL](#app-verification)</li><li>[Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki](#controls-users)</li><li>[Upewnij się, że parametry bezpieczne dla typu są używane w aplikacji sieci Web na potrzeby dostępu do danych](#typesafe)</li><li>[Użyj oddzielnych klas powiązania modelu lub listy filtrów powiązań, aby zapobiec usterce przypisywania grupowego MVC](#binding-mvc)</li><li>[Koduj niezaufane dane wyjściowe sieci Web przed renderowaniem](#rendering)</li><li>[Wykonaj walidację danych wejściowych i filtrowanie dla wszystkich właściwości modelu typu String](#typemodel)</li><li>[Narzędzie Oczyszczanie powinno być stosowane w polach formularza akceptujących wszystkie znaki, np. Edytor tekstu sformatowanego](#richtext)</li><li>[Nie przypisuj elementów DOM do ujścia, które nie mają wbudowanego kodowania](#inbuilt-encode)</li><li>[Sprawdź poprawność wszystkich przekierowań w aplikacji, które są zamknięte lub bezpiecznie gotowe](#redirect-safe)</li><li>[Implementuj sprawdzanie poprawności danych wejściowych dla wszystkich parametrów typu String zaakceptowanych przez metody kontrolera](#string-method)</li><li>[Ustaw limit czasu górnego limitu dla przetwarzania wyrażenia regularnego, aby zapobiec występowaniu z powodu nieprawidłowych wyrażeń regularnych](#dos-expression)</li><li>[Unikaj używania języka HTML. Raw w widokach Razor](#html-razor)</li></ul> | 
| **Database** (Baza danych) | <ul><li>[Nie używaj zapytań dynamicznych w procedurach składowanych](#stored-proc)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że Walidacja modelu odbywa się w metodach interfejsu API sieci Web](#validation-api)</li><li>[Implementuj sprawdzanie poprawności danych wejściowych dla wszystkich parametrów typu String zaakceptowanych przez metody interfejsu API sieci Web](#string-api)</li><li>[Upewnij się, że parametry bezpieczne dla typu są używane w interfejsie API sieci Web na potrzeby dostępu do danych](#typesafe-api)</li></ul> | 
| **Baza danych dokumentów platformy Azure** | <ul><li>[Użyj sparametryzowanych zapytań SQL dla Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Walidacja danych wejściowych WCF za poorednictwem powiązania schematu](#schema-binding)</li><li>[WCF — walidacja danych wejściowych za pomocą inspektorów parametrów](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Wyłącz obsługę skryptów XSLT dla wszystkich transformacji przy użyciu niezaufanych arkuszy stylów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zabezpieczenia XSLT](/previous-versions/windows/desktop/ms763800(v=vs.85)), [XsltSettings. EnableScript — Właściwość](/dotnet/api/system.xml.xsl.xsltsettings.enablescript) |
| **Kroki** | Język XSLT obsługuje skrypty w arkuszach stylów przy użyciu `<msxml:script>` elementu. Pozwala to na używanie funkcji niestandardowych w transformację XSLT. Skrypt jest wykonywany w kontekście procesu wykonującego transformację. Skrypt XSLT musi być wyłączony w niezaufanym środowisku, aby zapobiec wykonywaniu niezaufanego kodu. *W przypadku korzystania z platformy .NET:* Obsługa skryptów XSLT jest domyślnie wyłączona; należy jednak upewnić się, że nie został on jawnie włączony przy użyciu `XsltSettings.EnableScript` właściwości.|

### <a name="example"></a>Przykład 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
W przypadku korzystania z programu MSXML 6,0 obsługa skryptów XSLT jest domyślnie wyłączona; należy jednak upewnić się, że nie został jawnie włączony przy użyciu właściwości obiektu DOM XML AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
Jeśli używasz programu MSXML 5 lub starszego, skrypty XSLT są domyślnie włączone i należy je jawnie wyłączyć. Ustaw właściwość obiektu DOM XML AllowXsltScript na false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Upewnij się, że każda Strona, która może zawierać zawartość z kontrolowanymi przez użytkownika, jest niedostępna z automatycznego wykrywania MIME

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Część zabezpieczeń programu IE8 — Kompleksowa ochrona](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **Kroki** | <p>Dla każdej strony, która może zawierać zawartość do kontrolowania, należy użyć nagłówka HTTP `X-Content-Type-Options:nosniff` . Aby zapewnić zgodność z tym wymaganiem, można ustawić wymaganą stronę nagłówka na stronie tylko dla stron, które mogą zawierać zawartość sterowaną przez użytkownika, lub można ustawić ją globalnie dla wszystkich stron w aplikacji.</p><p>Każdy typ pliku dostarczonego z serwera sieci Web ma skojarzony [Typ MIME](https://en.wikipedia.org/wiki/Mime_type) (nazywany również *typem Content-Type* ), który opisuje charakter zawartości (czyli obraz, tekst, aplikacja itp.)</p><p>Nagłówek X-Content-options jest nagłówkiem HTTP, który umożliwia deweloperom określenie, że ich zawartość nie powinna być wykrywaniem MIME. Ten nagłówek jest przeznaczony do ograniczania ataków MIME-Sniffing. Obsługa tego nagłówka została dodana w programie Internet Explorer 8 (IE8)</p><p>Tylko użytkownicy programu Internet Explorer 8 (IE8) będą mogli korzystać z opcji X-Content-Type. Poprzednie wersje programu Internet Explorer nie respektują obecnie nagłówka X-Content-Type-Options</p><p>Program Internet Explorer 8 (i nowsze) to jedyne główne przeglądarki służące do implementowania funkcji rezygnacji z wykrywania MIME. Jeśli i gdy inne główne przeglądarki (Firefox, Safari, Chrome) implementują podobne funkcje, to zalecenie zostanie zaktualizowane w celu uwzględnienia również składni dla tych przeglądarek</p>|

### <a name="example"></a>Przykład
Aby włączyć wymagany nagłówek globalnie dla wszystkich stron w aplikacji, można wykonać jedną z następujących czynności: 

* Dodaj nagłówek w pliku web.config, jeśli aplikacja jest hostowana przez Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Dodawanie nagłówka za pomocą globalnej aplikacji \_ BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementowanie niestandardowego modułu HTTP 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Wymagany nagłówek można włączyć tylko dla określonych stron, dodając go do poszczególnych odpowiedzi: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Ograniczanie lub wyłączanie rozpoznawania jednostek XML

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Rozszerzanie jednostek XML](https://capec.mitre.org/data/definitions/197.html), [ataki typu "odmowa usługi" i Obrona](/archive/msdn-magazine/2009/november/xml-denial-of-service-attacks-and-defenses), [Omówienie zabezpieczeń programu MSXML](/previous-versions/windows/desktop/ms754611(v=vs.85)), [najlepsze rozwiązania dotyczące zabezpieczania kodu MSXML](/previous-versions/windows/desktop/ms759188(v=vs.85)), [odwołania do protokołu NSXMLParserDelegate](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [rozpoznawanie odwołań zewnętrznych](/dotnet/standard/data/xml/resolving-external-resources) |
| **Kroki**| <p>Chociaż nie jest on szeroko używany, istnieje funkcja XML, która umożliwia analizatorowi XML rozszerzanie jednostek makr o wartości zdefiniowane w obrębie samego dokumentu lub ze źródeł zewnętrznych. Na przykład dokument może definiować jednostkę "NazwaFirmy" z wartością "Microsoft", tak aby w przypadku każdego tekstu " &companyname; " pojawił się w dokumencie, jest on automatycznie zastępowany tekstem firmy Microsoft. Lub dokument może zdefiniować jednostkę "MSFTStock", która odwołuje się do zewnętrznej usługi sieci Web, aby pobrać bieżącą wartość firmy Microsoft.</p><p>W każdym momencie " &MSFTStock; " pojawia się w dokumencie, jest automatycznie zastępowana bieżącą ceną zapasową. Jednak ta funkcja może być nadużywana do tworzenia warunków odmowy usługi (DoS). Osoba atakująca może zagnieździć wiele jednostek, aby utworzyć dekompresyjnej XML rozszerzający, który zużywa całą dostępną pamięć w systemie. </p><p>Alternatywnie może utworzyć odwołanie zewnętrzne, które przesyła strumieniowo nieskończoną ilość danych lub po prostu zawiesza wątek. W związku z tym wszystkie zespoły muszą całkowicie wyłączyć wewnętrzne i/lub zewnętrzne rozwiązanie do rozpoznawania jednostek XML, jeśli ich aplikacja nie używa tej funkcji, lub ręcznie ograniczyć ilość pamięci i czas, jaką aplikacja może wykorzystać do rozpoznawania jednostek, jeśli ta funkcja jest absolutnie niezbędna. Jeśli aplikacja nie wymaga rozpoznawania jednostek, wyłącz ją. </p>|

### <a name="example"></a>Przykład
W przypadku kodu .NET Framework można użyć następujących metod:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Należy zauważyć, że wartość domyślna `ProhibitDtd` w parametrze in `XmlReaderSettings` ma wartość true, ale w `XmlTextReader` jej wartość jest równa false. Jeśli używasz tabela XmlReaderSettings, nie musisz jawnie ustawiać opcji ProhibitDtd na true, ale jest to zalecane w celu zapewnienia bezpieczeństwa. Należy również zauważyć, że Klasa XmlDocument domyślnie zezwala na rozpoznawanie jednostek. 

### <a name="example"></a>Przykład
Aby wyłączyć rozpoznawanie jednostek dla obiektów XmlDocuments, użyj `XmlDocument.Load(XmlReader)` przeciążenia metody Load i ustaw odpowiednie właściwości w argumencie XmlReader, aby wyłączyć rozpoznawanie, jak pokazano w poniższym kodzie: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Przykład
Jeśli wyłączenie rozpoznawania jednostek nie jest możliwe dla aplikacji, należy ustawić właściwość tabela XmlReaderSettings. MaxCharactersFromEntities na rozsądną wartość zgodnie z potrzebami aplikacji. Spowoduje to ograniczenie wpływu potencjalnych ataków na rozwinięcie wykładnicze. Poniższy kod zawiera przykład tego podejścia: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Przykład
Jeśli musisz rozpoznać jednostki wbudowane, ale nie musisz rozpoznawać jednostek zewnętrznych, ustaw właściwość XmlReaderSettings.Xmlresolver na wartość null. Przykład: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Należy pamiętać, że w programie MSXML6 ProhibitDTD jest ustawiona na true (domyślnie wyłączenie przetwarzania DTD). W przypadku kodu Apple OSX/iOS istnieją dwa parsery XML, których można użyć: NSXMLParser i libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Aplikacje wykorzystujące http.sys wykonywanie weryfikacji niezgodności adresów URL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Wszystkie aplikacje używające http.sys powinny przestrzegać następujących wytycznych:</p><ul><li>Ogranicz długość adresu URL do nie więcej niż 16 384 znaków (ASCII lub Unicode). Jest to bezwzględna maksymalna długość adresu URL oparta na domyślnym ustawieniu Internet Information Services (IIS) 6. Witryny sieci Web powinny dążyć do krótszej długości niż to możliwe</li><li>Użyj standardowych klas operacji we/wy plików .NET Framework (takich jak FileStream), ponieważ będą one korzystać z reguł kanonizacji w programie .NET FX</li><li>Jawnie Kompiluj listę dozwolonych nazw znanych plików</li><li>Jawnie Odrzuć znane pliki, nie będzie można odrzucać programu UrlScan: exe, bat, cmd, com, htw, IDA, IDQ, HTR, IDC, SHTM [l], STM, Printer, ini, pol, dat</li><li>Przechwyć następujące wyjątki:<ul><li>System. ArgumentException (dla nazw urządzeń)</li><li>System. NotSupportedException (dla strumieni danych)</li><li>System. IO. FileNotFoundException (dla nieprawidłowych nazw plików)</li><li>System. IO. DirectoryNotFoundException (dla nieprawidłowego ucieczki katalogów)</li></ul></li><li>*Nie należy* wywoływać interfejsów API we/wy plików Win32. Na nieprawidłowym adresie URL bezpiecznie zwracają błąd 400 do użytkownika i Rejestruj rzeczywistą wartość błędu.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Przekazywanie plików bez ograniczeń](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), [tabela sygnatur plików](https://www.garykessler.net/library/file_sigs.html) |
| **Kroki** | <p>Przekazane pliki stanowią znaczące zagrożenie dla aplikacji.</p><p>Pierwszym krokiem w wielu atakach jest uzyskanie pewnego kodu do systemu. Następnie osoba atakująca musi znaleźć sposób, aby uzyskać kod wykonywany. Użycie przekazywania plików pomaga osobie atakującej wykonać pierwszy krok. Konsekwencje nieograniczonego przekazywania plików mogą się różnić, łącznie z ukończeniem przejęcia systemu, przeciążonym systemem plików lub bazą danych, przekazywaniem ataków do systemów zaplecza i prostą.</p><p>Jest to zależne od tego, co aplikacja zawiera przekazany plik, a w szczególności w miejscu, w którym jest przechowywany. Brak walidacji plików po stronie serwera. Do funkcji przekazywania plików należy zaimplementować następujące kontrolki zabezpieczeń:</p><ul><li>Sprawdzanie rozszerzenia pliku (akceptowany jest tylko prawidłowy zestaw dozwolonych typów plików)</li><li>Maksymalny limit rozmiaru pliku</li><li>Nie należy przekazywać pliku do programu Webroot; Lokalizacja powinna być katalogiem na dysku innym niż system.</li><li>Należy postępować zgodnie z konwencją nazewnictwa, w taki sposób, że nazwa przekazanego pliku ma pewną losowość, więc aby zapobiec nadpisaniu plików</li><li>Pliki powinny być skanowane w poszukiwaniu oprogramowania antywirusowego przed zapisaniem na dysku</li><li>Upewnij się, że nazwa pliku i wszelkie inne metadane (np. ścieżka pliku) są weryfikowane pod kątem złośliwych znaków</li><li>Należy sprawdzić podpis formatu pliku, aby uniemożliwić użytkownikowi przekazanie zamaskowanego pliku (np. przekazanie pliku exe przez zmianę rozszerzenia do txt)</li></ul>| 

### <a name="example"></a>Przykład
Ostatni punkt dotyczący weryfikacji podpisu w formacie pliku znajduje się w poniższej klasie, aby uzyskać szczegółowe informacje: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Upewnij się, że parametry bezpieczne dla typu są używane w aplikacji sieci Web na potrzeby dostępu do danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Jeśli używasz kolekcji Parameters, SQL traktuje dane wejściowe jako wartość literału, a nie jako kod wykonywalny. Kolekcja Parameters może służyć do wymuszania ograniczeń typu i długości danych wejściowych. Wartości spoza zakresu wyzwalają wyjątek. Jeśli parametry SQL z bezpiecznym typem nie są używane, osoby atakujące mogą mieć możliwość wykonywania ataków iniekcji osadzonych w niefiltrowanych danych wejściowych.</p><p>Używaj parametrów typu Safe podczas konstruowania zapytań SQL, aby uniknąć możliwych ataków iniekcji kodu SQL, które mogą wystąpić w przypadku niefiltrowanych danych wejściowych. Można użyć parametrów typu Safe z procedurami składowanymi i dynamicznych instrukcji SQL. Parametry są traktowane jako wartości literału przez bazę danych, a nie jako kod wykonywalny. Parametry są również sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład 
Poniższy kod przedstawia sposób używania parametrów bezpiecznego typu z SqlParameterCollection podczas wywoływania procedury składowanej. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli dane nie są zgodne z typem lub długością zdefiniowaną przez parametr, Klasa SqlParameter zgłasza wyjątek. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Użyj oddzielnych klas powiązania modelu lub listy filtrów powiązań, aby zapobiec usterce przypisywania grupowego MVC

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Atrybuty metadanych](/dotnet/api/system.componentmodel.dataannotations.metadatatypeattribute), [luki w zabezpieczeniach klucza publicznego i łagodzenie](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [pełny Przewodnik po przypisaniu do grup w ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [wprowadzenie z EF przy użyciu MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroki** | <ul><li>**Kiedy należy szukać luk w zabezpieczeniach?-** Luki w zabezpieczeniach wykorzystujących przede wszystkim mogą wystąpić w dowolnym miejscu klasy modelu z danych wejściowych użytkownika. Struktury, takie jak MVC, mogą reprezentować dane użytkowników w niestandardowych klasach platformy .NET, w tym zwykłych obiektów CLR (POCOs). MVC automatycznie wypełnia te klasy modelu danymi z żądania, zapewniając wygodną reprezentację do celów związanych z danymi wejściowymi użytkownika. Gdy klasy te zawierają właściwości, które nie powinny być ustawiane przez użytkownika, aplikacja może być narażona na ataki wykorzystujące nadmierne Księgowanie, które umożliwiają kontrolę nad danymi, które nigdy nie są przeznaczone dla aplikacji. Podobnie jak powiązanie modelu MVC, technologie dostępu do baz danych, takie jak mapowania obiektów i relacyjnych, takie jak Entity Framework często również obsługują używanie obiektów POCO do reprezentowania danych bazy danych. Te klasy modelu danych zapewniają taką samą wygodę w zakresie pracy z danymi bazy danych, jak MVC w przypadku danych wejściowych użytkownika. Ponieważ zarówno MVC, jak i baza danych obsługują podobne modele, takie jak obiekty POCO, łatwo jest ponownie używać tych samych klas w obu celach. W tej sytuacji nie można zachować separacji obaw i jest to jeden typowy obszar, w którym niezamierzone właściwości są ujawniane w ramach powiązania modelu, umożliwiając ataki z wykorzystaniem nadmiernego księgowania.</li><li>**Dlaczego nie należy używać niefiltrowanych klas modelu bazy danych jako parametrów do akcji MVC?-** Ponieważ powiązanie modelu MVC spowoduje powiązanie elementów w tej klasie. Nawet jeśli dane nie są wyświetlane w widoku, złośliwy użytkownik może wysłać żądanie HTTP z uwzględnieniem tych danych, a MVC powiąże się z tym, że Klasa bazy danych jest kształtem danych, który powinien zostać zaakceptowany dla danych wejściowych użytkownika.</li><li>**Dlaczego warto zadbać o kształt używany do powiązania modelu?-** Użycie powiązania modelu ASP.NET MVC z zbyt szerokimi modelami uwidacznia aplikację w przypadku ataków z nadmiernym zaksięgowaniem. Użycie nadwyżkowe w księgowaniu umożliwia osobom atakującym zmianę danych aplikacji poza zamierzonym deweloperem, na przykład zastępowanie cen dla elementu lub uprawnień zabezpieczeń dla konta. Aplikacje powinny używać modeli powiązań specyficznych dla akcji (lub określonych dozwolonych list filtrów właściwości) w celu zapewnienia jawnego kontraktu dla niezaufanych danych wejściowych, które mają być dozwolone za pośrednictwem powiązania modelu.</li><li>**Czy istnieją oddzielne modele powiązań tylko duplikowanie kodu?-** Nie. jest to kwestia oddzielenia problemów. W przypadku ponownego użycia modeli baz danych w metodach akcji jest wymawiana Każda właściwość (lub podwłaściwość) w tej klasie może być ustawiana przez użytkownika w żądaniu HTTP. Jeśli nie jest to, co chcesz zrobić z MVC, potrzebujesz listy filtrów lub oddzielnego kształtu klasy do wyświetlania MVC, jakie dane mogą pochodzić z danych wejściowych użytkownika.</li><li>**Czy jeśli mam oddzielne modele powiązań dla danych wejściowych użytkownika, czy muszę duplikować wszystkie atrybuty mojej adnotacji z danymi?-** Niekoniecznie. Można użyć klasy MetadataAttribute w klasie modelu bazy danych do łączenia metadanych z klasą powiązań modelu. Należy zauważyć, że typ, do którego odwołuje się obiekt sqldatatypeattribute, musi być podzbiorem typu odwołania (może mieć mniej właściwości, ale nie więcej).</li><li>**Przeniesienie danych między modelami wejściowymi użytkownika i modelami baz danych to żmudnym. Czy mogę po prostu skopiować wszystkie właściwości przy użyciu odbicia? -** Tak. Jedyne właściwości, które pojawiają się w modelach powiązań, to te, które zostały uznane za bezpieczne dla danych wejściowych użytkownika. Nie istnieje powód zabezpieczeń, który uniemożliwia używanie odbicia do kopiowania wszystkich właściwości, które istnieją we wspólnych między tymi dwoma modelami.</li><li>**Co około [bind (exclude = "â &euro; ¦")]. Czy mogę użyć tego, czy nie ma oddzielnych modeli powiązań? —** Ta metoda nie jest zalecana. Użycie [bind (exclude = "â &euro; ¦")] oznacza, że każda nowa właściwość jest domyślnie powiązana. Po dodaniu nowej właściwości istnieje dodatkowy krok, który należy pamiętać, aby zachować bezpieczeństwo, a nie aby projekt był zabezpieczony domyślnie. W zależności od tego, czy ta lista jest sprawdzana przez dewelopera za każdym razem, gdy właściwość jest dodawana jest ryzykowna.</li><li>**Is [bind (include = "â &euro; ¦")] przydatne do edycji operacji?-** No. [Bind (include = "â &euro; ¦")] jest odpowiednia tylko dla operacji wstawiania w stylu (Dodawanie nowych danych). W przypadku operacji aktualizowania w stylu (korygowania istniejących danych) Użyj innego podejścia, takiego jak posiadanie oddzielnych modeli powiązań lub przekazanie jawnej listy dozwolonych właściwości do UpdateModel lub TryUpdateModel. Dodanie atrybutu [bind (include = "â &euro; ¦")] w operacji edycji oznacza, że MVC utworzy wystąpienie obiektu i ustawi tylko wymienione właściwości, pozostawiając wszystkie pozostałe z wartościami domyślnymi. Gdy dane zostaną utrwalone, całkowicie zastąpią istniejącą jednostkę, resetuje wartości dla wszystkich pominiętych właściwości do ich wartości domyślnych. Na przykład jeśli właściwość IsAdmin została pominięta z atrybutu [bind (include = "â &euro; ¦")] w operacji edycji, każdy użytkownik, którego nazwa była edytowana za pośrednictwem tej akcji, zostanie zresetowany do wartości IsAdmin = false (każdy zmodyfikowany użytkownik utraci stan administratora). Jeśli chcesz uniemożliwić aktualizacje niektórych właściwości, użyj jednego z innych podejścia powyżej. Należy zauważyć, że niektóre wersje narzędzi MVC generują klasy kontrolerów z [bind (include = "â &euro; ¦")] w ramach edycji akcji i implikują, że usunięcie właściwości z tej listy uniemożliwi wylogowanie. Jednakże, jak opisano powyżej, takie podejście nie działa zgodnie z oczekiwaniami i zamiast tego zresetuje wszystkie dane we właściwościach pominiętych do ich wartości domyślnych.</li><li>**W przypadku operacji tworzenia czy istnieją jakieś ograniczenia przy użyciu [bind (include = "â &euro; ¦")], a nie oddzielnych modeli powiązań?-** tak. Najpierw to podejście nie działa w przypadku scenariuszy edycji, co wymaga utrzymania dwóch oddzielnych podejścia do ograniczenia wszystkich luk w zabezpieczeniach. Po drugie, oddzielne modele powiązań wymuszają rozdzielenie problemów między kształtem używanym do wprowadzania danych przez użytkownika a kształtem używanym do trwałości. coś [bind (include = "â &euro; ¦")] nie robi. Po trzecie należy zauważyć, że [bind (include = "â &euro; ¦")] może obsługiwać tylko właściwości najwyższego poziomu. nie można zezwalać tylko na części właściwości podrzędnych (takich jak "Details.Name") w atrybucie. Na koniec, a najprawdopodobniej jest to najważniejsze, za pomocą polecenia [bind (include = "â &euro; ¦")] dodaje dodatkowy krok, który musi być zapamiętany za każdym razem, gdy Klasa jest używana do powiązania modelu. Jeśli nowa metoda akcji wiąże się bezpośrednio z klasą danych i zapomni, aby zawierała atrybut [bind (include = " &euro; )]", może być narażony na ataki wykorzystujące nadmierne Księgowanie, więc użycie metody [bind (include = "â &euro; ¦")] jest nieco mniej bezpieczne. Jeśli używasz [bind (include = "â &euro; ¦")], pamiętaj o tym, aby określić go za każdym razem, gdy klasy danych są wyświetlane jako parametry metody akcji.</li><li>**Co należy zrobić w przypadku operacji tworzenia, co obejmuje atrybut [bind (include = "â &euro; ¦")] w samej klasie modelu? Czy to podejście nie pozwala uniknąć konieczności umieszczania atrybutu na każdej metodzie działania? —** To podejście działa w niektórych przypadkach. Przy użyciu [bind (include = "â &euro; ¦")] na samym typie modelu (a nie w parametrach akcji przy użyciu tej klasy), nie trzeba pamiętać, aby w każdej metodzie akcji uwzględnić atrybut [bind (include = "â &euro; ¦")]. Użycie atrybutu bezpośrednio w klasie efektywnie tworzy osobny obszar powierzchni tej klasy na potrzeby powiązania modeli. Jednak takie podejście zezwala tylko na jeden kształt powiązania modelu na klasę modelu. Jeśli jedna metoda akcji wymaga zezwolenia na powiązanie modelu pola (na przykład akcja tylko administrator, która aktualizuje role użytkowników), a inne akcje muszą uniemożliwiać powiązanie modelu z tym polem, ta metoda nie będzie działała. Każda klasa może mieć tylko jeden kształt powiązania modelu; Jeśli różne akcje wymagają różnych kształtów powiązania modelu, muszą one reprezentować te odrębne kształty przy użyciu oddzielnych klas powiązania modelu lub oddzielnych atrybutów [bind (include = "â &euro; ¦")] w metodach akcji.</li><li>**Co to są modele powiązań? Czy są one takie same jak modele widoku? —** Te dwie powiązane koncepcje. Termin model powiązania odwołuje się do klasy modelu używanej na liście parametrów akcji (kształt przeszedł z powiązania modelu MVC do metody Action). Model widoku terminu odwołuje się do klasy modelu przekazaną z metody akcji do widoku. Użycie modelu specyficznego dla widoku jest powszechnym podejściem do przekazywania danych z metody akcji do widoku. Często ten kształt jest również odpowiedni dla powiązania modelu, a model widoku terminu może służyć do odwoływania się do tego samego modelu, który jest używany w obu miejscach. Aby była precyzyjna, ta procedura zawiera szczegółowe informacje na temat modeli powiązań, skoncentrowanych na kształcie przekazanym do działania, który jest celem przypisywania grupowego.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Koduj niezaufane dane wyjściowe sieci Web przed renderowaniem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, formularze sieci Web, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Jak zapobiegać skryptom między lokacjami w programie ASP.NET](/previous-versions/msp-n-p/ff649310(v=pandp.10)), [skrypty międzywitrynowe](https://cwe.mitre.org/data/definitions/79.html), [zapobieganie atakom](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) między lokacjami i Ściągawka arkusz |
| **Kroki** | Skrypty między lokacjami (powszechnie skracane jako XSS) to wektor ataków dla Usługi online lub dowolnej aplikacji/składnika, który wykorzystuje dane wejściowe z sieci Web. Luki w zabezpieczeniach XSS mogą umożliwić atakującemu wykonanie skryptu na komputerze innego użytkownika za pośrednictwem zagrożonej aplikacji sieci Web. Złośliwe skrypty mogą służyć do kradzieży plików cookie i w inny sposób manipulowania komputerem ofiary za pomocą języka JavaScript. Ataki XSS są blokowane przez zweryfikowanie danych wprowadzonych przez użytkownika, upewniając się, że są poprawnie sformułowane i kodowane, zanim zostaną renderowane na stronie sieci Web. Sprawdzanie poprawności danych wejściowych i Kodowanie wyjściowe można wykonać przy użyciu biblioteki ochrony sieci Web. W przypadku kodu zarządzanego (C \# , VB.net itp.) należy użyć co najmniej jednej odpowiedniej metody kodowania z biblioteki ochrony sieci Web (Anti-XSS) w zależności od kontekstu, w którym dane wejściowe użytkownika są obsługiwane:| 

### <a name="example"></a>Przykład

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Wykonaj walidację danych wejściowych i filtrowanie dla wszystkich właściwości modelu typu String

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Dodawanie walidacji](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [Weryfikowanie danych modelu w aplikacji MVC](/previous-versions/dd410404(v=vs.90)), [reguły identyfikatorów GUID dla aplikacji ASP.NET MVC](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Kroki** | <p>Wszystkie parametry wejściowe muszą zostać sprawdzone przed użyciem ich w aplikacji, aby upewnić się, że aplikacja jest chroniona przed złośliwymi danymi wejściowymi użytkownika. Sprawdź poprawność wartości wejściowych przy użyciu walidacji wyrażeń regularnych po stronie serwera z dozwoloną strategią walidacji listy. Nieoczyszczone dane wejściowe użytkownika/parametry przesłane do metod mogą spowodować luki w zabezpieczeniach iniekcji kodu.</p><p>W przypadku aplikacji sieci Web punkty wejścia mogą również zawierać pola formularza, QueryString, cookies, nagłówki HTTP i parametry usługi sieci Web.</p><p>Następujące sprawdzenia poprawności danych wejściowych należy wykonać po powiązaniu modelu:</p><ul><li>Do właściwości modelu należy dodać adnotację ze RegularExpressioną, aby akceptować dozwolone znaki i maksymalną dopuszczalną długość</li><li>Metody kontrolera powinny wykonywać ModelStateą ważność</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Narzędzie Oczyszczanie powinno być stosowane w polach formularza akceptujących wszystkie znaki, np. Edytor tekstu sformatowanego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Koduj niebezpieczne dane wejściowe](/previous-versions/msp-n-p/ff647397(v=pandp.10)#paght000003_step3), [Sanitizer HTML](https://github.com/mganss/HtmlSanitizer) |
| **Kroki** | <p>Zidentyfikuj wszystkie znaczniki statycznej znaczników, których chcesz użyć. Typowym celem jest ograniczenie formatowania do bezpiecznych elementów HTML, takich jak `<b>` (pogrubienie) i `<i>` (kursywa).</p><p>Przed zapisaniem danych kodowanie HTML. Dzięki temu złośliwe skrypty są bezpieczne, powodując, że są obsługiwane jako tekst, a nie jako kod wykonywalny.</p><ol><li>Wyłącz weryfikację żądania ASP.NET przez dodanie atrybutu ValidateRequest = "false" do \@ dyrektywy Page</li><li>Koduj dane wejściowe ciągu za pomocą metody HtmlEncode</li><li>Użyj elementu StringBuilder i Wywołaj jego metodę Replace, aby selektywnie usunąć kodowanie elementów HTML, które mają być dozwolone.</li></ol><p>Strona w odwołaniach powoduje wyłączenie walidacji żądania ASP.NET przez ustawienie `ValidateRequest="false"` . HTML — koduje dane wejściowe i wybiórczo zezwala na `<b>` i `<i>` Alternatywnie, można również użyć biblioteki .NET do oczyszczania html.</p><p>HtmlSanitizer to biblioteka .NET do czyszczenia fragmentów HTML i dokumentów z konstrukcji, które mogą prowadzić do ataków typu XSS. Używa AngleSharp do analizowania, manipulowania i renderowania kodu HTML i CSS. HtmlSanitizer można zainstalować jako pakiet NuGet, a dane wejściowe użytkownika mogą być przesyłane za pomocą odpowiednich metod oczyszczania HTML lub CSS, jak ma to zastosowanie, po stronie serwera. Należy pamiętać, że oczyszczanie jako kontroli zabezpieczeń należy traktować tylko jako ostatnią opcję.</p><p>Sprawdzanie poprawności danych wejściowych i kodowania danych wyjściowych jest traktowane jako lepsza kontrola zabezpieczeń.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Nie przypisuj elementów DOM do ujścia, które nie mają wbudowanego kodowania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | W wielu funkcjach JavaScript domyślnie nie jest używane kodowanie. W przypadku przypisywania niezaufanych danych wejściowych do elementów DOM za pośrednictwem takich funkcji może wystąpić wykonanie skryptu między lokacjami (XSS).| 

### <a name="example"></a>Przykład
Poniżej przedstawiono niebezpieczne przykłady: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nie używaj `innerHtml` ; zamiast tego użyj `innerText` . Podobnie zamiast `$("#elm").html()` , użyj `$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Sprawdź poprawność wszystkich przekierowań w aplikacji, które są zamknięte lub bezpiecznie gotowe

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Struktura autoryzacji OAuth 2,0 — otwarte readresatory](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroki** | <p>Projektowanie aplikacji wymagającej przekierowania do lokalizacji dostarczonej przez użytkownika musi ograniczyć możliwość przekierowania na wstępnie zdefiniowaną "bezpieczną" listę witryn lub domen. Wszystkie przekierowania w aplikacji muszą być zamknięte/bezpieczne.</p><p>W tym celu:</p><ul><li>Identyfikuj wszystkie przekierowania</li><li>Zaimplementuj odpowiednie środki zaradcze dla każdego przekierowania. Odpowiednie środki zaradcze obejmują listę dozwolonych przekierowań lub potwierdzenie użytkownika. Jeśli witryna sieci Web lub usługa z luką typu "Open redirect" używa dostawców tożsamości usługi Facebook/OAuth/OpenID Connect, osoba atakująca może wykraść token logowania użytkownika i spersonifikować tego użytkownika. Jest to nieodłączne ryzyko w przypadku korzystania z protokołu OAuth, który jest opisany w dokumencie RFC 6749 "Struktura autoryzacji uwierzytelniania OAuth 2,0", sekcja 10,15 "otwarte przekierowania" — Podobnie poświadczenia użytkowników mogą zostać naruszone przez Spear ataki wyłudzaniane przy użyciu funkcji Otwórz przekierowania.</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementuj sprawdzanie poprawności danych wejściowych dla wszystkich parametrów typu String zaakceptowanych przez metody kontrolera

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Sprawdzanie poprawności danych modelu w aplikacji MVC](/previous-versions/dd410404(v=vs.90)), [reguły identyfikatorów guid dla aplikacji ASP.NET MVC](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Kroki** | W przypadku metod, które po prostu akceptują typ danych pierwotnych i nie są modelami jako argumentem, należy wykonać walidację danych wejściowych przy użyciu wyrażenia regularnego. Tutaj wyrażenie regularne. IsMatch powinno być używane z prawidłowym wzorcem wyrażenia regularnego. Jeśli dane wejściowe nie pasują do określonego wyrażenia regularnego, formant nie powinien kontynuować działania i należy wyświetlić odpowiednie ostrzeżenie dotyczące błędu walidacji.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Ustaw limit czasu górnego limitu dla przetwarzania wyrażenia regularnego, aby zapobiec występowaniu z powodu nieprawidłowych wyrażeń regularnych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, formularze sieci Web, MVC5, MVC6  |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Właściwość DefaultRegexMatchTimeout](/dotnet/api/system.web.configuration.httpruntimesection.defaultregexmatchtimeout) |
| **Kroki** | Aby zapewnić ataki typu "odmowa usługi" na nieprawidłowo utworzone wyrażenia regularne, które powodują wiele operacji wycofywania, należy ustawić domyślny limit czasu dla globalnego. Jeśli czas przetwarzania trwa dłużej niż zdefiniowany górny limit, zostałby zgłoszony wyjątek limitu czasu. Jeśli nic nie jest skonfigurowane, limit czasu byłby nieskończony.| 

### <a name="example"></a>Przykład
Na przykład następująca konfiguracja spowoduje zgłoszenie RegexMatchTimeoutException, jeśli przetwarzanie trwa ponad 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Unikaj używania języka HTML. Raw w widokach Razor

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Krok | ASP.NET strony sieci Web (Razor) wykonują automatyczne kodowanie HTML. Wszystkie ciągi wydrukowane przez osadzony kod Nuggets (@ Blocks) są automatycznie kodowane w formacie HTML. Jednak gdy `HtmlHelper.Raw` Metoda jest wywoływana, zwraca znacznik, który nie jest zakodowany w formacie HTML. Jeśli `Html.Raw()` używana jest metoda pomocnika, pomija automatyczną ochronę kodowania, którą zapewnia Razor.|

### <a name="example"></a>Przykład
Poniżej znajduje się niebezpieczny przykład: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Nie używaj `Html.Raw()` , chyba że potrzebujesz wyświetlania znaczników. Ta metoda nie wykonuje niejawnie kodowania danych wyjściowych. Użyj innych pomocników ASP.NET, np., `@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Nie używaj zapytań dynamicznych w procedurach składowanych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Atak polegający na wstrzyknięciu kodu SQL wykorzystuje luki w zabezpieczeniach sprawdzania poprawności danych wejściowych w celu uruchomienia dowolnych poleceń w bazie danych. Może wystąpić, gdy aplikacja używa danych wejściowych do konstruowania dynamicznych instrukcji SQL w celu uzyskania dostępu do bazy danych. Może również wystąpić, jeśli kod używa procedur składowanych, które są przenoszone ciągi, które zawierają nieprzetworzone dane wejściowe użytkownika. Za pomocą ataku iniekcja SQL, osoba atakująca może wykonywać dowolne polecenia w bazie danych. Wszystkie instrukcje SQL (w tym instrukcje SQL w procedurach składowanych) muszą mieć wartość sparametryzowane. Sparametryzowane instrukcje SQL będą akceptować znaki, które mają specjalne znaczenie dla SQL (takie jak pojedynczy cudzysłów) bez problemów, ponieważ są jednoznacznie wpisane. |

### <a name="example"></a>Przykład
Poniżej znajduje się przykład niezabezpieczonej dynamicznej procedury składowanej: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Przykład
Poniżej znajduje się ta sama procedura składowana zaimplementowana bezpiecznie: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Upewnij się, że Walidacja modelu odbywa się w metodach interfejsu API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Walidacja modelu w interfejsie API sieci Web ASP.NET](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Gdy klient wysyła dane do internetowego interfejsu API, przed wykonaniem jakiegokolwiek przetwarzania wymagane jest zweryfikowanie danych. W przypadku internetowych interfejsów API ASP.NET, które akceptują modele jako dane wejściowe, należy używać adnotacji danych w modelach do ustawiania reguł walidacji dla właściwości modelu.|

### <a name="example"></a>Przykład
Poniższy kod ilustruje takie samo: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Przykład
W metodzie działania kontrolerów interfejsu API ważność modelu musi być jawnie sprawdzona, jak pokazano poniżej: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementuj sprawdzanie poprawności danych wejściowych dla wszystkich parametrów typu String zaakceptowanych przez metody interfejsu API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, MVC 5, MVC 6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Sprawdzanie poprawności danych modelu w aplikacji MVC](/previous-versions/dd410404(v=vs.90)), [reguły identyfikatorów guid dla aplikacji ASP.NET MVC](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Kroki** | W przypadku metod, które po prostu akceptują typ danych pierwotnych i nie są modelami jako argumentem, należy wykonać walidację danych wejściowych przy użyciu wyrażenia regularnego. Tutaj wyrażenie regularne. IsMatch powinno być używane z prawidłowym wzorcem wyrażenia regularnego. Jeśli dane wejściowe nie pasują do określonego wyrażenia regularnego, formant nie powinien kontynuować działania i należy wyświetlić odpowiednie ostrzeżenie dotyczące błędu walidacji.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Upewnij się, że parametry bezpieczne dla typu są używane w interfejsie API sieci Web na potrzeby dostępu do danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Jeśli używasz kolekcji Parameters, SQL traktuje dane wejściowe jako wartość literału, a nie jako kod wykonywalny. Kolekcja Parameters może służyć do wymuszania ograniczeń typu i długości danych wejściowych. Wartości spoza zakresu wyzwalają wyjątek. Jeśli parametry SQL z bezpiecznym typem nie są używane, osoby atakujące mogą mieć możliwość wykonywania ataków iniekcji osadzonych w niefiltrowanych danych wejściowych.</p><p>Używaj parametrów typu Safe podczas konstruowania zapytań SQL, aby uniknąć możliwych ataków iniekcji kodu SQL, które mogą wystąpić w przypadku niefiltrowanych danych wejściowych. Można użyć parametrów typu Safe z procedurami składowanymi i dynamicznych instrukcji SQL. Parametry są traktowane jako wartości literału przez bazę danych, a nie jako kod wykonywalny. Parametry są również sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład
Poniższy kod przedstawia sposób używania parametrów bezpiecznego typu z SqlParameterCollection podczas wywoływania procedury składowanej. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli dane nie są zgodne z typem lub długością zdefiniowaną przez parametr, Klasa SqlParameter zgłasza wyjątek. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Użyj sparametryzowanych zapytań SQL dla Cosmos DB

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zapowiedź programu SQL parametryzacja w Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroki** | Chociaż Azure Cosmos DB obsługuje tylko zapytania tylko do odczytu, iniekcja SQL jest wciąż możliwa, jeśli zapytania są konstruowane przez połączenie z danymi wejściowymi użytkownika. Użytkownik może mieć możliwość uzyskania dostępu do danych, do których nie należy uzyskiwać dostępu w obrębie tej samej kolekcji, przez rozdanie złośliwych zapytań SQL. Użyj sparametryzowanych zapytań SQL, jeśli zapytania są konstruowane na podstawie danych wejściowych użytkownika. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>Walidacja danych wejściowych WCF za poorednictwem powiązania schematu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff647820(v=pandp.10)) |
| **Kroki** | <p>Brak walidacji prowadzi do różnych ataków iniekcji typu.</p><p>Weryfikacja komunikatów reprezentuje jedną linię obrony w ochronie aplikacji WCF. To podejście umożliwia Weryfikowanie komunikatów przy użyciu schematów w celu ochrony operacji usługi WCF przed atakami złośliwego klienta. Sprawdź poprawność wszystkich komunikatów odebranych przez klienta, aby chronić klienta przed atakiem przez złośliwą usługę. Sprawdzanie poprawności komunikatów umożliwia Weryfikowanie komunikatów, gdy operacje korzystają z kontraktów komunikatów lub kontraktów danych, których nie można wykonać za pomocą weryfikacji parametrów. Sprawdzanie poprawności komunikatów umożliwia tworzenie logiki walidacji w schematach, co zapewnia większą elastyczność i skrócenie czasu projektowania. Schematy mogą być ponownie używane w różnych aplikacjach w organizacji, tworząc standardy dla reprezentacji danych. Ponadto funkcja weryfikacji komunikatów umożliwia ochronę operacji w przypadku korzystania z bardziej złożonych typów danych obejmujących kontrakty reprezentujące logikę biznesową.</p><p>Aby przeprowadzić weryfikację wiadomości, należy najpierw zbudować schemat, który reprezentuje operacje usługi i typy danych używane przez te operacje. Następnie utworzysz klasę .NET, która implementuje niestandardowego inspektora komunikatów klienta i niestandardowego inspektora komunikatów dyspozytora, aby zweryfikować komunikaty wysyłane/odbierane do/z usługi. Następnie należy zaimplementować niestandardowe zachowanie punktu końcowego, aby umożliwić weryfikację komunikatów zarówno na kliencie, jak i w usłudze. Na koniec należy zaimplementować niestandardowy element konfiguracji w klasie, która umożliwia uwidocznienie rozszerzonego niestandardowego zachowania punktu końcowego w pliku konfiguracji usługi lub klienta ".</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF — walidacja danych wejściowych za pomocą inspektorów parametrów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff647875(v=pandp.10)) |
| **Kroki** | <p>Dane wejściowe i walidacja danych przedstawiają jedną ważną linię obrony w ochronie aplikacji WCF. Należy sprawdzić poprawność wszystkich parametrów uwidocznionych w operacjach usługi WCF, aby chronić usługę przed atakiem złośliwego klienta. Z drugiej strony należy również sprawdzić poprawność wszystkich zwracanych wartości odebranych przez klienta, aby chronić klienta przed atakiem przez złośliwą usługę</p><p>Funkcja WCF oferuje różne punkty rozszerzalności, które umożliwiają dostosowanie zachowania środowiska uruchomieniowego WCF przez tworzenie rozszerzeń niestandardowych. Inspektorzy komunikatów i inspektory parametrów to dwa mechanizmy rozszerzalności służące do uzyskania większej kontroli nad danymi przekazywanymi między klientem a usługą. Inspektorów parametrów należy używać do sprawdzania poprawności danych wejściowych i używania inspektorów komunikatów tylko wtedy, gdy trzeba sprawdzić cały komunikat przepływający do i z usługi.</p><p>Aby przeprowadzić walidację danych wejściowych, należy utworzyć klasę platformy .NET i zaimplementować niestandardowy Inspektor parametrów w celu sprawdzenia poprawności parametrów operacji w usłudze. Następnie zostanie zaimplementowane niestandardowe zachowanie punktu końcowego w celu włączenia walidacji zarówno na kliencie, jak i w usłudze. Na koniec zostanie wdrożony niestandardowy element konfiguracji w klasie, która umożliwia uwidocznienie rozszerzonego niestandardowego zachowania punktu końcowego w pliku konfiguracyjnym usługi lub kliencie programu.</p>|