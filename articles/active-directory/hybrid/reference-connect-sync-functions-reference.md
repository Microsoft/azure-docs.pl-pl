---
title: 'Azure AD Connect Sync: informacje o funkcjach | Microsoft Docs'
description: Odwołanie do wyrażeń aprowizacji deklaracyjne w Azure AD Connect synchronizacji.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8ec8eec28d66cf93608393ddca45f78460d831
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279775"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect Sync: informacje o funkcjach
W Azure AD Connect funkcje są używane do manipulowania wartością atrybutu podczas synchronizacji.  
Składnia funkcji jest wyrażana przy użyciu następującego formatu:  
`<output type> FunctionName(<input type> <position name>, ..)`

Jeśli funkcja jest przeciążona i akceptuje wiele składni, zostaną wyświetlone wszystkie prawidłowe składnie.  
Funkcje są jednoznacznie wpisane i sprawdzają, czy typ, który przeszedł, jest zgodny z udokumentowanym typem.  
Jeśli typ nie jest zgodny, zostanie zgłoszony błąd.

Typy są wyrażane przy użyciu następującej składni:

* **bin** — plik binarny
* **bool** — wartość logiczna
* **DT** — Data/godzina UTC
* **enum** — Wyliczenie znanych stałych
* **EXP** — wyrażenie, które ma zostać obliczone jako wartość logiczna
* **mvbin** — wiele wartości binarnych
* **mvstr** — ciąg wielowartościowy
* **mvref** — odwołanie wielowartościowe
* **NUM** — wartość liczbowa
* **ref** — odwołanie
* **str** — ciąg
* **var** — wariant (prawie) każdy inny typ
* **void** — nie zwraca wartości

Funkcje o typach **mvbin**, **mvstr**i **mvref** mogą działać tylko w przypadku atrybutów wielowartościowych. Funkcja with **bin**, **str**i **ref** Work na atrybutach jednowartościowych i wielowartościowych.

## <a name="functions-reference"></a>Informacje ogólne o funkcjach

