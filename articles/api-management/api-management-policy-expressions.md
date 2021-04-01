---
title: Wyrażenia zasad API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat wyrażeń zasad w usłudze Azure API Management. Zobacz przykłady i wyświetlanie dodatkowych dostępnych zasobów.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: aec1967f0652e18c4a24ca258c14a103355b22af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219319"
---
# <a name="api-management-policy-expressions"></a>Wyrażenia zasad API Management
W tym artykule omówiono składnię wyrażeń zasad w języku C# 7. Każde wyrażenie ma dostęp do niejawnie podanej zmiennej [kontekstowej](api-management-policy-expressions.md#ContextVariables) i dozwolone [podzbiór](api-management-policy-expressions.md#CLRTypes) typów .NET Framework.

Więcej informacji:

- Zobacz, jak dostarczać informacje kontekstu do usługi wewnętrznej bazy danych. Aby podać te informacje, użyj [Ustawienia ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) i [Ustaw zasady nagłówka HTTP](api-management-transformation-policies.md#SetHTTPheader) .
- Zapoznaj się z tematem jak używać zasad [tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) do wstępnego autoryzowania dostępu do operacji na podstawie oświadczeń tokenów.
- Zobacz jak używać śladu [inspektora interfejsu API](./api-management-howto-api-inspector.md) , aby zobaczyć, jak oceniane są zasady i wyniki tych ocen.
- Zobacz jak używać wyrażeń z zasadami [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) i [Zapisz do pamięci podręcznej](api-management-caching-policies.md#StoreToCache) , aby skonfigurować buforowanie odpowiedzi API Management. Ustaw czas trwania, który jest zgodny z buforowaniem odpowiedzi usługi wewnętrznej bazy danych określonej przez dyrektywę usługi kopii zapasowej `Cache-Control` .
- Zobacz, jak wykonywać filtrowanie zawartości. Usuń elementy danych z odpowiedzi otrzymanej z zaplecza przy użyciu [przepływu sterowania](api-management-advanced-policies.md#choose) i [Ustaw](api-management-transformation-policies.md#SetBody) zasady dotyczące treści.
- Aby pobrać instrukcje zasad, zobacz repozytorium usługi [API Management — przykłady/zasady](https://github.com/Azure/api-management-samples/tree/master/policies) usługi GitHub.


## <a name="syntax"></a><a name="Syntax"></a> Obowiązuje
Wyrażenia pojedynczej instrukcji są ujęte w `@(expression)` , gdzie `expression` jest poprawnie sformułowaną instrukcją języka C#.

Wyrażenia wieloinstrukcji są ujęte w `@{expression}` . Wszystkie ścieżki kodu w wyrażeniach wieloinstrukcji muszą kończyć się `return` instrukcją.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> Pokazują

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Użycie
Wyrażenia mogą być używane jako wartości atrybutów lub wartości tekstowe w dowolnych [zasadach](api-management-policies.md) API Management (chyba że odwołanie do zasad określono inaczej).

> [!IMPORTANT]
> W przypadku używania wyrażeń zasad istnieje tylko ograniczona weryfikacja wyrażeń zasad, gdy zasady są zdefiniowane. Wyrażenia są wykonywane przez bramę w czasie wykonywania, wszystkie wyjątki generowane przez wyrażenia zasad powodują błąd czasu wykonania.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> Typy .NET Framework dozwolone w wyrażeniach zasad
Poniższa tabela zawiera listę typów .NET Framework i ich elementów członkowskich, które są dozwolone w wyrażeniach zasad.

|Typ|Obsługiwane elementy członkowskie|
|--------------|-----------------------|
|Newtonsoft.Js. Grubi|Wszystko|
|Newtonsoft.Json.Jsonconvert|Serializacjaobject, deserializacjaobject|
|Newtonsoft.Js. LINQ. Extensions|Wszystko|
|Newtonsoft.Js. LINQ. JArray|Wszystko|
|Newtonsoft.Js. LINQ. JConstructor|Wszystko|
|Newtonsoft.Js. LINQ. JContainer|Wszystko|
|Newtonsoft.Js. LINQ. JObject|Wszystko|
|Newtonsoft.Js. LINQ. JProperty|Wszystko|
|Newtonsoft.Js. LINQ. JRaw|Wszystko|
|Newtonsoft.Js. LINQ. JToken|Wszystko|
|Newtonsoft.Js. LINQ. JTokenType|Wszystko|
|Newtonsoft.Js. LINQ. JValue|Wszystko|
|System. Array|Wszystko|
|System. BitConverter|Wszystko|
|System. Boolean|Wszystko|
|System. Byte|Wszystko|
|System. Char|Wszystko|
|System. Collections. Generic. dictionary<TKey, TValue>|Wszystko|
|System. Collections. Generic. HashSet —\<T>|Wszystko|
|System. Collections. Generic. ICollection\<T>|Wszystko|
|System. Collections. Generic. IDictionary<TKey, TValue>|Wszystko|
|System. Collections. Generic. IEnumerable\<T>|Wszystko|
|System. Collections. Generic. IEnumerator\<T>|Wszystko|
|System. Collections. Generic. IList\<T>|Wszystko|
|System. Collections. Generic. IReadOnlyCollection\<T>|Wszystko|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Wszystko|
|System. Collections. Generic. ISet\<T>|Wszystko|
|System. Collections. Generic. KeyValuePair<TKey, TValue>|Wszystko|
|System. Collections. Generic. list\<T>|Wszystko|
|System. Collections. Generic. Queue\<T>|Wszystko|
|System. Collections. Generic. Stack\<T>|Wszystko|
|System. Convert|Wszystko|
|System. DateTime|(Konstruktor), Dodaj, addDays, addgodz., addms, addminut, addmiesiącach, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, dzieńroku, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekundy, czas, MinValue, month, Now, Parse,|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Wszystko|
|System. Decimal|Wszystko|
|System. Double|Wszystko|
|System. Exception|Wszystko|
|System. GUID|Wszystko|
|System. Int16|Wszystko|
|System. Int32|Wszystko|
|System. Int64|Wszystko|
|System. IO. StringReader|Wszystko|
|System. IO. StringWriter|Wszystko|
|System. LINQ. wyliczalne|Wszystko|
|System. Math|Wszystko|
|System. MidpointRounding|Wszystko|
|System .NET. WebUtility|Wszystko|
|System. Nullable|Wszystko|
|System. Random|Wszystko|
|System. nadana|Wszystko|
|System. Security. Cryptography. AsymmetricAlgorithm|Wszystko|
|System. Security. Cryptography. CipherMode|Wszystko|
|System. Security. Cryptography. algorytm|Wszystko|
|System. Security. Cryptography. HashAlgorithmName|Wszystko|
|System. Security. Cryptography. HMAC|Wszystko|
|System. Security. Cryptography. HMACMD5|Wszystko|
|System. Security. Cryptography. HMACSHA1|Wszystko|
|System. Security. Cryptography. HMACSHA256|Wszystko|
|System. Security. Cryptography. HMACSHA384|Wszystko|
|System. Security. Cryptography. HMACSHA512|Wszystko|
|System. Security. Cryptography. KeyedHashAlgorithm|Wszystko|
|System. Security. Cryptography. MD5|Wszystko|
|System. Security. Cryptography. OID|Wszystko|
|System. Security. Cryptography. Uzupełnieniemode|Wszystko|
|System. Security. Cryptography. RNGCryptoServiceProvider|Wszystko|
|System. Security. Cryptography. RSA|Wszystko|
|System. Security. Cryptography. RSAEncryptionPadding|Wszystko|
|System. Security. Cryptography. RSASignaturePadding|Wszystko|
|System. Security. Cryptography. SHA1|Wszystko|
|System. Security. Cryptography. SHA1Managed|Wszystko|
|System. Security. Cryptography. SHA256|Wszystko|
|System. Security. Cryptography. SHA256Managed|Wszystko|
|System. Security. Cryptography. SHA384|Wszystko|
|System. Security. Cryptography. SHA384Managed|Wszystko|
|System. Security. Cryptography. SHA512|Wszystko|
|System. Security. Cryptography. SHA512Managed|Wszystko|
|System. Security. Cryptography. SymmetricAlgorithm|Wszystko|
|System. Security. Cryptography. x509. PublicKey|Wszystko|
|System. Security. Cryptography. x509. RSACertificateExtensions|Wszystko|
|System. Security. Cryptography. x509. X500DistinguishedName|Nazwa|
|System. Security. Cryptography. x509. x509|Wszystko|
|System. Security. Cryptography. x509. X509Certificate2|Wszystko|
|System. Security. Cryptography. x509. X509ContentType|Wszystko|
|System. Security. Cryptography. x509. X509NameType|Wszystko|
|System. Single|Wszystko|
|System. String|Wszystko|
|System. StringComparer|Wszystko|
|System. StringComparison|Wszystko|
|System. StringSplitOptions|Wszystko|
|System. Text. Encoding|Wszystko|
|System. Text. RegularExpressions. Capture|Indeks, długość, wartość|
|System. Text. RegularExpressions. CaptureCollection|Liczba, element|
|System. Text. RegularExpressions. Group|Przechwytywanie, sukces|
|System. Text. RegularExpressions. GroupCollection|Liczba, element|
|System. Text. RegularExpressions. Match|Puste, grupy, wynik|
|System. Text. RegularExpressions. wyrażenie regularne|(Konstruktor), IsMatch, Match, dopasowań, Replace, Unescape, Split|
|System. Text. RegularExpressions. RegexOptions|Wszystko|
|System. Text. StringBuilder|Wszystko|
|System. TimeSpan|Wszystko|
|System. TimeZone|Wszystko|
|System. TimeZoneInfo. AdjustmentRule|Wszystko|
|System. TimeZoneInfo. TransitionTime|Wszystko|
|System. TimeZoneInfo|Wszystko|
|System. krotka|Wszystko|
|System. UInt16|Wszystko|
|System. UInt32|Wszystko|
|System. UInt64|Wszystko|
|System. URI|Wszystko|
|System. UriPartial|Wszystko|
|System.Xml. LINQ. Extensions|Wszystko|
|System.Xml. LINQ. XAttribute|Wszystko|
|System.Xml. LINQ. XCData|Wszystko|
|System.Xml. LINQ. XComment|Wszystko|
|System.Xml. LINQ. XContainer|Wszystko|
|System.Xml. LINQ. XDeclaration|Wszystko|
|System.Xml. LINQ. XDocument|Wszystkie, z wyjątkiem: Load|
|System.Xml. LINQ. XDocumenttype|Wszystko|
|System.Xml. LINQ. XElement|Wszystko|
|System.Xml. LINQ. XName|Wszystko|
|System.Xml. LINQ. XNamespace|Wszystko|
|System.Xml. LINQ. XNode|Wszystko|
|System.Xml. LINQ. XNodeDocumentOrderComparer|Wszystko|
|System.Xml. LINQ. XNodeEqualityComparer|Wszystko|
|System.Xml. LINQ. XObject|Wszystko|
|System.Xml. LINQ. XProcessingInstruction|Wszystko|
|System.Xml. LINQ. XText|Wszystko|
|System.Xml.XmlNodeType|Wszystko|

## <a name="context-variable"></a><a name="ContextVariables"></a> Zmienna kontekstowa
Zmienna o nazwie `context` jest niejawnie dostępna w każdym [wyrażeniu](api-management-policy-expressions.md#Syntax)zasad. Jego członkowie zawierają informacje dotyczące programu `\request` . Wszystkie `context` elementy członkowskie są tylko do odczytu.

|Zmienna kontekstowa|Dozwolone metody, właściwości i wartości parametrów|
|----------------------|-------------------------------------------------------|
|kontekst|[Interfejs API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Wdrożenie](#ref-context-deployment)<br /><br /> Upłynęło: przedział czasu w czasie między wartością sygnatury czasowej a bieżącym czasem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operacja](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [Żądanie](#ref-context-request)<br /><br /> IdentyfikatorŻądania: GUID — unikatowy identyfikator żądania<br /><br /> [Odpowiedź](#ref-context-response)<br /><br /> [Subskrypcja](#ref-context-subscription)<br /><br /> Sygnatura czasowa: Data i godzina odebrania żądania<br /><br /> Śledzenie: bool-wskazuje, czy śledzenie jest włączone, czy wyłączone <br /><br /> [Użytkownik](#ref-context-user)<br /><br /> [Zmienne](#ref-context-variables): IReadOnlyDictionary<String, Object><br /><br /> void Trace (Message: String)|
|<a id="ref-context-api"></a>Context. Interfejsu API|ID: ciąg<br /><br /> IsCurrentRevision: bool<br /><br />  Name: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Poprawka: ciąg<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Wersja: ciąg |
|<a id="ref-context-deployment"></a>Context. Mieszczeniu|Region: ciąg<br /><br /> ServiceName: ciąg<br /><br /> Certyfikaty: IReadOnlyDictionary<String, X509Certificate2>|
|<a id="ref-context-lasterror"></a>Context. LastError|Źródło: ciąg<br /><br /> Przyczyna: ciąg<br /><br /> Komunikat: ciąg<br /><br /> Zakres: ciąg<br /><br /> Sekcja: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> PolicyId: ciąg<br /><br /> Aby uzyskać więcej informacji o kontekście. LastError, zobacz [Obsługa błędów](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Context. Operacje|ID: ciąg<br /><br /> Metoda: ciąg<br /><br /> Name: ciąg<br /><br /> UrlTemplate: ciąg|
|<a id="ref-context-product"></a>Context. Iloczyn|Interfejsy API: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Grupy: interfejs IEnumerable<[IGroup](#ref-igroup)\><br /><br /> ID: ciąg<br /><br /> Name: ciąg<br /><br /> State: Wyliczenie ProductState {NotPublished, opublikowano}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>Context. Żądając|Treść: [IMessageBody](#ref-imessagebody) lub `null` Jeśli żądanie nie ma treści.<br /><br /> Certyfikat: System. Security. Cryptography. x509. X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<String, String [] ><br /><br /> IpAddress: ciąg<br /><br /> MatchedParameters: IReadOnlyDictionary<ciąg, ciąg><br /><br /> Metoda: ciąg<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Adres URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontekst ciągu. Request. Headers. GetValueOrDefault (headerName: String, DefaultValue: String)|headerName: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości nagłówka żądania oddzielone przecinkami lub `defaultValue` Jeśli nagłówek nie zostanie znaleziony.|
|<a id="ref-context-response"></a>Context. Reakcji|Treść: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<String, String [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: ciąg|
|<a id="ref-context-response-headers"></a>kontekst ciągu. Response. Headers. GetValueOrDefault (headerName: String, DefaultValue: String)|headerName: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości nagłówka odpowiedzi rozdzielane przecinkami lub `defaultValue` nie można znaleźć nagłówka.|
|<a id="ref-context-subscription"></a>Context. Ramach|CreatedDate: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: ciąg<br /><br /> Klucz: ciąg<br /><br /> Name: ciąg<br /><br /> PrimaryKey: ciąg<br /><br /> SecondaryKey: ciąg<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>Context. Użytkownicy|Adres e-mail: ciąg<br /><br /> FirstName: ciąg<br /><br /> Grupy: interfejs IEnumerable<[IGroup](#ref-igroup)\><br /><br /> ID: ciąg<br /><br /> Tożsamości: interfejs IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: ciąg<br /><br /> Uwaga: ciąg<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: ciąg<br /><br /> Name: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Protokoły: interfejs IEnumerable<ciąg\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: ciąg<br /><br /> Name: ciąg|
|<a id="ref-imessagebody"></a>IMessageBody|Jako<T \> (preserveContent: bool = false): gdzie T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> `context.Request.Body.As<T>`Metody i `context.Response.Body.As<T>` są używane do odczytywania treści żądania i odpowiedzi w określonym typie `T` . Domyślnie metoda używa oryginalnego strumienia treści wiadomości i renderuje go po powrocie. Aby uniknąć tego, że metoda operuje na kopii strumienia treści, ustaw `preserveContent` parametr na `true` . Przejdź [tutaj](api-management-transformation-policies.md#SetBody) , aby zobaczyć przykład.|
|<a id="ref-iurl"></a>IUrl|Host: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Port: int<br /><br /> [Zapytanie](#ref-iurl-query): IReadOnlyDictionary<String, String [] ><br /><br /> QueryString: ciąg<br /><br /> Schemat: ciąg|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: ciąg<br /><br /> Dostawca: ciąg|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Nagłówek: ciąg<br /><br /> Zapytanie: ciąg|
|<a id="ref-iurl-query"></a>String IUrl. Query. GetValueOrDefault (queryParameterName: String, DefaultValue: String)|queryParameterName: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości parametrów zapytania oddzielone przecinkami lub `defaultValue` Jeśli nie znaleziono parametru.|
|<a id="ref-context-variables"></a>Kontekst T. Zmienne. GetValueOrDefault<T \> (VariableName: String, DefaultValue: T)|VariableName: ciąg<br /><br /> DefaultValue: T<br /><br /> Zwraca Rzutowanie wartości zmiennej na typ `T` lub `defaultValue` Jeśli zmienna nie zostanie znaleziona.<br /><br /> Ta metoda zgłasza wyjątek, jeśli określony typ nie jest zgodny z rzeczywistym typem zwracanej zmiennej.|
|BasicAuthCredentials AsBasic (dane wejściowe: ten ciąg)|dane wejściowe: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość nagłówka żądanie autoryzacji uwierzytelniania podstawowego protokołu HTTP, metoda zwraca obiekt typu `BasicAuthCredentials` ; w przeciwnym razie metoda zwraca wartość null.|
|bool TryParseBasic (dane wejściowe: ten ciąg, wynik: out BasicAuthCredentials)|dane wejściowe: ciąg<br /><br /> wynik: BasicAuthCredentials out<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość autoryzacji uwierzytelniania podstawowego HTTP w nagłówku żądania, metoda zwraca `true` i parametr wynik zawiera wartość typu `BasicAuthCredentials` ; w przeciwnym razie metoda zwraca `false` .|
|BasicAuthCredentials|Hasło: ciąg<br /><br /> UserId: ciąg|
|AsJwt JWT (dane wejściowe: ten ciąg)|dane wejściowe: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu JWT, metoda zwraca obiekt typu `Jwt` ; w przeciwnym razie metoda zwraca `null` .|
|bool TryParseJwt (dane wejściowe: ten ciąg, wynik: out JWT)|dane wejściowe: ciąg<br /><br /> wynik: out JWT<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu JWT, metoda zwraca `true` i parametr wynik zawiera wartość typu `Jwt` ; w przeciwnym razie metoda zwraca `false` .|
|JWT|Algorytm: ciąg<br /><br /> Odbiorcy: ciąg<IEnumerable\><br /><br /> Oświadczenia: IReadOnlyDictionary<String, String [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: ciąg<br /><br /> Wystawca: ciąg<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Podmiot: ciąg<br /><br /> Typ: ciąg|
|String JWT. Claims. GetValueOrDefault (claimname: String, DefaultValue: String)|claimname: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości w postaci oddzielone przecinkami lub `defaultValue` nie można znaleźć nagłówka.|
|Byte [] Szyfruj (dane wejściowe: ten bajt [], alg: ciąg, klucz: Byte [], IV: Byte [])|dane wejściowe — tekst do zaszyfrowania<br /><br />alg — Nazwa algorytmu szyfrowania symetrycznego<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zaszyfrowany tekst.|
|Byte [] Szyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm)|dane wejściowe — tekst do zaszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />Zwraca zaszyfrowany tekst.|
|Byte [] Szyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm, klucz: Byte [], IV: Byte [])|dane wejściowe — tekst do zaszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zaszyfrowany tekst.|
|Byte [] Odszyfruj (dane wejściowe: ten bajt [], alg: ciąg, klucz: Byte [], IV: Byte [])|Tekst wejściowy-szyfr do odszyfrowania<br /><br />alg — Nazwa algorytmu szyfrowania symetrycznego<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zwykły tekst.|
|Byte [] Odszyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm)|Tekst wejściowy-szyfr do odszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />Zwraca zwykły tekst.|
|Byte [] Odszyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm, klucz: Byte [], IV: Byte [])|Tekst wejściowy-szyfr do odszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zwykły tekst.|
|bool VerifyNoRevocation (dane wejściowe: this system. Security. Cryptography. x509. X509Certificate2)|Wykonuje weryfikację łańcucha X. 509 bez sprawdzania stanu odwołania do certyfikatu.<br /><br />Obiekt certyfikatu wejściowego<br /><br />Zwraca `true` czy Walidacja zakończyła się pomyślnie; `false` Jeśli sprawdzanie poprawności zakończy się niepowodzeniem.|


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](./policy-reference.md)
