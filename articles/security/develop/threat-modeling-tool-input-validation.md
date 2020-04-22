---
title: Sprawdzanie poprawności danych wejściowych — narzędzie microsoft do modelowania zagrożeń — Azure | Dokumenty firmy Microsoft
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
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
ms.openlocfilehash: 712a0707826f97f29b015a2c5892f8d20577e41b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687894"
---
# <a name="security-frame-input-validation--mitigations"></a>Ramka zabezpieczeń: Sprawdzanie poprawności danych wejściowych | Czynniki 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Aplikacja internetowa** | <ul><li>[Wyłączanie skryptów XSLT dla wszystkich przekształceń przy użyciu niezaufanych arkuszy stylów](#disable-xslt)</li><li>[Upewnij się, że każda strona, która może zawierać zawartość kontrolowaną przez użytkownika, rezygnuje z automatycznego wąchania MIME](#out-sniffing)</li><li>[Utwardzanie lub wyłączanie rozpoznawania jednostek XML](#xml-resolution)</li><li>[Aplikacje wykorzystujące http.sys przeprowadzają weryfikację kanonizacji adresów URL](#app-verification)</li><li>[Upewnij się, że podczas akceptowania plików od użytkowników obowiązują odpowiednie mechanizmy kontroli](#controls-users)</li><li>[Upewnij się, że parametry bezpieczne dla typu są używane w aplikacji sieci Web w celu uzyskania dostępu do danych](#typesafe)</li><li>[Użyj oddzielnych klas powiązań modelu lub list filtrów powiązania, aby zapobiec podatności na przydział masy MVC](#binding-mvc)</li><li>[Kodowanie niezaufanego wyjścia sieci Web przed renderowaniem](#rendering)</li><li>[Wykonywanie sprawdzania poprawności danych wejściowych i filtrowania we wszystkich właściwościach modelu typu ciągu](#typemodel)</li><li>[Sanityzacji należy stosować w polach formularza, które akceptują wszystkie znaki, np.](#richtext)</li><li>[Nie przypisywać elementów DOM do zlewów, które nie mają wbudowanego kodowania](#inbuilt-encode)</li><li>[Sprawdź poprawność wszystkich przekierowań w aplikacji są zamknięte lub wykonane bezpiecznie](#redirect-safe)</li><li>[Implementowanie sprawdzania poprawności danych wejściowych dla wszystkich parametrów typu ciągu akceptowanych przez metody kontrolera](#string-method)</li><li>[Ustaw górny limit limitu czasu przetwarzania wyrażeń regularnych, aby zapobiec dos z powodu złych wyrażeń regularnych](#dos-expression)</li><li>[Unikaj używania html.raw w widokach Razor](#html-razor)</li></ul> | 
| **baza danych** | <ul><li>[Nie używaj zapytań dynamicznych w procedurach przechowywanych](#stored-proc)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że sprawdzanie poprawności modelu odbywa się w metodach interfejsu API sieci Web](#validation-api)</li><li>[Implementowanie sprawdzania poprawności danych wejściowych dla wszystkich parametrów typu ciągu akceptowanych przez metody interfejsu API sieci Web](#string-api)</li><li>[Upewnij się, że parametry bezpieczne dla typu są używane w interfejsie API sieci Web w celu uzyskania dostępu do danych](#typesafe-api)</li></ul> | 
| **Baza danych dokumentów platformy Azure** | <ul><li>[Używanie sparametryzowanych zapytań SQL dla usługi Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Sprawdzanie poprawności danych wejściowych WCF za pomocą powiązania schematu](#schema-binding)</li><li>[WCF- Sprawdzanie poprawności danych wejściowych za pośrednictwem inspektorów parametrów](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Wyłączanie skryptów XSLT dla wszystkich przekształceń przy użyciu niezaufanych arkuszy stylów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [XSLT Security](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [Właściwość XsltSettings.EnableScript](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Kroki** | XSLT obsługuje skrypty wewnątrz `<msxml:script>` arkuszy stylów przy użyciu elementu. Dzięki temu funkcje niestandardowe mają być używane w transformacji XSLT. Skrypt jest wykonywany w kontekście procesu wykonywania transformacji. Skrypt XSLT musi być wyłączony, gdy w niezaufanym środowisku, aby zapobiec wykonywaniu niezaufanego kodu. *W przypadku korzystania z platformy .NET:* Skrypty XSLT są domyślnie wyłączone; jednak należy upewnić się, że nie została `XsltSettings.EnableScript` jawnie włączona za pośrednictwem właściwości.|

### <a name="example"></a>Przykład 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
Jeśli używasz msxml 6.0, skrypty XSLT jest domyślnie wyłączone; należy jednak upewnić się, że nie została ona jawnie włączona za pośrednictwem właściwości obiektu XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
Jeśli używasz MSXML 5 lub poniżej, skrypty XSLT jest domyślnie włączona i należy jawnie wyłączyć go. Ustaw właściwość obiektu XML DOM AllowXsltScript na wartość false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Upewnij się, że każda strona, która może zawierać zawartość kontrolowaną przez użytkownika, rezygnuje z automatycznego wąchania MIME

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [IE8 Security Part V - Kompleksowa ochrona](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Kroki** | <p>Dla każdej strony, która może zawierać zawartość kontrolowaną `X-Content-Type-Options:nosniff`przez użytkownika, należy użyć nagłówka HTTP . Aby spełnić to wymaganie, można ustawić wymaganą stronę nagłówka według strony tylko dla tych stron, które mogą zawierać zawartość kontrolowaną przez użytkownika, lub można ustawić ją globalnie dla wszystkich stron w aplikacji.</p><p>Każdy typ pliku dostarczanego z serwera sieci web ma skojarzony [typ MIME](https://en.wikipedia.org/wiki/Mime_type) (nazywany także *typem zawartości),* który opisuje charakter zawartości (czyli obraz, tekst, aplikację itp.)</p><p>Nagłówek X-Content-Type-Options jest nagłówkiem HTTP, który umożliwia deweloperom określenie, że ich zawartość nie powinna być wąchaniem MIME. Ten nagłówek jest przeznaczony do łagodzenia ataków MIME-Sniffing. Obsługa tego nagłówka została dodana w programie Internet Explorer 8 (IE8)</p><p>Tylko użytkownicy programu Internet Explorer 8 (IE8) będą korzystać z opcji typu zawartości X. Poprzednie wersje programu Internet Explorer nie są obecnie zgodne z nagłówkiem X-Content-Type-Options</p><p>Internet Explorer 8 (i nowsze) są jedynymi głównymi przeglądarkami, które zaimplementują funkcję rezygnacji mime-sniffing. Jeśli inne główne przeglądarki (Firefox, Safari, Chrome) zaimplementują podobne funkcje, to zalecenie zostanie zaktualizowane w celu uwzględnienia składni dla tych przeglądarek, jak również</p>|

### <a name="example"></a>Przykład
Aby włączyć wymagany nagłówek globalnie dla wszystkich stron w aplikacji, można wykonać jedną z następujących czynności: 

* Dodaj nagłówek w pliku web.config, jeśli aplikacja jest obsługiwana przez internetowe usługi informacyjne (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Dodawanie nagłówka za\_pośrednictwem globalnego application BeginRequest 

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

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Utwardzanie lub wyłączanie rozpoznawania jednostek XML

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Rozszerzenie jednostki XML,](https://capec.mitre.org/data/definitions/197.html) [ataki i zabezpieczenia typu "odmowa usługi" XML,](https://msdn.microsoft.com/magazine/ee335713.aspx) [omówienie zabezpieczeń MSXML,](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx) [najważniejsze wskazówki dotyczące zabezpieczania kodu MSXML,](https://msdn.microsoft.com/library/ms759188(VS.85).aspx) [odwołanie do protokołu NSXMLParserDelegate](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [rozpoznawanie odwołań zewnętrznych](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Kroki**| <p>Chociaż nie jest powszechnie używany, istnieje funkcja XML, która umożliwia analizatorowi XML rozwijanie jednostek makr z wartościami zdefiniowanymi w samym dokumencie lub ze źródeł zewnętrznych. Na przykład dokument może zdefiniować jednostkę "companyname" z wartością "Microsoft", tak aby za każdym razem, gdy tekst " "&companyname;pojawia się w dokumencie, został automatycznie zastąpiony tekstem Microsoft. Lub dokument może zdefiniować jednostkę "MSFTStock", która odwołuje się do zewnętrznej usługi sieci web, aby pobrać bieżącą wartość akcji firmy Microsoft.</p><p>Następnie w&MSFTStock;dowolnym momencie " " pojawia się w dokumencie, jest automatycznie zastępowany aktualną ceną akcji. Jednak ta funkcja może być nadużywane do tworzenia warunków odmowa usługi (DoS). Osoba atakująca może zagnieździć wiele jednostek, aby utworzyć wykładniczą bombę XML rozszerzenia, która zużywa całą dostępną pamięć w systemie. </p><p>Alternatywnie może utworzyć odwołanie zewnętrzne, które strumieniuje z powrotem nieskończoną ilość danych lub po prostu zawiesza wątku. W rezultacie wszystkie zespoły muszą całkowicie wyłączyć wewnętrzne i/lub zewnętrzne rozpoznawanie jednostek XML, jeśli ich aplikacja jej nie używa, lub ręcznie ograniczyć ilość pamięci i czas, który aplikacja może wykorzystać do rozpoznawania jednostek, jeśli ta funkcja jest absolutnie konieczna. Jeśli rozpoznawanie jednostek nie jest wymagane przez aplikację, a następnie wyłączyć go. </p>|

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
Należy zauważyć, że `ProhibitDtd` `XmlReaderSettings` wartość domyślna `XmlTextReader` w jest true, ale w nim jest false. Jeśli używasz XmlReaderSettings, nie trzeba ustawić ProhibitDtd true jawnie, ale jest zalecane ze względu na bezpieczeństwo, że to zrobić. Należy również zauważyć, że XmlDocument klasa umożliwia rozdzielczość jednostki domyślnie. 

### <a name="example"></a>Przykład
Aby wyłączyć rozdzielczość jednostki dla XmlDocuments, użyj `XmlDocument.Load(XmlReader)` przeciążenia Load metody i ustawić odpowiednie właściwości w xmlReader argument, aby wyłączyć rozdzielczość, jak pokazano w następującym kodzie: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Przykład
Jeśli wyłączenie rozpoznawania jednostek nie jest możliwe dla aplikacji, należy ustawić XmlReaderSettings.MaxCharactersFromEntities właściwość rozsądnej wartości zgodnie z potrzebami aplikacji. Ograniczy to wpływ potencjalnych wykładniczych ataków DoS ekspansji. Poniższy kod zawiera przykład tego podejścia: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Przykład
Jeśli trzeba rozpoznać jednostki wbudowane, ale nie trzeba rozpoznawać jednostek zewnętrznych, ustaw właściwość XmlReaderSettings.XmlResolver na wartość null. Przykład: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Należy zauważyć, że w MSXML6, ProhibitDTD jest ustawiona na true (wyłączenie przetwarzania DTD) domyślnie. W przypadku kodu Apple OSX/iOS dostępne są dwa analizatory XML: NSXMLParser i libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Aplikacje wykorzystujące http.sys przeprowadzają weryfikację kanonizacji adresów URL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | <p>Każda aplikacja korzystająca z http.sys powinna postępować zgodnie z tymi wytycznymi:</p><ul><li>Ogranicz długość adresu URL do nie więcej niż 16 384 znaków (ASCII lub Unicode). Jest to bezwzględna maksymalna długość adresu URL oparta na domyślnym ustawieniu usług internetowych (IIS) 6. Strony internetowe powinny dążyć do długości krótszej niż to, jeśli to możliwe</li><li>Użyj standardowych klas we/wy pliku .NET Framework (takich jak FileStream), ponieważ będą one korzystać z reguł kanonizacji w .NET FX</li><li>Jawnie tworzenie listy dozwolonych znanych nazwy plików</li><li>Jawnie odrzucić znane typy plików nie będzie służyć UrlScan odrzuca: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, drukarka, ini, pol, dat plików</li><li>Złap następujące wyjątki:<ul><li>System.ArgumentException (dla nazw urządzeń)</li><li>System.NotSupportedException (dla strumieni danych)</li><li>System.IO.FileNotFoundException (dla nieprawidłowych nazwy plików ominęły)</li><li>System.IO.DirectoryNotFoundException (dla nieprawidłowych identyfikatorów ekustaw)</li></ul></li><li>*Nie* wywoływaj interfejsów API we/wy pliku Win32. Na nieprawidłowy adres URL bezpiecznie zwrócić błąd 400 do użytkownika i rejestrować rzeczywisty błąd.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Upewnij się, że podczas akceptowania plików od użytkowników obowiązują odpowiednie mechanizmy kontroli

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Nieograniczone przekazywanie plików](https://www.owasp.org/index.php/Unrestricted_File_Upload), [Tabela podpisów plików](https://www.garykessler.net/library/file_sigs.html) |
| **Kroki** | <p>Przesłane pliki stanowią znaczne zagrożenie dla aplikacji.</p><p>Pierwszym krokiem w wielu ataków jest uzyskanie kodu do systemu, który ma zostać zaatakowany. Następnie atak musi tylko znaleźć sposób, aby uzyskać kod wykonany. Użycie przekazywania plików pomaga osobie atakującej wykonać pierwszy krok. Konsekwencje nieograniczonego przekazywania plików mogą się różnić, w tym całkowite przejęcie systemu, przeciążony system plików lub baza danych, przekazywanie ataków do systemów zaplecza i proste zniesławienie.</p><p>To zależy od tego, co aplikacja robi z przesłanym plikiem, a zwłaszcza gdzie jest przechowywany. Brak sprawdzania poprawności przekazywania plików po stronie serwera. Następujące zabezpieczenia powinny być zaimplementowane dla funkcji przekazywania plików:</p><ul><li>Sprawdzanie rozszerzenia pliku (akceptowany powinien być tylko prawidłowy zestaw dozwolonego typu pliku)</li><li>Maksymalny limit rozmiaru pliku</li><li>Plik nie powinien być przesyłany do webroot; lokalizacja powinna być katalogiem na dysku niesystemowym</li><li>Należy przestrzegać konwencji nazewnictwa, aby nazwa przesłana pliku miała pewną losowość, aby zapobiec zastępowaniu plików</li><li>Pliki powinny być skanowane w poszukiwaniu wirusa przed zapisaniem na dysku</li><li>Upewnij się, że nazwa pliku i inne metadane (np. ścieżka pliku) są sprawdzane pod kątem złośliwych znaków</li><li>Należy sprawdzić podpis formatu pliku, aby uniemożliwić użytkownikowi przesłanie pliku maskowanego (np. przesłanie pliku exe przez zmianę rozszerzenia na txt)</li></ul>| 

### <a name="example"></a>Przykład
Aby uzyskać ostatni punkt dotyczący sprawdzania poprawności podpisu formatu pliku, zapoznaj się z poniższą klasą, aby uzyskać szczegółowe informacje: 

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

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Upewnij się, że parametry bezpieczne dla typu są używane w aplikacji sieci Web w celu uzyskania dostępu do danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | <p>Jeśli używasz parametrów kolekcji, SQL traktuje dane wejściowe jest jako wartość literału, a następnie jako kod wykonywalny. Kolekcja Parameters może służyć do wymuszania ograniczeń typu i długości danych wejściowych. Wartości poza zakresem wyzwalają wyjątek. Jeśli parametry SQL bezpieczne dla typu nie są używane, osoby atakujące mogą być w stanie wykonać ataki iniekcji, które są osadzone w niefiltrowanych danych wejściowych.</p><p>Użyj parametrów typu bezpieczne podczas konstruowania zapytań SQL, aby uniknąć ewentualnych ataków iniekcji SQL, które mogą wystąpić przy niefiltrowanych danych wejściowych. Można użyć parametrów typu bezpieczne z procedurami przechowywanymi i z dynamicznymi instrukcjami SQL. Parametry są traktowane jako wartości literału przez bazę danych, a nie jako kod wykonywalny. Parametry są również sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład 
Poniższy kod pokazuje, jak używać parametrów typu bezpieczne z SqlParameterCollection podczas wywoływania procedury składowanej. 

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
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli dane nie są zgodne z typem lub długością zdefiniowaną przez parametr, klasa SqlParameter zgłasza wyjątek. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Użyj oddzielnych klas powiązań modelu lub list filtrów powiązania, aby zapobiec podatności na przydział masy MVC

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Atrybuty metadanych,](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute) [Luka w zabezpieczeniach klucza publicznego i łagodzenie,](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation) [Kompletny przewodnik po przypisaniu masy w ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Wprowadzenie do EF przy użyciu MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroki** | <ul><li>**Kiedy należy szukać luk w zabezpieczeniach dotyczących nadmiernego publikowania?** Luki w zabezpieczeniach nadmiernego publikowania mogą wystąpić w dowolnym miejscu, w które należy powiązać klasy modelu z danych wejściowych użytkownika. Struktury, takie jak MVC może reprezentować dane użytkownika w niestandardowych klas .NET, w tym zwykły stary CLR obiektów (POCO). MVC automatycznie wypełnia te klasy modelu danymi z żądania, zapewniając wygodną reprezentację do obsługi danych wejściowych użytkownika. Gdy te klasy zawierają właściwości, które nie powinny być ustawiane przez użytkownika, aplikacja może być narażony na ataki nadmiernego publikowania, które umożliwiają użytkownikowi kontrolę nad danymi, które aplikacja nigdy nie zamierzała. Podobnie jak powiązanie modelu MVC, technologie dostępu do bazy danych, takie jak obiekty/relacyjne mapery, takie jak Entity Framework, często obsługują również obiekty POCO do reprezentowania danych bazy danych. Te klasy modelu danych zapewniają taką samą wygodę w kontaktach z danymi bazy danych, jak MVC w kontaktach z danymi wejściowymi użytkownika. Ponieważ zarówno MVC, jak i bazy danych obsługują podobne modele, takie jak obiekty POCO, wydaje się łatwe do ponownego użycia tych samych klas do obu celów. Ta praktyka nie zachowuje separacji problemów i jest jednym z wspólnych obszarów, w którym niezamierzone właściwości są narażone na powiązanie modelu, umożliwiając nadmierne publikowanie ataków.</li><li>**Dlaczego nie powinienem używać moich niefiltrowanych klas modelu bazy danych jako parametrów do moich akcji MVC? -** Ponieważ powiązanie modelu MVC będzie wiązać wszystko w tej klasie. Nawet jeśli dane nie są wyświetlane w widoku, złośliwy użytkownik może wysłać żądanie HTTP z tymi danymi, a MVC chętnie powiąże go, ponieważ akcja mówi, że klasa bazy danych jest kształtem danych, które powinien zaakceptować dla danych wejściowych użytkownika.</li><li>**Dlaczego powinienem dbać o kształt używany do wiązania modelu?** Za pomocą ASP.NET wiązania modelu MVC z modeli zbyt szeroki udostępnia aplikacji do nadmiernego publikowania ataków. Nadmierne księgowanie może umożliwić atakującym zmianę danych aplikacji poza to, co deweloper zamierzał, takich jak zastąpienie ceny dla elementu lub uprawnień zabezpieczeń dla konta. Aplikacje powinny używać modeli wiązania specyficzne dla akcji (lub określonych list filtrów dozwolonych właściwości) w celu zapewnienia jawnej umowy dla niezaufanych danych wejściowych, aby umożliwić za pośrednictwem powiązania modelu.</li><li>**Czy posiadanie oddzielnych modeli wiązania po prostu duplikuje kod? -** Nie, jest to kwestia rozdzielenia obaw. Jeśli ponownie użyć modeli bazy danych w metodach akcji, mówisz, że każda właściwość (lub podwładna) w tej klasie może być ustawiona przez użytkownika w żądaniu HTTP. Jeśli nie jest to, co chcesz MVC zrobić, trzeba listy filtrów lub kształt klasy oddzielne, aby pokazać MVC, jakie dane mogą pochodzić z danych wejściowych użytkownika zamiast.</li><li>**Jeśli mam oddzielne modele wiązania dla wprowadzania danych przez użytkownika, czy muszę powielić wszystkie moje atrybuty adnotacji danych? -** Niekoniecznie. Można użyć MetadataTypeAttribute w klasie modelu bazy danych, aby połączyć się z metadanymi w klasie wiązania modelu. Wystarczy zauważyć, że typ, do którego odwołuje się Atrybut MetadataTypeAttribute musi być podzbiorem typu odwołującego się (może mieć mniej właściwości, ale nie więcej).</li><li>**Przenoszenie danych między modelami wprowadzania danych użytkownika i modeli baz danych jest nużące. Czy mogę po prostu skopiować wszystkie właściwości za pomocą odbicia? -** Tak. Tylko właściwości, które pojawiają się w modelach wiązania są te, które zostały określone jako bezpieczne dla danych wejściowych użytkownika. Nie ma powodu zabezpieczeń, który uniemożliwia użycie odbicia do kopiowania wszystkich właściwości, które istnieją wspólne między tymi dwoma modelami.</li><li>**Co z [Bind(Exclude ="â&euro;¦")]. Czy mogę używać tego zamiast oddzielnych modeli wiązania? -** Takie podejście nie jest zalecane. Użycie funkcji [Bind(Exclude&euro;="â ¦")] oznacza, że każda nowa właściwość jest domyślnie powiązana. Po dodaniu nowej właściwości, istnieje dodatkowy krok do zapamiętania, aby zachować rzeczy bezpieczne, a nie o projekt być bezpieczne domyślnie. W zależności od dewelopera sprawdzanie tej listy za każdym razem, gdy właściwość jest dodawany jest ryzykowne.</li><li>**Czy [Bind(Include ="â&euro;¦")] jest przydatne dla operacji edycji? -** №. [Bind(Include ="â&euro;¦")] nadaje się tylko do operacji w stylu INSERT (dodawanie nowych danych). W przypadku operacji w stylu UPDATE (poprawianie istniejących danych) należy użyć innego podejścia, takiego jak oddzielne modele wiązania lub przekazanie jawnej listy dozwolonych właściwości do UpdateModel lub TryUpdateModel. Dodanie atrybutu [Bind(Include&euro;="â ¦")] w operacji edycji oznacza, że MVC utworzy wystąpienie obiektu i ustawi tylko wymienione właściwości, pozostawiając wszystkie inne przy ich wartościach domyślnych. Gdy dane są zachowywane, całkowicie zastąpi istniejącą jednostkę, resetując wartości dla wszystkich pominiętych właściwości do ich wartości domyślnych. Na przykład jeśli IsAdmin został pominięty w [Bind(Include ="â&euro;¦")] w operacji edycji, każdy użytkownik, którego nazwa została edytowana za pomocą tej akcji, zostanie zresetowany do IsAdmin = false (każdy edytowany użytkownik utraciłby status administratora). Jeśli chcesz zapobiec aktualizacji niektórych właściwości, należy użyć jednego z innych metod powyżej. Należy zauważyć, że niektóre wersje narzędzi MVC generują klasy kontrolera z [Bind(Include ="â&euro;¦")] w akcje edycji i sugerują, że usunięcie właściwości z tej listy zapobiegnie nadmiernemu publikowaniu ataków. Jednak jak opisano powyżej, to podejście nie działa zgodnie z oczekiwaniami i zamiast tego spowoduje zresetowanie wszystkich danych w pominiętych właściwości do ich wartości domyślnych.</li><li>**Czy w przypadku operacji tworzenia istnieją jakieś zastrzeżenia używające&euro;[Bind(Include ="â ¦")] zamiast oddzielnych modeli wiązania? -** Tak. Po pierwsze to podejście nie działa w przypadku edytowania scenariuszy, wymagając obsługi dwóch oddzielnych podejść w celu wyeliminowania wszystkich luk w zabezpieczeniach związanych z nadmiernym księgowaniem. Po drugie oddzielne modele wiązania wymuszają rozdzielenie problemów między kształtem używanym do wprowadzania danych przez użytkownika a kształtem używanym do trwałości, czego nie robi coś [Bind(Include ="â&euro;¦")] nie. Po trzecie, należy zauważyć, że&euro;[Bind(Include ="â ¦")] może obsługiwać tylko właściwości najwyższego poziomu; nie można zezwolić tylko na części właściwości podrzędnych (takie jak "Details.Name") w atrybucie. Na koniec i być może najważniejsze, przy użyciu [Bind(Include ="â&euro;¦")] dodaje dodatkowy krok, który musi być zapamiętany za każdym razem, gdy klasa jest używana do wiązania modelu. Jeśli nowa metoda akcji wiąże się bezpośrednio z klasą danych i zapomni dołączyć&euro;atrybut [Bind(Include ="â ¦")] może być narażony na&euro;ataki nadmiernego publikowania, więc podejście [Bind(Include ="â ¦")] jest domyślnie nieco mniej bezpieczne. Jeśli używasz [Bind(Include ="â&euro;¦")], należy zawsze pamiętać, aby określić go za każdym razem, gdy klasy danych są wyświetlane jako parametry metody akcji.</li><li>**W przypadku operacji tworzenia, co z umieszczeniem&euro;atrybutu [Bind(Include ="â ¦")] w samej klasie modelu? Czy to podejście nie pozwala uniknąć konieczności zapamiętywania umieszczania atrybutu na każdej metodzie działania? -** Takie podejście działa w niektórych przypadkach. Za pomocą [Bind(Include&euro;="â ¦")] na samym typie modelu (a nie na parametry akcji przy użyciu tej&euro;klasy), nie pozwala na to, aby pamiętać o uwzględnienie [Bind(Include ="â ¦")] atrybutu dla każdej metody akcji. Za pomocą atrybutu bezpośrednio na klasy skutecznie tworzy oddzielny obszar powierzchni tej klasy do celów wiązania modelu. Jednak takie podejście pozwala tylko na jeden kształt wiązania modelu na klasę modelu. Jeśli jedna metoda akcji musi zezwolić na powiązanie modelu pola (na przykład akcję tylko dla administratora, która aktualizuje role użytkowników) i inne akcje muszą zapobiec wiązaniu modelu tego pola, to podejście nie będzie działać. Każda klasa może mieć tylko jeden kształt wiązania modelu; Jeśli różne akcje potrzebują różnych kształtów wiązania modelu, muszą reprezentować te oddzielne kształty przy użyciu oddzielnych klas wiązania modelu lub oddzielnych atrybutów [Bind(Include ="â&euro;¦")] w metodach akcji.</li><li>**Co to są modele wiązania? Czy są to samo, co modele widoku? -** Są to dwie powiązane koncepcje. Termin model wiązania odnosi się do klasy modelu używanej na liście parametrów akcji (kształt przeszedł z wiązania modelu MVC do metody akcji). Model widoku terminu odnosi się do klasy modelu przekazywane z metody akcji do widoku. Za pomocą modelu specyficznego dla widoku jest wspólne podejście do przekazywania danych z metody akcji do widoku. Często ten kształt jest również odpowiedni dla wiązania modelu, a termin model widoku może służyć do odwoływania się do tego samego modelu używanego w obu miejscach. Aby być precyzyjnym, procedura ta mówi konkretnie o wiążących modelach, koncentrując się na kształcie przekazanym do działania, co ma znaczenie dla celów masowego przydzielania.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Kodowanie niezaufanego wyjścia sieci Web przed renderowaniem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, Formularze internetowe, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Jak zapobiec cross-site skryptów w ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [Cross-site Scripting](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) Zapobieganie Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Kroki** | Skrypty między witrynami (zwykle skracane jako XSS) to wektor ataku dla usług online lub dowolnej aplikacji/składnika, która zużywa dane wejściowe z sieci Web. Luki w zabezpieczeniach XSS mogą umożliwiać osobie atakującej wykonanie skryptu na komputerze innego użytkownika za pośrednictwem zagrożonej aplikacji sieci web. Złośliwe skrypty mogą być używane do kradzieży plików cookie i w inny sposób manipulowania komputerem ofiary za pośrednictwem języka JavaScript. XSS jest zapobiegana przez sprawdzanie poprawności danych wejściowych użytkownika, upewniając się, że jest dobrze sformułowany i kodowania przed renderowaniem na stronie sieci web. Sprawdzanie poprawności danych wejściowych i kodowania danych wyjściowych można wykonać za pomocą biblioteki ochrony sieci Web. W przypadku kodu\#zarządzanego (C , VB.NET itp.) użyj jednej lub więcej odpowiednich metod kodowania z biblioteki ochrony sieci Web (Anti-XSS), w zależności od kontekstu, w którym dane wejściowe użytkownika są manifestowane:| 

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

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Wykonywanie sprawdzania poprawności danych wejściowych i filtrowania we wszystkich właściwościach modelu typu ciągu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Dodawanie sprawdzania poprawności,](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation) [sprawdzanie poprawności danych modelu w aplikacji MVC,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)zasady [przewodnie dla aplikacji ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | <p>Wszystkie parametry wejściowe muszą zostać zweryfikowane, zanim zostaną użyte w aplikacji, aby upewnić się, że aplikacja jest chroniona przed złośliwymi plikami wejściowymi użytkownika. Sprawdź poprawność wartości wejściowych przy użyciu weryfikacji wyrażenia regularnego po stronie serwera za pomocą strategii sprawdzania poprawności białej listy. Niezaajnasłowione dane wejściowe użytkownika / parametry przekazywane do metod mogą powodować luki w wstrzyknięciu kodu.</p><p>W przypadku aplikacji sieci web punkty wejścia mogą również zawierać pola formularza, połączenia z plikami QueryStrings, pliki cookie, nagłówki HTTP i parametry usługi sieci web.</p><p>Następujące kontrole sprawdzania poprawności danych wejściowych muszą być wykonywane po powiązaniu modelu:</p><ul><li>Właściwości modelu powinny być opisywane adnotacją RegularExpression, do akceptowania dozwolonych znaków i maksymalnej dopuszczalnej długości</li><li>Metody kontrolera powinny spełniać ważność modelstate</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Sanityzacji należy stosować w polach formularza, które akceptują wszystkie znaki, np.

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Kodowanie niebezpiecznych danych wejściowych](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [dezynfekcji HTML](https://github.com/mganss/HtmlSanitizer) |
| **Kroki** | <p>Zidentyfikuj wszystkie statyczne znaczniki, których chcesz użyć. Powszechną praktyką jest ograniczenie formatowania do `<b>` bezpiecznych elementów HTML, takich jak (pogrubienie) i `<i>` (kursywa).</p><p>Przed zapisaniem danych należy zakodować je w formacie HTML. Dzięki temu każdy złośliwy skrypt jest bezpieczny, powodując, że jest obsługiwany jako tekst, a nie jako kod wykonywalny.</p><ol><li>Wyłącz sprawdzanie poprawności żądania ASP.NET przez dodanie atrybutu ValidateRequest="false" do dyrektywy \@ Page</li><li>Kodowanie danych wejściowych ciągu za pomocą metody HtmlEncode</li><li>Użyj StringBuilder i wywołać jego Replace metody selektywnie usunąć kodowanie na elementy HTML, które chcesz zezwolić</li></ol><p>Strona w odwołaniach wyłącza sprawdzanie poprawności żądania `ValidateRequest="false"`ASP.NET, ustawiając . To HTML koduje dane wejściowe i `<b>` `<i>` selektywnie pozwala i alternatywnie, .NET biblioteki do dezynfekcji HTML mogą być również używane.</p><p>HtmlSanitizer to biblioteka .NET do czyszczenia fragmentów HTML i dokumentów z konstrukcji, które mogą prowadzić do ataków XSS. Używa AngleSharp do analizować, manipulować i renderować HTML i CSS. HtmlSanitizer można zainstalować jako pakiet NuGet, a dane wejściowe użytkownika mogą być przekazywane za pośrednictwem odpowiednich metod dezynfekcji HTML lub CSS, w zależności od przypadku, po stronie serwera. Należy pamiętać, że sanitization jako kontrola bezpieczeństwa powinna być traktowana tylko jako ostatnia opcja.</p><p>Sprawdzanie poprawności danych wejściowych i kodowania danych wyjściowych są uważane za lepsze mechanizmy kontroli zabezpieczeń.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Nie przypisywać elementów DOM do zlewów, które nie mają wbudowanego kodowania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | Wiele funkcji javascript nie koduje domyślnie. Podczas przypisywania niezaufanych danych wejściowych do elementów DOM za pośrednictwem takich funkcji, może spowodować wykonanie skryptu krzyżowego (XSS).| 

### <a name="example"></a>Przykład
Oto niepewne przykłady: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nie używaj `innerHtml`; zamiast tego `innerText`użyj . Podobnie, zamiast `$("#elm").html()`, użyj`$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Sprawdź poprawność wszystkich przekierowań w aplikacji są zamknięte lub wykonane bezpiecznie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Ramy autoryzacji OAuth 2.0 — otwarte readresatory](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroki** | <p>Projekt aplikacji wymagający przekierowania do lokalizacji dostarczonej przez użytkownika musi ograniczać możliwe cele przekierowania do wstępnie zdefiniowanej "bezpiecznej" listy lokacji lub domen. Wszystkie przekierowania w aplikacji muszą być zamknięte/bezpieczne.</p><p>W tym celu:</p><ul><li>Identyfikowanie wszystkich przekierowań</li><li>Zaimplementuj odpowiednie środki zaradcze dla każdego przekierowania. Odpowiednie środki zaradcze obejmują przekierowanie na białej liście lub potwierdzenie użytkownika. Jeśli witryna sieci Web lub usługa z otwartą luką w zabezpieczeniach przekierowania korzysta z dostawców tożsamości Facebook/OAuth/OpenID, osoba atakująca może ukraść token logowania użytkownika i podszyć się pod niego. Jest to nieodłączne ryzyko podczas korzystania z OAuth, co jest udokumentowane w RFC 6749 "The OAuth 2.0 Authorization Framework", Sekcja 10.15 "Otwarte przekierowania" Podobnie, poświadczenia użytkowników mogą zostać naruszone przez ataki phishingowe spear za pomocą otwartych przekierowań</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementowanie sprawdzania poprawności danych wejściowych dla wszystkich parametrów typu ciągu akceptowanych przez metody kontrolera

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Sprawdzanie poprawności danych modelu w aplikacji MVC,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [zasady przewodnie dla aplikacji ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | Dla metod, które po prostu zaakceptować typ danych pierwotnych, a nie modele jako argument, sprawdzanie poprawności danych wejściowych przy użyciu wyrażenia regularnego należy wykonać. Tutaj Regex.IsMatch powinny być używane z prawidłowym wzorem regex. Jeśli dane wejściowe nie są zgodne z określonym wyrażeniem regularnym, formant nie powinien być kontynuowany i powinno być wyświetlane odpowiednie ostrzeżenie dotyczące niepowodzenia sprawdzania poprawności.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Ustaw górny limit limitu czasu przetwarzania wyrażeń regularnych, aby zapobiec dos z powodu złych wyrażeń regularnych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, Formularze internetowe, MVC5, MVC6  |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Właściwość DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Kroki** | Aby zapewnić ataki typu "odmowa usługi" na źle utworzone wyrażenia regularne, które powodują wiele wycofywania, ustaw domyślny limit czasu na zwłoki globalnej. Jeśli czas przetwarzania trwa dłużej niż zdefiniowany górny limit, to zgłosić wyjątek timeout. Jeśli nic nie jest skonfigurowany, limit czasu będzie nieskończony.| 

### <a name="example"></a>Przykład
Na przykład następująca konfiguracja zrzuci RegexMatchTimeoutException, jeśli przetwarzanie trwa dłużej niż 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Unikaj używania html.raw w widokach Razor

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| Krok | ASP.NET strony internetowe (Razor) wykonują automatyczne kodowanie HTML. Wszystkie ciągi drukowane przez osadzone bryłki kodu (@ bloki) są automatycznie zakodowane w formacie HTML. Jednak gdy `HtmlHelper.Raw` metoda jest wywoływana, zwraca znaczników, który nie jest zakodowany HTML. Jeśli `Html.Raw()` używana jest metoda pomocnika, pomija ona automatyczną ochronę przed kodowaniem, którą zapewnia Razor.|

### <a name="example"></a>Przykład
Oto niepewny przykład: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Nie należy `Html.Raw()` używać, chyba że trzeba wyświetlić znaczniki. Ta metoda nie wykonuje kodowania danych wyjściowych niejawnie. Użyj innych pomocników ASP.NET np.`@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Nie używaj zapytań dynamicznych w procedurach przechowywanych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | <p>Atak iniekcji SQL wykorzystuje luki w weryfikacji danych wejściowych, aby uruchomić dowolne polecenia w bazie danych. Może wystąpić, gdy aplikacja używa danych wejściowych do konstruowania dynamicznych instrukcji SQL, aby uzyskać dostęp do bazy danych. Może również wystąpić, jeśli kod używa procedur przechowywanych, które są przekazywane ciągi, które zawierają nieprzetworzone dane wejściowe użytkownika. Za pomocą ataku iniekcji SQL osoba atakująca może wykonywać dowolne polecenia w bazie danych. Wszystkie instrukcje SQL (w tym instrukcje SQL w procedurach przechowywanych) muszą być parametryzowane. Sparametryzowane instrukcje SQL będą akceptować znaki, które mają specjalne znaczenie dla SQL (jak pojedynczy cudzysłów) bez problemów, ponieważ są one silnie wpisane. |

### <a name="example"></a>Przykład
Oto przykład niezabezpieczonych dynamicznych procedur składowanych: 

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

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Upewnij się, że sprawdzanie poprawności modelu odbywa się w metodach interfejsu API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Sprawdzanie poprawności modelu w ASP.NET interfejsie API sieci Web](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Gdy klient wysyła dane do internetowego interfejsu API, jest obowiązkowe, aby sprawdzić poprawność danych przed wykonaniem przetwarzania. W przypadku ASP.NET interfejsów API sieci Web, które akceptują modele jako dane wejściowe, użyj adnotacji danych w modelach, aby ustawić reguły sprawdzania poprawności właściwości modelu.|

### <a name="example"></a>Przykład
Poniższy kod demonstruje to samo: 

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
W metodzie akcji kontrolerów interfejsu API ważność modelu musi być jawnie sprawdzona, jak pokazano poniżej: 

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

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementowanie sprawdzania poprawności danych wejściowych dla wszystkich parametrów typu ciągu akceptowanych przez metody interfejsu API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, MVC 5, MVC 6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Sprawdzanie poprawności danych modelu w aplikacji MVC,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [zasady przewodnie dla aplikacji ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | Dla metod, które po prostu zaakceptować typ danych pierwotnych, a nie modele jako argument, sprawdzanie poprawności danych wejściowych przy użyciu wyrażenia regularnego należy wykonać. Tutaj Regex.IsMatch powinny być używane z prawidłowym wzorem regex. Jeśli dane wejściowe nie są zgodne z określonym wyrażeniem regularnym, formant nie powinien być kontynuowany i powinno być wyświetlane odpowiednie ostrzeżenie dotyczące niepowodzenia sprawdzania poprawności.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Upewnij się, że parametry bezpieczne dla typu są używane w interfejsie API sieci Web w celu uzyskania dostępu do danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | <p>Jeśli używasz parametrów kolekcji, SQL traktuje dane wejściowe jest jako wartość literału, a następnie jako kod wykonywalny. Kolekcja Parameters może służyć do wymuszania ograniczeń typu i długości danych wejściowych. Wartości poza zakresem wyzwalają wyjątek. Jeśli parametry SQL bezpieczne dla typu nie są używane, osoby atakujące mogą być w stanie wykonać ataki iniekcji, które są osadzone w niefiltrowanych danych wejściowych.</p><p>Użyj parametrów typu bezpieczne podczas konstruowania zapytań SQL, aby uniknąć ewentualnych ataków iniekcji SQL, które mogą wystąpić przy niefiltrowanych danych wejściowych. Można użyć parametrów typu bezpieczne z procedurami przechowywanymi i z dynamicznymi instrukcjami SQL. Parametry są traktowane jako wartości literału przez bazę danych, a nie jako kod wykonywalny. Parametry są również sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład
Poniższy kod pokazuje, jak używać parametrów typu bezpieczne z SqlParameterCollection podczas wywoływania procedury składowanej. 

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
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli dane nie są zgodne z typem lub długością zdefiniowaną przez parametr, klasa SqlParameter zgłasza wyjątek. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Używanie sparametrowanych zapytań SQL dla usługi Cosmos DB

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Ogłaszanie parametryzacji SQL w usłudze Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroki** | Mimo że usługa Azure Cosmos DB obsługuje tylko kwerendy tylko do odczytu, iniekcja SQL jest nadal możliwe, jeśli kwerendy są konstruowane przez łączenie z danymi wejściowymi użytkownika. Może być możliwe dla użytkownika, aby uzyskać dostęp do danych, które nie powinny być dostępne w ramach tej samej kolekcji przez tworzenie złośliwych zapytań SQL. Użyj sparametryzowanych zapytań SQL, jeśli kwerendy są konstruowane na podstawie danych wejściowych użytkownika. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>Sprawdzanie poprawności danych wejściowych WCF za pomocą powiązania schematu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Kroki** | <p>Brak walidacji prowadzi do różnych ataków iniekcji typu.</p><p>Sprawdzanie poprawności wiadomości reprezentuje jeden wiersz obrony w ochronie aplikacji WCF. Dzięki takiemu podejściu można sprawdzić poprawność wiadomości przy użyciu schematów w celu ochrony operacji usługi WCF przed atakiem złośliwego klienta. Sprawdź poprawność wszystkich wiadomości odebranych przez klienta, aby chronić klienta przed atakiem złośliwej usługi. Sprawdzanie poprawności wiadomości umożliwia sprawdzanie poprawności wiadomości, gdy operacje zużywają kontrakty wiadomości lub kontrakty danych, których nie można wykonać przy użyciu sprawdzania poprawności parametrów. Sprawdzanie poprawności komunikatów umożliwia tworzenie logiki sprawdzania poprawności wewnątrz schematów, zapewniając w ten sposób większą elastyczność i skracając czas opracowywania. Schematy mogą być ponownie stosowane w różnych aplikacjach wewnątrz organizacji, tworząc standardy reprezentacji danych. Ponadto sprawdzanie poprawności wiadomości umożliwia ochronę operacji, gdy zużywają one bardziej złożone typy danych obejmujące kontrakty reprezentujące logikę biznesową.</p><p>Aby wykonać sprawdzanie poprawności wiadomości, najpierw skompilować schemat, który reprezentuje operacje usługi i typy danych używane przez te operacje. Następnie należy utworzyć klasę .NET, która implementuje niestandardowy inspektor wiadomości klienta i inspektor wiadomości niestandardowego dyspozytora, aby sprawdzić poprawność wiadomości wysłanych/odebranych do/z usługi. Następnie należy zaimplementować zachowanie niestandardowego punktu końcowego, aby włączyć sprawdzanie poprawności wiadomości na kliencie i w usłudze. Na koniec zaimplementujesz niestandardowy element konfiguracji w klasie, który umożliwia uwidacznianie rozszerzonego niestandardowego zachowania punktu końcowego w pliku konfiguracyjnym usługi lub klienta"</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF- Sprawdzanie poprawności danych wejściowych za pośrednictwem inspektorów parametrów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Kroki** | <p>Sprawdzanie poprawności danych i danych reprezentuje jedną ważną linię obrony w ochronie aplikacji WCF. Należy sprawdzić poprawność wszystkich parametrów ujawnionych w operacjach usługi WCF, aby chronić usługę przed atakiem złośliwego klienta. Z drugiej strony należy również sprawdzić poprawność wszystkich wartości zwracane odebranych przez klienta, aby chronić klienta przed atakiem złośliwej usługi</p><p>WCF zapewnia różne punkty rozszerzalności, które umożliwiają dostosowanie zachowania środowiska uruchomieniowego WCF przez tworzenie rozszerzeń niestandardowych. Inspektorzy komunikatów i inspektorzy parametrów to dwa mechanizmy rozszerzalności używane w celu uzyskania większej kontroli nad danymi przekazywaniem między klientem a usługą. Inspektorów parametrów należy używać do sprawdzania poprawności danych wejściowych i używać inspektorów komunikatów tylko wtedy, gdy trzeba sprawdzić całą wiadomość przepływającą do i z usługi.</p><p>Aby wykonać sprawdzanie poprawności danych wejściowych, należy utworzyć klasę .NET i zaimplementować inspektora parametrów niestandardowych w celu sprawdzania poprawności parametrów w operacjach w usłudze. Następnie zaimplementujesz niestandardowe zachowanie punktu końcowego, aby włączyć sprawdzanie poprawności zarówno na kliencie, jak i w usłudze. Na koniec zaimplementujesz niestandardowy element konfiguracji w klasie, który umożliwia udostępnienie rozszerzonego niestandardowego zachowania punktu końcowego w pliku konfiguracyjnym usługi lub klienta</p>|