* **Certyfikat**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [Iscert](#iscert)
* **Konwersja**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **Data i godzina**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Znajdź](#now)
  * [NumFromDate](#numfromdate)
* **Katalog**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Ocena**
  * [IsBitSet](#isbitset)
  * [ISDATE](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [Isobecne](#ispresent)
  * [IsString](#isstring)
* **Math**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Wiele * wartości**
  * [Wyświetlana](#contains)
  * [Liczbą](#count)
  * [Element](#item)
  * [ItemOrNull](#itemornull)
  * [Dołącz](#join)
  * [RemoveDuplicates —](#removeduplicates)
  * [Podziału](#split)
* **Przepływ programu**
  * [Błąd](#error)
  * [IIF](#iif)
  * [Wybierz](#select)
  * [Przełącznika](#switch)
  * [Miejscu](#where)
  * [Się](#with)
* **Tekst**
  * [IDENT](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Lewym](#left)
  * [Funkcja](#len)
  * [Dawaj](#ltrim)
  * [Mid](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Stępować](#replace)
  * [ReplaceChars](#replacechars)
  * [Kliknij](#right)
  * [RTrim](#rtrim)
  * [Trim](#trim)
  * [UCase](#ucase)
  * [Word](#word)

---
### <a name="bitand"></a>BitAnd
**Opis:**  
Funkcja BitAnd ustawia określoną liczbę bitów w wartości.

**Obowiązuje**  
`num BitAnd(num value1, num value2)`

* wartość1, wartość2: wartości liczbowe, które powinny być AND'ed razem

**Uwagi**  
Ta funkcja konwertuje oba parametry na reprezentację binarną i ustawia bit na:

* 0 — Jeśli jedna lub obie odpowiadające bity w *wartość1* i *wartość2* są równe 0
* 1 — Jeśli obie odpowiadające bity są 1.

Innymi słowy zwraca 0 we wszystkich przypadkach, z wyjątkiem sytuacji, gdy odpowiadające im bity obu parametrów są 1.

**Przykład:**  
`BitAnd(&HF, &HF7)`  
Zwraca wartość 7, ponieważ w liczbie szesnastkowej "F" i "F7" można obliczyć tę wartości.

---
### <a name="bitor"></a>BitOr
**Opis:**  
Funkcja BitOr ustawia określoną liczbę bitów w wartości.

**Obowiązuje**  
`num BitOr(num value1, num value2)`

* wartość1, wartość2: wartości liczbowe, które powinny być OR'ed razem

**Uwagi**  
Ta funkcja konwertuje oba parametry na reprezentację binarną i ustawia bit na 1, jeśli co najmniej jeden z odpowiednich bitów w masce i flagi ma wartość 1, i wartość 0, jeśli oba odpowiednie bity są równe 0. Innymi słowy zwraca 1 we wszystkich przypadkach, z wyjątkiem sytuacji, gdy odpowiadające im bity obu parametrów są równe 0.

---
### <a name="cbool"></a>CBool
**Opis:**  
Funkcja CBool zwraca wartość logiczną opartą na obliczanym wyrażeniu

**Obowiązuje**  
`bool CBool(exp Expression)`

**Uwagi**  
Jeśli wyrażenie daje w wyniku wartość różną od zera, Funkcja CBool zwraca wartość true, w przeciwnym razie zwraca wartość false.

**Przykład:**  
`CBool([attrib1] = [attrib2])`  

Zwraca wartość true, jeśli oba atrybuty mają tę samą wartość.

---
### <a name="cdate"></a>CDate
**Opis:**  
Funkcja CDate zwraca datę i godzinę typu UTC z ciągu. Element DateTime nie jest typem atrybutu natywnego w synchronizacji, ale jest używany przez niektóre funkcje.

**Obowiązuje**  
`dt CDate(str value)`

* Wartość: ciąg zawierający datę, godzinę i opcjonalnie strefę czasową

**Uwagi**  
Zwrócony ciąg jest zawsze w formacie UTC.

**Przykład:**  
`CDate([employeeStartTime])`  
Zwraca datę i godzinę na podstawie czasu rozpoczęcia pracownika.

`CDate("2013-01-10 4:00 PM -8")`  
Zwraca datę i godzinę reprezentującą wartość "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOids
**Opis:**  
Zwraca wartości OID wszystkich krytycznych rozszerzeń obiektu certyfikatu.

**Obowiązuje**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certformat"></a>CertFormat
**Opis:**  
Zwraca nazwę formatu tego certyfikatu X. 509v3.

**Obowiązuje**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Opis:**  
Zwraca skojarzony alias certyfikatu.

**Obowiązuje**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certhashstring"></a>CertHashString
**Opis:**  
Zwraca wartość skrótu SHA1 dla certyfikatu X. 509v3 jako ciąg szesnastkowy.

**Obowiązuje**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certissuer"></a>CertIssuer
**Opis:**  
Zwraca nazwę urzędu certyfikacji, który wystawił certyfikat X. 509v3.

**Obowiązuje**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Opis:**  
Zwraca nazwę wyróżniającą wystawcy certyfikatu.

**Obowiązuje**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certissueroid"></a>CertIssuerOid
**Opis:**  
Zwraca identyfikator OID wystawcy certyfikatu.

**Obowiązuje**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Opis:**  
Zwraca informacje o algorytmie klucza dla tego certyfikatu X. 509v3 jako ciąg.

**Obowiązuje**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Opis:**  
Zwraca parametry algorytmu klucza dla certyfikatu X. 509v3 jako ciąg szesnastkowy.

**Obowiązuje**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certnameinfo"></a>CertNameInfo
**Opis:**  
Zwraca nazwy podmiotu i wystawcy z certyfikatu.

**Obowiązuje**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.
*   X509NameType: wartość X509NameType podmiotu.
*   includesIssuerName: true, aby uwzględnić nazwę wystawcy; w przeciwnym razie false.

---
### <a name="certnotafter"></a>CertNotAfter
**Opis:**  
Zwraca datę w czasie lokalnym, po której certyfikat nie jest już ważny.

**Obowiązuje**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certnotbefore"></a>CertNotBefore
**Opis:**  
Zwraca datę w lokalnym czasie, w którym certyfikat jest ważny.

**Obowiązuje**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Opis:**  
Zwraca identyfikator OID klucza publicznego dla certyfikatu X. 509v3.

**Obowiązuje**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Opis:**  
Zwraca identyfikator OID parametrów klucza publicznego dla certyfikatu X. 509v3.

**Obowiązuje**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Opis:**  
Zwraca numer seryjny certyfikatu X. 509v3.

**Obowiązuje**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Opis:**  
Zwraca identyfikator OID algorytmu używanego do tworzenia podpisu certyfikatu.

**Obowiązuje**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certsubject"></a>CertSubject
**Opis:**  
Pobiera nazwę wyróżniającą podmiotu z certyfikatu.

**Obowiązuje**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Opis:**  
Zwraca nazwę wyróżniającą podmiotu z certyfikatu.

**Obowiązuje**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Opis:**  
Zwraca identyfikator OID nazwy podmiotu z certyfikatu.

**Obowiązuje**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certthumbprint"></a>CertThumbprint
**Opis:**  
Zwraca odcisk palca certyfikatu.

**Obowiązuje**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="certversion"></a>CertVersion
**Opis:**  
Zwraca wersję formatu X. 509 certyfikatu.

**Obowiązuje**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.

---
### <a name="cguid"></a>CGuid
**Opis:**  
Funkcja CGuid konwertuje ciąg reprezentujący identyfikator GUID na jego reprezentację binarną.

**Obowiązuje**  
`bin CGuid(str GUID)`

* Ciąg sformatowany w tym wzorcu: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Contains
**Opis:**  
Funkcja Contains znajduje ciąg wewnątrz atrybutu wielowartościowego

**Obowiązuje**  
`num Contains (mvstring attribute, str search)` — z uwzględnieniem wielkości liter  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` — z uwzględnieniem wielkości liter

* attribute: Atrybut wielowartościowy do wyszukania.
* Search: ciąg do znalezienia w atrybucie.
* Casetype: CaseInsensitive lub CaseSensitive.

Zwraca indeks w atrybucie wielowartościowym, w którym znaleziono ciąg. Zwraca wartość 0, jeśli nie znaleziono ciągu.

**Uwagi**  
W przypadku atrybutów ciągu wielowartościowego wyszukiwanie wyszukuje podciągi w wartości.  
W przypadku atrybutów odwołań przeszukiwany ciąg musi dokładnie pasować do wartości, która zostanie uznana za dopasowanie.

**Przykład:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Jeśli atrybut proxyAddresses ma podstawowy adres e-mail (oznaczony wielkimi literami "SMTP:"), a następnie zwróci atrybut proxyAddress, w przeciwnym razie zwraca błąd.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Opis:**  
Funkcja ConvertFromBase64 konwertuje określoną zakodowaną wartość Base64 na zwykły ciąg.

**Obowiązuje**  
`str ConvertFromBase64(str source)` -zakłada, że kodowanie Unicode  
`str ConvertFromBase64(str source, enum Encoding)`

* Źródło: zakodowany ciąg Base64  
* Kodowanie: Unicode, ASCII, UTF8

**Przykład**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba przykłady zwracają "*Hello World!*"

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Opis:**  
Funkcja ConvertFromUTF8Hex konwertuje określoną zakodowaną szesnastkową wartość UTF8 na ciąg.

**Obowiązuje**  
`str ConvertFromUTF8Hex(str source)`

* Źródło: 2 bajty zakodowane UTF8 Sting

**Uwagi**  
Różnica między tą funkcją i ConvertFromBase64 ([], UTF8) w tym, że wynik jest przyjazny dla atrybutu DN.  
Ten format jest używany przez Azure Active Directory jako nazwę wyróżniającą.

**Przykład:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Zwraca "*Hello World!*"

---
### <a name="converttobase64"></a>ConvertToBase64
**Opis:**  
Funkcja ConvertToBase64 konwertuje ciąg na ciąg Unicode Base64.  
Konwertuje wartość tablicy liczb całkowitych na równoważną reprezentację w postaci ciągu, która jest zakodowana przy użyciu cyfry Base-64.

**Obowiązuje**  
`str ConvertToBase64(str source)`

**Przykład:**  
`ConvertToBase64("Hello world!")`  
Zwraca wartość "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Opis:**  
Funkcja ConvertToUTF8Hex konwertuje ciąg na zakodowaną wartość szesnastkową UTF8.

**Obowiązuje**  
`str ConvertToUTF8Hex(str source)`

**Uwagi**  
Format danych wyjściowych tej funkcji jest używany przez Azure Active Directory jako format atrybutu nazwy wyróżniającej.

**Przykład:**  
`ConvertToUTF8Hex("Hello world!")`  
Zwraca 48656C6C6F20776F726C6421

---
### <a name="count"></a>Liczba
**Opis:**  
Funkcja count zwraca liczbę elementów w atrybucie wielowartościowym

**Obowiązuje**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**Opis:**  
Funkcja CNum przyjmuje ciąg i zwraca typ danych liczbowych.

**Obowiązuje**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Opis:**  
Konwertuje ciąg na atrybut Reference

**Obowiązuje**  
`ref CRef(str value)`

**Przykład:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**Opis:**  
Funkcja CStr konwertuje na typ danych ciągu.

**Obowiązuje**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* wartość: może być wartością liczbową, atrybutem odwołania lub wartością logiczną.

**Przykład:**  
`CStr([dn])`  
Może zwrócić wartość "CN = Jan, DC = contoso, DC = com"

---
### <a name="dateadd"></a>DateAdd
**Opis:**  
Zwraca datę zawierającą datę, do której został dodany określony przedział czasu.

**Obowiązuje**  
`dt DateAdd(str interval, num value, dt date)`

* Interwał: wyrażenie ciągu będące przedziałem czasu, który ma zostać dodany. Ciąg musi mieć jedną z następujących wartości:
  * RRRR — rok
  * Kwartał q
  * m mies.
  * Dzień roku
  * d dzień
  * w dniu tygodnia
  * w tygodniu TT
  * Godzina h
  * n minut
  * s sekund
* wartość: liczba jednostek, które chcesz dodać. Może być dodatnia (do uzyskania dat w przyszłości) lub ujemna (w celu uzyskania dat w przeszłości).
* Data: DateTime reprezentująca datę, do której zostanie dodany interwał.

**Przykład:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Dodaje 3 miesiące i zwraca wartość typu DateTime reprezentującą wartość "2001-04-01".

---
### <a name="datefromnum"></a>DateFromNum
**Opis:**  
Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.

**Obowiązuje**  
`dt DateFromNum(num value)`

**Przykład:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Zwraca datę i godzinę reprezentującą 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Opis:**  
Funkcja DNComponent zwraca wartość określonego składnika DN z lewej strony.

**Obowiązuje**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: atrybut Reference do zinterpretowania
* ComponentNumber: składnik w nazwie DN do zwrócenia

**Przykład:**  
`DNComponent(CRef([dn]),1)`  
Jeśli nazwa DN to "CN = Jan, OU =...", zwraca Jan

---
### <a name="dncomponentrev"></a>DNComponentRev
**Opis:**  
Funkcja DNComponentRev zwraca wartość określonego składnika DN z prawej strony (koniec).

**Obowiązuje**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: atrybut Reference do zinterpretowania
* ComponentNumber — składnik w nazwie DN do zwrócenia
* Opcje: DC — Ignoruj wszystkie składniki z "DC ="

**Przykład:**  
Jeśli DN to "CN = Jan, OU = Atlanta, OU = GA, OU = US, DC = contoso, DC = com"  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Obie zwracają nam.

---
### <a name="error"></a>Błąd
**Opis:**  
Funkcja Error służy do zwrócenia błędu niestandardowego.

**Obowiązuje**  
`void Error(str ErrorMessage)`

**Przykład:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Jeśli atrybut AccountName nie istnieje, zgłoś błąd w obiekcie.

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**Opis:**  
Funkcja EscapeDNComponent przyjmuje jeden składnik nazwy wyróżniającej i wyprowadza ją w taki sposób, aby mogła być reprezentowana w protokole LDAP.

**Obowiązuje**  
`str EscapeDNComponent(str value)`

**Przykład:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Upewnij się, że obiekt może być utworzony w katalogu LDAP, nawet jeśli atrybut displayName ma znaki, które muszą zostać zmienione w protokole LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Opis:**  
Funkcja FormatDateTime służy do formatowania daty i godziny w postaci ciągu z określonym formatem

**Obowiązuje**  
`str FormatDateTime(dt value, str format)`

* wartość: wartość w formacie daty/godziny.
* Format: ciąg reprezentujący format do przekonwertowania.

**Uwagi**  
Możliwe wartości formatu można znaleźć tutaj: [niestandardowe formaty daty i godziny dla funkcji format](/dax/custom-date-and-time-formats-for-the-format-function).

**Przykład:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Wyniki w "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Może skutkować "20140905081453.0 Z"

---
### <a name="guid"></a>Guid (identyfikator GUID)
**Opis:**  
Identyfikator GUID funkcji generuje nowy losowy identyfikator GUID

**Obowiązuje**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Opis:**  
Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.

**Obowiązuje**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* warunek: dowolna wartość lub wyrażenie, które może przyjąć wartość PRAWDA lub FAŁSZ.
* valueIfTrue: Jeśli wynikiem warunku jest wartość true, zwrócona wartość.
* valueIfFalse: Jeśli warunek ma wartość false, zwrócona wartość.

**Przykład:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Jeśli użytkownik jest informatykiem, zwraca alias użytkownika ze znakiem "t-", który został dodany do początku tego elementu, w przeciwnym razie zwraca alias użytkownika.

---
### <a name="instr"></a>InStr
**Opis:**  
Funkcja InStr Znajdowanie pierwszego wystąpienia podciągu w ciągu

**Obowiązuje**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: ciąg do przeszukania
* stringmatch: ciąg, który ma zostać znaleziony
* Początek: pozycja początkowa, aby znaleźć podciąg
* Porównanie: vbTextCompare lub vbBinaryCompare

**Uwagi**  
Zwraca pozycję, w której znaleziono podciąg lub wartość 0, jeśli nie została znaleziona.

**Przykład:**  
`InStr("The quick brown fox","quick")`  
Evalues do 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Szacuje się w 7

---
### <a name="instrrev"></a>InStrRev
**Opis:**  
Funkcja InStrRev znajduje ostatnie wystąpienie podciągu w ciągu

**Obowiązuje**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: ciąg do przeszukania
* stringmatch: ciąg, który ma zostać znaleziony
* Początek: pozycja początkowa, aby znaleźć podciąg
* Porównanie: vbTextCompare lub vbBinaryCompare

**Uwagi**  
Zwraca pozycję, w której znaleziono podciąg lub wartość 0, jeśli nie została znaleziona.

**Przykład:**  
`InStrRev("abbcdbbbef","bb")`  
Zwraca 7

---
### <a name="isbitset"></a>IsBitSet
**Opis:**  
Funkcja IsBitSet testuje, czy bit jest ustawiony

**Obowiązuje**  
`bool IsBitSet(num value, num flag)`

* wartość: wartość liczbowa, która jest szacowana. flaga: wartość liczbowa, która ma bit do obliczenia

**Przykład:**  
`IsBitSet(&HF,4)`  
Zwraca wartość true, ponieważ w wartości szesnastkowej "F" ustawiono bit "4"

---
### <a name="isdate"></a>ISDATE
**Opis:**  
Jeśli wyrażenie może być szacowane jako typ DateTime, Funkcja ISDATE daje w wyniku wartość true.

**Obowiązuje**  
`bool IsDate(var Expression)`

**Uwagi**  
Służy do określenia, czy CDate () mogą się powieść.

---
### <a name="iscert"></a>Iscert
**Opis:**  
Zwraca wartość PRAWDA, jeśli pierwotne dane można serializować do obiektu certyfikatu .NET X509Certificate2.

**Obowiązuje**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: tablica bajtów reprezentacja certyfikatu X. 509. Tablica bajtów może być binarna (DER) zakodowana lub zakodowana algorytmem Base64 danych X. 509.
---
### <a name="isempty"></a>IsEmpty
**Opis:**  
Jeśli atrybut jest obecny w CS lub MV, ale zwraca pusty ciąg, funkcja IsEmpty zwraca wartość true.

**Obowiązuje**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Opis:**  
Jeśli ciąg może zostać przekonwertowany na identyfikator GUID, funkcja IsGuid oceniła wartość true.

**Obowiązuje**  
`bool IsGuid(str GUID)`

**Uwagi**  
Identyfikator GUID jest zdefiniowany jako ciąg po jednym z wzorców: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Służy do określenia, czy CGuid () może się powieść.

**Przykład:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Jeśli StrAttribute ma format identyfikatora GUID, zwróć reprezentację binarną, w przeciwnym razie Zwróć wartość null.

---
### <a name="isnull"></a>IsNull
**Opis:**  
Jeśli wyrażenie daje w wyniku wartość null, Funkcja IsNull zwraca wartość true.

**Obowiązuje**  
`bool IsNull(var Expression)`

**Uwagi**  
W przypadku atrybutu wartość null jest wyrażana przez nieobecność atrybutu.

**Przykład:**  
`IsNull([displayName])`  
Zwraca wartość true, jeśli atrybut nie jest obecny w CS lub MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Opis:**  
Jeśli wyrażenie ma wartość null lub jest pustym ciągiem, funkcja IsNullOrEmpty zwraca wartość true.

**Obowiązuje**  
`bool IsNullOrEmpty(var Expression)`

**Uwagi**  
W przypadku atrybutu wynikiem tego jest wartość true, jeśli atrybut jest nieobecny lub jest obecny, ale jest ciągiem pustym.  
Odwrotność tej funkcji ma nazwę isobecne.

**Przykład:**  
`IsNullOrEmpty([displayName])`  
Zwraca wartość true, jeśli atrybut nie jest obecny lub jest pustym ciągiem w CS lub MV.

---
### <a name="isnumeric"></a>IsNumeric
**Opis:**  
Funkcja IsNumeric zwraca wartość logiczną wskazującą, czy wyrażenie może być oceniane jako typ liczbowy.

**Obowiązuje**  
`bool IsNumeric(var Expression)`

**Uwagi**  
Służy do określenia, czy CNum () może pomyślnie przeanalizować wyrażenie.

---
### <a name="isstring"></a>IsString
**Opis:**  
Jeśli wyrażenie może być szacowane do typu ciągu, funkcja IsString daje w wyniku wartość true.

**Obowiązuje**  
`bool IsString(var expression)`

**Uwagi**  
Służy do określenia, czy CStr () może pomyślnie przeanalizować wyrażenie.

---
### <a name="ispresent"></a>Isobecne
**Opis:**  
Jeśli wyrażenie daje w wyniku ciąg, który nie ma wartości null i nie jest pusty, funkcja isprezent zwraca wartość true.

**Obowiązuje**  
`bool IsPresent(var expression)`

**Uwagi**  
Odwrotność tej funkcji ma nazwę IsNullOrEmpty.

**Przykład:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Element
**Opis:**  
Funkcja Item zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Obowiązuje**  
`var Item(mvstr attribute, num index)`

* attribute: Atrybut wielowartościowy
* index: indeks do elementu w ciągu wielowartościowym.

**Uwagi**  
Funkcja Item jest przydatna razem z funkcją Contains, ponieważ Ostatnia funkcja zwraca indeks do elementu w atrybucie wielowartościowym.

Zgłasza błąd, jeśli indeks jest poza zakresem.

**Przykład:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Zwraca podstawowy adres e-mail.

---
### <a name="itemornull"></a>ItemOrNull
**Opis:**  
Funkcja ItemOrNull zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Obowiązuje**  
`var ItemOrNull(mvstr attribute, num index)`

* attribute: Atrybut wielowartościowy
* index: indeks do elementu w ciągu wielowartościowym.

**Uwagi**  
Funkcja ItemOrNull jest przydatna razem z funkcją Contains, ponieważ Ostatnia funkcja zwraca indeks do elementu w atrybucie wielowartościowym.

Jeśli indeks jest poza zakresem, zwraca wartość null.

---
### <a name="join"></a>Join
**Opis:**  
Funkcja Join przyjmuje ciąg o wartości wielowartościowej i zwraca ciąg jednowartościowy z określonym separatorem wstawionym między poszczególnymi elementami.

**Obowiązuje**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribute: Atrybut wielowartościowy zawierający ciągi do przyłączenia.
* ogranicznik: dowolny ciąg, używany do oddzielania podciągów w zwracanym ciągu. W przypadku pominięcia zostanie użyty znak spacji (""). Jeśli ogranicznik jest ciągiem o zerowej długości ("") lub Nothing, wszystkie elementy na liście są łączone bez ograniczników.

**Uwagi**  
Między funkcjami Join i Split występuje parzystość. Funkcja Join przyjmuje tablicę ciągów i sprzęga je przy użyciu ciągu ogranicznika, aby zwrócić pojedynczy ciąg. Funkcja Split pobiera ciąg i oddziela go od ogranicznika, aby zwracał tablicę ciągów. Jednak kluczową różnicą jest to, że sprzężenie może łączyć ciągi z dowolnym ciągiem ogranicznika, podział może tylko oddzielić ciągi przy użyciu pojedynczego ogranicznika znaków.

**Przykład:**  
`Join([proxyAddresses],",")`  
Może zwrócić: " SMTP:john.doe@contoso.com , smtp:jd@contoso.com "

---
### <a name="lcase"></a>LCase
**Opis:**  
Funkcja LCase konwertuje wszystkie znaki w ciągu na małe litery.

**Obowiązuje**  
`str LCase(str value)`

**Przykład:**  
`LCase("TeSt")`  
Zwraca "test".

---
### <a name="left"></a>Lewe
**Opis:**  
Funkcja Left Zwraca określoną liczbę znaków z lewej strony ciągu.

**Obowiązuje**  
`str Left(str string, num NumChars)`

* String: ciąg, z którego mają zostać zwrócone znaki
* NumChars: numer identyfikujący liczbę znaków do zwrócenia od początku (po lewej) ciągu

**Uwagi**  
Ciąg zawierający pierwsze znaki numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwracany ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwracany jest pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba określona w numChars, zwracany jest ciąg identyczny jak ciąg (czyli zawierający wszystkie znaki w parametrze 1).

**Przykład:**  
`Left("John Doe", 3)`  
Zwraca wartość "Joh".

---
### <a name="len"></a>Len
**Opis:**  
Funkcja len zwraca liczbę znaków w ciągu.

**Obowiązuje**  
`num Len(str value)`

**Przykład:**  
`Len("John Doe")`  
Zwraca 8

---
### <a name="ltrim"></a>Dawaj
**Opis:**  
Funkcja LTrim usuwa wiodące białe znaki z ciągu.

**Obowiązuje**  
`str LTrim(str value)`

**Przykład:**  
`LTrim(" Test ")`  
Zwraca "test"

---
### <a name="mid"></a>Mid
**Opis:**  
Funkcja MID zwraca określoną liczbę znaków z określonej pozycji w ciągu.

**Obowiązuje**  
`str Mid(str string, num start, num NumChars)`

* String: ciąg, z którego mają zostać zwrócone znaki
* Początek: numer identyfikujący pozycję początkową w ciągu, aby zwracała znaki z
* NumChars: numer identyfikujący liczbę znaków do zwrócenia z pozycji w ciągu

**Uwagi**  
Zwracaj znaki numChars zaczynające się od początku pozycji w ciągu.  
Ciąg zawierający znaki numChars od początku pozycji w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwracany ciąg wejściowy.
* Jeśli Start > długość ciągu, zwracany ciąg wejściowy.
* Jeśli Start <= 0, zwracany ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwracany jest pusty ciąg.

Jeśli w ciągu od początku pozycji nie ma numChar znaków, liczba zwracanych znaków jest większa.

**Przykład:**  
`Mid("John Doe", 3, 5)`  
Zwraca wartość "HN do".

`Mid("John Doe", 6, 999)`  
Zwraca "Nowak"

---
### <a name="now"></a>Now
**Opis:**  
Funkcja NOW zwraca wartość typu DateTime określającą bieżącą datę i godzinę zgodnie z datą i godziną systemu komputera.

**Obowiązuje**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Opis:**  
Funkcja NumFromDate zwraca datę w formacie daty usługi AD.

**Obowiązuje**  
`num NumFromDate(dt value)`

**Przykład:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Zwraca 129699324000000000

---
### <a name="padleft"></a>PadLeft
**Opis:**  
Funkcja PadLeft po lewej stronie tworzy ciąg do określonej długości przy użyciu podanego znaku dopełnienia.

**Obowiązuje**  
`str PadLeft(str string, num length, str padCharacter)`

* String: ciąg do zablokowania.
* Długość: liczba całkowita reprezentująca żądaną długość ciągu.
* padCharacter: ciąg składający się z pojedynczego znaku, który ma być używany jako znak uzupełniający

**Uwagi**

* Jeśli długość ciągu jest mniejsza niż długość, padCharacter jest wielokrotnie dołączana do początku (Left) ciągu, dopóki nie ma długości równej długości.
* PadCharacter może być znakiem spacji, ale nie może być wartością null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg jest zwracany bez zmian.
* Jeśli ciąg ma długość większą lub równą długości, zwracany jest ciąg identyczny z ciągiem.
* Jeśli długość ciągu jest mniejsza niż długość, zwracany jest nowy ciąg o żądanej długości zawierający ciąg uzupełniony padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadLeft("User", 10, "0")`  
Zwraca wartość "000000User".

---
### <a name="padright"></a>PadRight
**Opis:**  
Funkcja PadRighta po prawej stronie ciągu do określonej długości przy użyciu podanego znaku dopełnienia.

**Obowiązuje**  
`str PadRight(str string, num length, str padCharacter)`

* String: ciąg do zablokowania.
* Długość: liczba całkowita reprezentująca żądaną długość ciągu.
* padCharacter: ciąg składający się z pojedynczego znaku, który ma być używany jako znak uzupełniający

**Uwagi**

* Jeśli długość ciągu jest mniejsza niż długość, padCharacter jest wielokrotnie dołączany do końca (prawy) ciągu, dopóki nie ma długości równej długości.
* padCharacter może być znakiem spacji, ale nie może być wartością null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg jest zwracany bez zmian.
* Jeśli ciąg ma długość większą lub równą długości, zwracany jest ciąg identyczny z ciągiem.
* Jeśli długość ciągu jest mniejsza niż długość, zwracany jest nowy ciąg o żądanej długości zawierający ciąg uzupełniony padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadRight("User", 10, "0")`  
Zwraca wartość "User000000".

---
### <a name="pcase"></a>PCase
**Opis:**  
Funkcja PCase konwertuje pierwszy znak każdego wyrazu rozdzielanego spacją w ciągu na wielkie litery, a wszystkie inne znaki są konwertowane na małe litery.

**Obowiązuje**  
`String PCase(string)`

**Uwagi**

* Ta funkcja obecnie nie zapewnia właściwej wielkości liter, aby przekonwertować wyraz, który jest całkowicie pisany wielkimi literami, na przykład akronimem.

**Przykład:**  
`PCase("TEsT")`  
Zwraca "test".

`PCase(LCase("TEST"))`  
Zwraca "test"

---
### <a name="randomnum"></a>RandomNum
**Opis:**  
Funkcja RandomNum zwraca liczbę losową z określonego interwału.

**Obowiązuje**  
`num RandomNum(num start, num end)`

* Początek: numer identyfikujący dolny limit losowej wartości do wygenerowania
* koniec: numer identyfikujący górny limit losowej wartości do wygenerowania

**Przykład:**  
`Random(100,999)`  
Może zwrócić 734.

---
### <a name="removeduplicates"></a>RemoveDuplicates —
**Opis:**  
Funkcja RemoveDuplicates — przyjmuje ciąg o wartości wielowartościowej i upewnij się, że każda wartość jest unikatowa.

**Obowiązuje**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Przykład:**  
`RemoveDuplicates([proxyAddresses])`  
Zwraca oczyszczony atrybut proxyAddress, w którym wszystkie zduplikowane wartości zostały usunięte.

---
### <a name="replace"></a>Zamień
**Opis:**  
Funkcja Replace zastępuje wszystkie wystąpienia ciągu do innego ciągu.

**Obowiązuje**  
`str Replace(str string, str OldValue, str NewValue)`

* ciąg: ciąg, w którym mają zostać zamienione wartości.
* OldValue: ciąg do wyszukania i zastąpienia.
* NewValue: ciąg, który ma zostać zamieniony.

**Uwagi**  
Funkcja rozpoznaje następujące specjalne monikery:

* \n — nowy wiersz
* \r — znak powrotu karetki
* \t — karta

**Przykład:**  
`Replace([address],"\r\n",", ")`  
Zamienia wartość CRLF na przecinek i spację i może prowadzić do "jeden Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>ReplaceChars
**Opis:**  
Funkcja ReplaceChars zastępuje wszystkie wystąpienia znaków Znalezione w ciągu ReplacePattern.

**Obowiązuje**  
`str ReplaceChars(str string, str ReplacePattern)`

* ciąg: ciąg, w którym mają zostać zamienione znaki.
* ReplacePattern: ciąg zawierający słownik ze znakami do zamiany.

Format to {source1}: {nazwach Target1}, {SOURCE2}: {TARGET2}, {sourceN}, {targetN}, gdzie Source to znak, który ma zostać znaleziony i który jest celem zamiany ciągu.

**Uwagi**

* Funkcja przyjmuje każde wystąpienie określonych źródeł i zastępuje je obiektami docelowymi.
* Źródło musi zawierać dokładnie jeden znak (Unicode).
* Źródło nie może być puste ani zawierać więcej niż jednego znaku (błąd analizy).
* Element docelowy może mieć wiele znaków, na przykład ö: OE, β: SS.
* Element docelowy może być pusty, co oznacza, że znak powinien zostać usunięty.
* W źródle jest rozróżniana wielkość liter i musi to być dokładne dopasowanie.
* , (Przecinek) i: (dwukropek) są zarezerwowane i nie można ich zastąpić za pomocą tej funkcji.
* Spacje i inne znaki białe w ciągu ReplacePattern są ignorowane.

**Przykład:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Zwraca Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Zwraca wartość "ONeil", aby można było usunąć pojedynczy takt.

---
### <a name="right"></a>Prawe
**Opis:**  
Funkcja Right Zwraca określoną liczbę znaków od prawej (końca) ciągu.

**Obowiązuje**  
`str Right(str string, num NumChars)`

* String: ciąg, z którego mają zostać zwrócone znaki
* NumChars: numer identyfikujący liczbę znaków do zwrócenia z końca (po prawej) ciągu

**Uwagi**  
Znaki NumChars są zwracane z ostatniego położenia ciągu.

Ciąg zawierający ostatni numChars znaków w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwracany ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwracany jest pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba określona w NumChars, zwracany jest ciąg identyczny z ciągiem.

**Przykład:**  
`Right("John Doe", 3)`  
Zwraca wartość "Nowak".

---
### <a name="rtrim"></a>RTrim
**Opis:**  
Funkcja RTrim usuwa końcowe znaki białe z ciągu.

**Obowiązuje**  
`str RTrim(str value)`

**Przykład:**  
`RTrim(" Test ")`  
Zwraca "test".

---
### <a name="select"></a>Wybierz
**Opis:**  
Przetwórz wszystkie wartości w atrybucie wielowartościowym (lub danych wyjściowych wyrażenia) w oparciu o określoną funkcję.

**Obowiązuje**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* element: reprezentuje element w atrybucie wielowartościowym
* attribute: Atrybut wielowartościowy
* wyrażenie: Wyrażenie zwracające kolekcję wartości
* warunek: Każda funkcja, która może przetwarzać element w atrybucie

**Przykłady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Zwróć wszystkie wartości w atrybucie wielowartościowym otherPhone po usunięciu łączników (-).

---
### <a name="split"></a>Podział
**Opis:**  
Funkcja Split przyjmuje ciąg oddzielony ogranicznikiem i czyni go ciągiem wielowartościowym.

**Obowiązuje**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* wartość: ciąg z znakiem ogranicznika do oddzielenia.
* ogranicznik: pojedynczy znak, który ma być używany jako ogranicznik.
* limit: Maksymalna liczba wartości, które mogą zostać zwrócone.

**Przykład:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Zwraca ciąg wielowartościowy z 2 elementami przydatnymi dla atrybutu proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid
**Opis:**  
Funkcja StringFromGuid przyjmuje binarny identyfikator GUID i konwertuje ją na ciąg

**Obowiązuje**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Opis:**  
Funkcja StringFromSid konwertuje tablicę bajtową zawierającą identyfikator zabezpieczeń na ciąg.

**Obowiązuje**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Przełącznik
**Opis:**  
Funkcja Switch służy do zwracania pojedynczej wartości na podstawie ocenionych warunków.

**Obowiązuje**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* wyrażenie: Wyrażenie wariantowe, które ma zostać obliczone.
* Value: wartość, która ma zostać zwrócona, jeśli odpowiednie wyrażenie ma wartość true.

**Uwagi**  
Lista argumentów funkcji przełączenia składa się z par wyrażeń i wartości. Wyrażenia są oceniane od lewej do prawej i zwracana jest wartość skojarzona z pierwszym wyrażeniem, które ma zostać obliczone na wartość true. Jeśli części nie są prawidłowo sparowane, wystąpi błąd w czasie wykonywania.

Na przykład jeśli Wyr1 ma wartość true, funkcja Switch zwraca wartość wartość1. Jeśli wyrażenie-1 ma wartość false, ale wyrażenie-2 ma wartość true, przełącznik zwraca wartość-2 i tak dalej.

Przełącznik zwraca wartość Nothing, jeśli:

* Żadne wyrażenie nie ma wartości true.
* Pierwsze wyrażenie prawdziwe ma odpowiadającą mu wartość null.

Przełącznik oblicza wszystkie wyrażenia, mimo że zwraca tylko jeden z nich. Z tego powodu należy oglądać niepożądane skutki uboczne. Na przykład, jeśli Obliczanie dowolnego wyrażenia spowoduje błąd dzielenia przez zero, wystąpi błąd.

Wartość może być również funkcją błędu, która zwróci niestandardowy ciąg.

**Przykład:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Zwraca język mówiony w niektórych głównych miejscowościach, w przeciwnym razie zwraca błąd.

---
### <a name="trim"></a>Trim
**Opis:**  
Funkcja Trim usuwa wiodące i końcowe białe znaki z ciągu.

**Obowiązuje**  
`str Trim(str value)`  

**Przykład:**  
`Trim(" Test ")`  
Zwraca "test".

`Trim([proxyAddresses])`  
Usuwa spacje wiodące i końcowe dla każdej wartości w atrybucie proxyAddress.

---
### <a name="ucase"></a>UCase
**Opis:**  
Funkcja UCase konwertuje wszystkie znaki w ciągu na wielkie litery.

**Obowiązuje**  
`str UCase(str string)`

**Przykład:**  
`UCase("TeSt")`  
Zwraca "TEST".

---
### <a name="where"></a>Lokalizacja

**Opis:**  
Zwraca podzestaw wartości z atrybutu wielowartościowego (lub danych wyjściowych wyrażenia) na podstawie określonego warunku.

**Obowiązuje**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* element: reprezentuje element w atrybucie wielowartościowym
* attribute: Atrybut wielowartościowy
* warunek: dowolne wyrażenie, które może przyjmować wartość PRAWDA lub FAŁSZ
* wyrażenie: Wyrażenie zwracające kolekcję wartości

**Przykład:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Zwróć wartości certyfikatu w userCertificate atrybutu wielowartościowego, który nie wygasł.

---
### <a name="with"></a>With
**Opis:**  
Funkcja with umożliwia uproszczenie wyrażenia złożonego przy użyciu zmiennej do reprezentowania podwyrażenia, które pojawia się jeden lub więcej razy w wyrażeniu złożonym.

**Obowiązuje**
`With(var variable, exp subExpression, exp complexExpression)`  
* Zmienna: reprezentuje Podwyrażenie.
* Podwyrażenie: Podwyrażenie reprezentowane przez zmienną.
* complexExpression: wyrażenie złożone.

**Przykład:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Jest funkcjonalnie równoważny z:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Zwracające tylko niewygasłe wartości certyfikatów w atrybucie userCertificate.


---
### <a name="word"></a>Word
**Opis:**  
Funkcja słowa zwraca słowo zawarte w ciągu, w oparciu o parametry opisujące ograniczniki do użycia i numer wyrazu do zwrócenia.

**Obowiązuje**  
`str Word(str string, num WordNumber, str delimiters)`

* String: ciąg, z którego ma zostać zwrócony wyraz.
* WordNumber: numer identyfikujący, który numer wyrazu powinien zwrócić.
* Ograniczniki: ciąg reprezentujący ograniczniki, które powinny być używane do identyfikowania wyrazów

**Uwagi**  
Każdy ciąg znaków w ciągu rozdzielony przez jeden ze znaków w ogranicznikach jest identyfikowany jako wyrazy:

* Jeśli liczba < 1, zwraca pusty ciąg.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej niż liczbowe słowa lub ciąg nie zawiera słów identyfikowanych przez ograniczniki, zwracany jest pusty ciąg.

**Przykład:**  
`Word("The quick brown fox",3," ")`  
Zwraca "brązowy"

`Word("This,string!has&many separators",3,",!&#")`  
Zwróci "ma"

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Opis wyrażeń związanych z aprowizacją deklaratywną](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronizacja Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)