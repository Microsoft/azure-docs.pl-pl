---
title: Pisanie wyrażeń dla mapowań atrybutów w usłudze Azure AD
description: Dowiedz się, jak używać mapowań wyrażeń do przekształcania wartości atrybutów w akceptowalny format podczas automatycznego inicjowania obsługi administracyjnej obiektów aplikacji SaaS w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28e591234e28770a90bed827e4d36c6342661dd1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866603"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Pisanie wyrażeń na potrzeby mapowania atrybutów w usłudze Azure Active Directory
Podczas konfigurowania inicjowania obsługi administracyjnej do aplikacji SaaS, jednym z typów mapowań atrybutów, które można określić jest mapowanie wyrażeń. W tym celu należy napisać wyrażenie podobne do skryptu, które umożliwia przekształcenie danych użytkowników w formaty, które są bardziej akceptowalne dla aplikacji SaaS.

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń dla mapowań atrybutów przypomina funkcje języka Visual Basic for Applications (VBA).

* Całe wyrażenie musi być zdefiniowane w kategoriach funkcji, które składają się z nazwy, po której następują argumenty w nawiasach: <br>
  *FunctionName(`<<argument 1>>``<<argument N>>`, )*
* Możesz zagnieżdżać funkcje w sobie nawzajem. Przykład: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Można przekazać trzy różne typy argumentów do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe ciągów, które muszą być ujęte w cudzysłowy. Na przykład: "Stany Zjednoczone"
  3. Inne funkcje. Na przykład: FunctionOne(`<<argument1>>`,`<<argument2>>`FunctionTwo( ))
* W przypadku stałych ciągów, jeśli potrzebujesz ukośnika odwrotnego ( \ ) lub cudzysłowu ( " ) w ciągu, musi on zostać zmieniony z symbolem ukośnika odwrotnego ( \ ). Na przykład: "Nazwa \\firmy:\\"Contoso ""

## <a name="list-of-functions"></a>Lista funkcji
[Append](#append) &nbsp; &nbsp; [Left](#left) &nbsp; [Count](#count) &nbsp; &nbsp; [Join](#join) &nbsp; [Replace](#replace) [Item](#item) &nbsp; [Mid](#mid) &nbsp; [Not](#not) [BitAnd](#bitand) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [IIF](#iif) &nbsp; &nbsp; [CStr](#cstr) &nbsp; &nbsp; [InStr](#instr) &nbsp; &nbsp; [Guid](#guid) &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [CBool](#cbool) &nbsp; &nbsp; &nbsp; [IsPresent](#ispresent) &nbsp; [IsString](#isstring) &nbsp; [DateFromNum](#datefromnum) &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; [ConvertToBase64](#converttobase64) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [IsNull](#isnull) &nbsp; [RemoveDuplicates](#removeduplicates) [SelectUniqueValue](#selectuniquevalue) [IsNullOrEmpty](#isnullorempty) &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) Dołącz BitAnd &nbsp; &nbsp; &nbsp; &nbsp; CBool &nbsp; &nbsp; &nbsp; &nbsp; Coalesce &nbsp; &nbsp; &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; Count &nbsp; CStr &nbsp; &nbsp; &nbsp; DateFromNum &nbsp; FormatDateTime &nbsp; &nbsp; &nbsp; Guid IIF InStr &nbsp; IsNull IsNullIsty &nbsp; IsPresent IsString Item &nbsp; Join Left Mid NormalizeDiacritics Not RemoveDuplicates Replace SelectUniqueValue &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; [Word](#word) [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; Split&nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) [Switch](#switch) [ToLower](#tolower) StripSpaces Przełącz&nbsp; się doNiejsa doupper word&nbsp; &nbsp;

---
### <a name="append"></a>Append
**Funkcja:**<br> Dołącz(źródło, sufiks)

**Opis:**<br> Pobiera wartość ciągu źródłowego i dołącza sufiks na jego końcu.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **Sufiks** |Wymagany |Ciąg |Ciąg, który chcesz dołączyć na końcu wartości źródłowej. |

---
### <a name="bitand"></a>BitAnd ( BitAnd )
**Funkcja:**<br> BitAnd(wartość1, wartość2)

**Opis:**<br> Ta funkcja konwertuje oba parametry do reprezentacji binarnej i ustawia nieco na:

0 - jeśli jeden lub oba z odpowiadających im bitów w wartości1 i wartości2 są 0                                                  
1 - jeśli oba odpowiednie bity są 1.                                    

Innymi słowy zwraca 0 we wszystkich przypadkach, z wyjątkiem sytuacji, gdy odpowiednie bity obu parametrów są 1.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **wartość 1** |Wymagany |num |Wartość liczbowa, która powinna być AND'ed z wartością2|
| **wartość2** |Wymagany |num |Wartość liczbowa, która powinna być AND'ed z wartością1|

**Przykład:**<br>
BitAnd (&HF, &HF7)                                                                                
11110111 I 00000111 = 00000111 więc BitAnd zwraca 7, wartość binarną 00000111

---
### <a name="cbool"></a>Cbool
**Funkcja:**<br> CBool(wyrażenie)

**Opis:**<br> CBool zwraca wartość logiczną na podstawie obliczonego wyrażenia. Jeśli wyrażenie ma wartość inną niż zero, cBool zwraca wartość True, w przeciwnym razie zwraca false..

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Wyrażenie** |Wymagany | wyrażenie | Dowolne prawidłowe wyrażenie |

**Przykład:**<br>
CBool([atrybut1] = [atrybut2])                                                                    
Zwraca wartość True, jeśli oba atrybuty mają tę samą wartość.

---
### <a name="coalesce"></a>Coalesce
**Funkcja:**<br> Coalesce(source1, source2, ..., defaultValue)

**Opis:**<br> Zwraca pierwszą wartość źródła, która nie jest null. Jeśli wszystkie argumenty mają wartość NULL i defaultValue jest obecny, defaultValue zostaną zwrócone. Jeśli wszystkie argumenty mają wartość NULL i defaultValue nie jest obecny, Coalesce zwraca wartość NULL.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **źródło1 ... sourceN** | Wymagany | Ciąg |Wymagana, zmienna liczba razy. Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **Defaultvalue** | Optional (Opcjonalność) | Ciąg | Wartość domyślna, która ma być używana, gdy wszystkie wartości źródłowe mają wartość NULL. Może być pustym ciągiem ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Funkcja:**<br> ConvertToBase64(źródło)

**Opis:**<br> ConvertToBase64 Funkcja konwertuje ciąg do ciągu Base64 Unicode.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Ciąg do konwersji na bazę 64|

**Przykład:**<br>
ConvertToBase64("Witaj świecie!")                                                                                                        
Zwraca wartość "SABlAGwAbVACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funkcja:**<br> ConvertToUTF8Hex(źródło)

**Opis:**<br> ConvertToUTF8Hex Funkcja konwertuje ciąg na wartość zakodowaną w hex UTF8.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Ciąg do konwersji na UTF8 Hex|

**Przykład:**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
Zwraca 48656C6C6F20776F726C6421

---
### <a name="count"></a>Liczba
**Funkcja:**<br> Liczba(atrybut)

**Opis:**<br> Funkcja Count zwraca liczbę elementów w atrybucie wielowartościowym

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Atrybut** |Wymagany | — atrybut |Atrybut wielowartościowy, który będzie liczył elementy|

---
### <a name="cstr"></a>Cstr
**Funkcja:**<br> CStr(wartość)

**Opis:**<br> Funkcja CStr konwertuje wartość na typ danych ciągu.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **value** |Wymagany | numeryczne, referencyjne lub logiczne | Może to być wartość liczbowa, atrybut odwołania lub wartość logiczna. |

**Przykład:**<br>
CStr([dn])                                                            
Zwraca wartość "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DataFromNum
**Funkcja:**<br> DateFromNum(wartość)

**Opis:**<br> Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **value** |Wymagany | Date | Data usługi AD do przekonwertowania na typ DateTime |

**Przykład:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Zwraca datetime reprezentujący 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>Formatdatetime
**Funkcja:**<br> FormatDateTime(źródło, inputFormat, outputFormat)

**Opis:**<br> Pobiera ciąg daty z jednego formatu i konwertuje go na inny format.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **inputFormat** |Wymagany |Ciąg |Oczekiwany format wartości źródłowej. Aby uzyskać obsługiwane formaty, zobacz [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **wyjścieFormat** |Wymagany |Ciąg |Format daty wyjściowej. |

---
### <a name="guid"></a>Guid (identyfikator GUID)
**Funkcja:**<br> Guid()

**Opis:**<br> Funkcja Guid generuje nowy losowy identyfikator GUID

---
### <a name="iif"></a>Iif
**Funkcja:**<br> IIF(warunek,wartośćIfTrue,wartośćIfFalse)

**Opis:**<br> Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Warunek** |Wymagany |Zmienna lub wyrażenie |Dowolna wartość lub wyrażenie, które można ocenić na wartość true lub false. |
| **wartośćIfTrue** |Wymagany |Zmienna lub ciąg | Jeśli warunek ma wartość true, zwracana wartość. |
| **wartośćIfFalse** |Wymagany |Zmienna lub ciąg |Jeśli warunek ma wartość false, zwracana wartość.|

**Przykład:**<br>
IIF([kraj]="USA",[kraj],[dział])

---
### <a name="instr"></a>Instr
**Funkcja:**<br> InStr(wartość1,wartość2,start,compareType)

**Opis:**<br> Funkcja InStr znajduje pierwsze wystąpienie podciągu w ciągu

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **wartość 1** |Wymagany |Ciąg |Ciąg do przeszukania |
| **wartość2** |Wymagany |Ciąg |Ciąg do znalezienia |
| **Uruchomić** |Optional (Opcjonalność) |Liczba całkowita |Pozycja początkowa w celu znalezienia podciągu|
| **porównajType** |Optional (Opcjonalność) |Wyliczenie |Może być vbTextCompare lub vbBinaryCompare |

**Przykład:**<br>
InStr("Szybki brązowy lis","szybki")                                                                             
Evalues do 5

InStr("repEated",,"e",3,vbBinaryCompare)                                                                                  
Ocenia do 7

---
### <a name="isnull"></a>Isnull
**Funkcja:**<br> IsNull(wyrażenie)

**Opis:**<br> Jeśli wyrażenie ma wartość Null, funkcja IsNull zwraca wartość true. Dla atrybutu Null jest wyrażona przez brak atrybutu.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Wyrażenie** |Wymagany |wyrażenie |Wyrażenie do oceny |

**Przykład:**<br>
IsNull([nazwa wyświetlania])                                                                                                
Zwraca wartość True, jeśli atrybut nie jest obecny

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funkcja:**<br> IsNullOrEmpty(wyrażenie)

**Opis:**<br> Jeśli wyrażenie ma wartość null lub pusty ciąg, funkcja IsNullOrEmpty zwraca wartość true. Dla atrybutu to ocenić True, jeśli atrybut jest nieobecny lub jest obecny, ale jest pusty ciąg.
Odwrotność tej funkcji nosi nazwę IsPresent.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Wyrażenie** |Wymagany |wyrażenie |Wyrażenie do oceny |

**Przykład:**<br>
IsNullOrEmpty([nazwa wyświetlania])                                               
Zwraca wartość True, jeśli atrybut nie jest obecny lub jest pustym ciągiem

---
### <a name="ispresent"></a>Ispresent
**Funkcja:**<br> IsPresent(wyrażenie)

**Opis:**<br> Jeśli wyrażenie ma wartość ciągu, który nie jest null i nie jest pusty, a następnie IsPresent funkcja zwraca true. Odwrotność tej funkcji nosi nazwę IsNullOrEmpty.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Wyrażenie** |Wymagany |wyrażenie |Wyrażenie do oceny |

**Przykład:**<br>
Switch(IsPresent([directManager])[directManager], IsPresent([skiplevelManager])[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString (IsString)
**Funkcja:**<br> IsString(wyrażenie)

**Opis:**<br> Jeśli wyrażenie można ocenić do typu ciągu, a następnie IsString funkcja ocenia True.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Wyrażenie** |Wymagany |wyrażenie |Wyrażenie do oceny |

---
### <a name="item"></a>Element
**Funkcja:**<br> Element(atrybut, indeks)

**Opis:**<br> Funkcja Element zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Atrybut** |Wymagany |Atrybut |Atrybut o wielu wartościach, który ma zostać przeszukany |
| **Indeks** |Wymagany |Liczba całkowita | Indeks do elementu w ciągu wielowartościowym|

**Przykład:**<br>
Pozycja([proxyAddresses], 1)

---
### <a name="join"></a>Join
**Funkcja:**<br> Sprzężenie(separator, source1, source2, ...)

**Opis:**<br> Join() jest podobny do Append(), z tą różnicą, że może łączyć wiele wartości **ciągów źródłowych** w jeden ciąg, a każda wartość zostanie oddzielona ciągiem **separatora.**

Jeśli jedna z wartości źródłowych jest atrybutem wielowartościowym, każda wartość w tym atrybucie zostanie połączona, oddzielona wartością separatora.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Separator** |Wymagany |Ciąg |Ciąg używany do oddzielania wartości źródłowych, gdy są one łączone w jeden ciąg. Może być "", jeśli separator nie jest wymagany. |
| **źródło1 ... sourceN** |Wymagana, zmienna liczba razy |Ciąg |Wartości ciągu, które mają być połączone ze sobą. |

---
### <a name="left"></a>Lewe
**Funkcja:**<br> Po lewej stronie(ciąg,NumChars)

**Opis:**<br> Funkcja Left zwraca określoną liczbę znaków z lewej strony ciągu. Jeśli numChars = 0, zwraca pusty ciąg.
Jeśli numChars < 0, zwraca ciąg wejściowy.
Jeśli ciąg ma wartość null, zwróć pusty ciąg.
Jeśli ciąg zawiera mniej znaków niż liczba określona w numChars, zwracany jest ciąg identyczny z ciągiem (czyli zawierający wszystkie znaki w parametrze 1).

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Ciąg** |Wymagany |Atrybut | Ciąg do zwracania znaków z |
| **NumChars ( NumChars )** |Wymagany |Liczba całkowita | Liczba identyfikującya liczbę znaków do zwrócenia od początku (po lewej) ciągu|

**Przykład:**<br>
Po lewej("John Doe", 3)                                                            
Zwraca "Joh"

---
### <a name="mid"></a>Mid
**Funkcja:**<br> Środek (źródło, początek, długość)

**Opis:**<br> Zwraca podciąg wartości źródłowej. Podciąg to ciąg, który zawiera tylko niektóre znaki z ciągu źródłowego.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu. |
| **Uruchomić** |Wymagany |liczba całkowita |Indeks w ciągu **źródłowym,** gdzie podciąg powinien się rozpocząć. Pierwszy znak w ciągu będzie miał indeks 1, drugi znak będzie miał indeks 2 i tak dalej. |
| **Długość** |Wymagany |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza **ciągiem źródłowym,** funkcja zwróci podciąg z indeksu **startowego** do końca ciągu **źródłowego.** |

---
### <a name="normalizediacritics"></a>NormalizeDiakryty
**Funkcja:**<br> NormalizeDiacritics(źródło)

**Opis:**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale z dowolnymi znakami diakrytycznymi zastąpionymi równoważnymi znakami niekrytycznymi. Zazwyczaj używane do konwertowania imion i nazwisk zawierających znaki diakrytyczne (znaki akcentujące) na wartości prawne, które mogą być używane w różnych identyfikatorach użytkowników, takich jak główne nazwy użytkowników, nazwy kont SAM i adresy e-mail.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg | Zwykle atrybut imienia lub nazwiska. |

---
### <a name="not"></a>Not
**Funkcja:**<br> Not(źródło)

**Opis:**<br> Odwraca wartość logiczną **źródła**. Jeśli wartość **źródła** to "*True*", zwraca "*False*". W przeciwnym razie zwraca "*True*".

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg logiczny |Oczekiwane wartości **źródłowe** to "True" lub "False". |

---
### <a name="numfromdate"></a>NumFromDate (NumFromDate)
**Funkcja:**<br> NumFromDate(wartość)

**Opis:**<br> Funkcja NumFromDate konwertuje wartość DateTime na format usługi Active Directory, która jest wymagana do ustawienia atrybutów takich jak [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Ta funkcja służy do konwertowania wartości DateTime odebranych z aplikacji hr w chmurze, takich jak Workday i SuccessFactors, na ich równoważną reprezentację usługi AD. 

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **value** |Wymagany | Ciąg | Ciąg daty i godziny w obsługiwanym formacie. Aby uzyskać obsługiwane formaty, zobacz https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Przykład:**<br>
* Przykład dnia roboczego <br>
  Zakładając, że chcesz mapować atrybut *ContractEndDate* z dnia roboczego, który jest w formacie *2020-12-31-08:00* do *accountExpires* pola w ucho, oto jak można użyć tej funkcji i zmienić przesunięcie strefy czasowej, aby dopasować ustawienia regionalne. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Przykład SuccessFactors <br>
  Zakładając, że chcesz mapować atrybut *endDate* z SuccessFactors, który jest w formacie *M/d/yyyy hh:mm:ss tt* do *accountExpires* pola w AD, oto jak można użyć tej funkcji i zmienić przesunięcie strefy czasowej, aby dopasować ustawienia regionalne.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>Usuńduplika
**Funkcja:**<br> RemoveDuplicates(atrybut)

**Opis:**<br> RemoveDuplicates Funkcja przyjmuje ciąg wielowartościowy i upewnij się, że każda wartość jest unikatowa.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Atrybut** |Wymagany |Atrybut o wielu wartościach |Atrybut o wielu wartościach, który będzie miał usunięte duplikaty|

**Przykład:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
Zwraca zdezynfekowany atrybut proxyAddress, w którym usunięto wszystkie zduplikowane wartości

---
### <a name="replace"></a>Replace
**Funkcja:**<br> Zamień(źródło, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, szablon)

**Opis:**<br>
Zastępuje wartości w ciągu. Działa inaczej w zależności od podanych parametrów:

* Gdy **oldValue** i **replacementValue** są dostarczane:
  
  * Zastępuje wszystkie wystąpienia **staregoValue** w **źródle** **z replacementValue**
* Gdy **stareWartość** i **szablon** są dostarczane:
  
  * Zastępuje wszystkie wystąpienia **starejWartość** w **szablonie** wartością **źródłową**
* Gdy **regexPattern** i **replacementValue** są dostarczane:

  * Funkcja stosuje **wyrażenie regularnePattern** do ciągu **źródłowego** i można użyć nazw grup **replacementValue** odc.
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** są dostarczane:
  
  * Funkcja stosuje **wyrażenie regularnePattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące **do wyrażenia regularnegoGroupName** z **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementAttributeName** są dostarczane:
  
  * Jeśli **źródło** nie ma wartości, **zwracane** jest źródło
  * Jeśli **źródło** ma wartość, funkcja stosuje **wyrażenie regularnePattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące **regexGroupName** z wartością skojarzoną z **replacementAttributeName**

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu **źródłowego.** |
| **Oldvalue** |Optional (Opcjonalność) |Ciąg |Wartość do wymiany w **źródle** lub **szablonie**. |
| **regexPattern** |Optional (Opcjonalność) |Ciąg |Wzorzec regex dla wartości, która ma zostać zastąpiona w **źródle**. Lub, gdy używana jest **nazwa zastępczaPropertyName,** wzorzec wyodrębnić wartość z **replacementPropertyName**. |
| **nazwa grupy regex** |Optional (Opcjonalność) |Ciąg |Nazwa grupy wewnątrz **regexPattern**. Tylko wtedy, gdy używana jest **nazwa zastępczaPropertyName,** wyodrębnimy wartość tej grupy jako **replacementValue** z **replacementPropertyName**. |
| **wymianaValue** |Optional (Opcjonalność) |Ciąg |Nowa wartość, aby zastąpić stary. |
| **replacementAttributeName** |Optional (Opcjonalność) |Ciąg |Nazwa atrybutu, który ma być używany dla wartości zastępczej |
| **Szablonu** |Optional (Opcjonalność) |Ciąg |Gdy wartość **szablonu** jest podana, będziemy szukać **oldValue** wewnątrz szablonu i zastąpić go wartością **źródłową.** |

---
### <a name="selectuniquevalue"></a>Wybierz Wartość Nieoczyszka
**Funkcja:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Opis:**<br> Wymaga co najmniej dwóch argumentów, które są regułami generowania unikatowych wartości zdefiniowanych przy użyciu wyrażeń. Funkcja ocenia każdą regułę, a następnie sprawdza wartość wygenerowaną pod kątem unikatowości w docelowej aplikacji/katalogu. Pierwszą znalezioną unikatową wartością będzie ta zwrócona. Jeśli wszystkie wartości już istnieją w docelowych, wpis zostanie zdeesowany i powód zostanie zarejestrowany w dziennikach inspekcji. Nie ma górnej granicy liczby argumentów, które mogą być dostarczone.


 - Jest to funkcja najwyższego poziomu, nie może być zagnieżdżona.
 - Tej funkcji nie można zastosować do atrybutów, które mają pasujące pierwszeństwo.   
 - Ta funkcja jest przeznaczona tylko do tworzenia wpisu. Podczas używania go z atrybutem ustaw właściwość **Zastosuj mapowanie** **na Tylko podczas tworzenia obiektu**.
 - Ta funkcja jest obecnie obsługiwana tylko dla "Workday to Active Directory User Provisioning". Nie można używać z innymi aplikacjami inicjowania obsługi administracyjnej. 


**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Wymagane są co najmniej 2, nie |Ciąg | Lista unikatowych reguł generowania wartości do oceny. |


---
### <a name="singleapproleassignment"></a>Znak SingleAppRoleAssignment
**Funkcja:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Opis:**<br> Zwraca pojedynczą przypisanie aplikacjiRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do konwersji appRoleAssignments obiektu do ciągu nazwy pojedynczej roli. Należy zauważyć, że najlepszym rozwiązaniem jest zapewnienie tylko jeden appRoleAssignment jest przypisany do jednego użytkownika w czasie, a jeśli wiele ról są przypisane ciąg roli zwracany może nie być przewidywalne. 

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Wymagany |Ciąg |**[appRoleAssignments]** obiekt. |

---
### <a name="split"></a>Podział
**Funkcja:**<br> Split(źródło, ogranicznik)

**Opis:**<br> Dzieli ciąg na tablicę wielowartościową, używając określonego znaku ogranicznika.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |wartości **źródłowego** do aktualizacji. |
| **Ogranicznik** |Wymagany |Ciąg |Określa znak, który będzie używany do dzielenia ciągu (przykład: ",") |

---
### <a name="stripspaces"></a>StripSpaces (Przestrzenie ze striptizem
**Funkcja:**<br> StripSpaces(źródło)

**Opis:**<br> Usuwa wszystkie znaki spacji (" ") z ciągu źródłowego.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |wartości **źródłowego** do aktualizacji. |

---
### <a name="switch"></a>Przełącznik
**Funkcja:**<br> Przełącznik(źródło, defaultValue, key1, value1, key2, value2, ...)

**Opis:**<br> Gdy wartość **źródłowa** jest zgodna z **kluczem,** zwraca **wartość** tego **klucza**. Jeśli wartość **źródła** nie jest zgodna z żadnymi kluczami, zwraca **wartość domyślną**.  **Parametry** **klucza** i wartości muszą zawsze być w parach. Funkcja zawsze oczekuje parzyste liczby parametrów. Funkcja nie powinna być używana dla atrybutów referencyjnych, takich jak menedżer. 

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |**Wartość źródła** do aktualizacji. |
| **Defaultvalue** |Optional (Opcjonalność) |Ciąg |Wartość domyślna, która ma być używana, gdy źródło nie pasuje do żadnych kluczy. Może być pustym ciągiem (""). |
| **key** |Wymagany |Ciąg |**Klucz** do porównania wartości **źródłowych** z. |
| **value** |Wymagany |Ciąg |Wartość zastępcza dla **źródła** pasującego do klucza. |

---
### <a name="tolower"></a>Tolower
**Funkcja:**<br> ToLower(źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje go na małe litery przy użyciu reguł kultury, które są określone. Jeśli nie określono żadnych informacji o *kulturze,* użyje ono kultury niezmiennej.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego |
| **kultura** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury na podstawie RFC 4646 to *languagecode2-country/regioncode2*, gdzie *languagecode2* jest dwuliterowym kodem języka, a *kod kraju/regionu2* jest dwuliterowym kodem subkultury. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i en-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy dwuliterowy kod języka nie jest dostępny, używany jest trzyliterowy kod pochodzący z iso 639-2.|

---
### <a name="toupper"></a>Toupper
**Funkcja:**<br> ToUpper(źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje go na wielkie litery przy użyciu reguł kultury, które są określone. Jeśli nie określono żadnych informacji o *kulturze,* użyje ono kultury niezmiennej.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **kultura** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury na podstawie RFC 4646 to *languagecode2-country/regioncode2*, gdzie *languagecode2* jest dwuliterowym kodem języka, a *kod kraju/regionu2* jest dwuliterowym kodem subkultury. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i en-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy dwuliterowy kod języka nie jest dostępny, używany jest trzyliterowy kod pochodzący z iso 639-2.|

---
### <a name="word"></a>Word
**Funkcja:**<br> Słowo(ciąg,Liczba słów,Ograniczniki)

**Opis:**<br> Funkcja Word zwraca wyraz zawarty w ciągu, na podstawie parametrów opisujących ograniczniki do użycia i numer wyrazu do zwrócenia. Każdy ciąg znaków w ciągu oddzielony jednym ze znaków w ogranicznikach są identyfikowane jako słowa:

Jeśli liczba < 1, zwraca pusty ciąg.
Jeśli ciąg ma wartość null, zwraca pusty ciąg.
Jeśli ciąg zawiera mniej niż słowa liczbowe lub ciąg nie zawiera żadnych słów zidentyfikowanych przez ograniczniki, zwracany jest pusty ciąg.

**Parametry:**<br> 

| Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
| --- | --- | --- | --- |
| **Ciąg** |Wymagany |Atrybut o wielu wartościach |Ciąg, aby zwrócić słowo z.|
| **Liczba słów** |Wymagany | Liczba całkowita | Numer identyfikujący numer słowa powinien zostać odesłany|
| **Ograniczniki** |Wymagany |Ciąg| Ciąg reprezentujący ogranicznik(y), który powinien być używany do identyfikowania wyrazów|

**Przykład:**<br>
Słowo("Szybki brązowy lis", 3", ")                                                                                       
Zwraca "brązowy"

Word("This,string!has&wiele separatorów",3,",!&#")                                                                       
Zwraca "ma"

---

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Rozsyłanie znanej nazwy domeny
Aby uzyskać nazwę użytkownika, musisz usunąć znaną nazwę domeny z wiadomości e-mail użytkownika. <br>
Na przykład, jeśli domena jest "contoso.com", można użyć następującego wyrażenia:

**Wyrażenie:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe wejście / wyjście:** <br>

* **WEJŚCIE** (poczta):john.doe@contoso.com" "
* **WYJŚCIE:**"john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Dołączanie stałego sufiksu do nazwy użytkownika
Jeśli używasz piaskownicy Salesforce, może być konieczne dołączenie dodatkowego sufiksu do wszystkich nazw użytkowników przed ich synchronizacją.

**Wyrażenie:** <br>
`Append([userPrincipalName], ".test")`

**Przykładowe wejście/wyjście:** <br>

* **WEJŚCIE**: (userPrincipalName): "John.Doe@contoso.com"
* **WYJŚCIE**:John.Doe@contoso.com.test" "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie aliasu użytkownika przez łączenie części imienia i nazwiska
Musisz wygenerować alias użytkownika, biorąc pierwsze 3 litery imienia użytkownika i pierwsze 5 liter nazwiska użytkownika.

**Wyrażenie:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Przykładowe wejście/wyjście:** <br>

* **INPUT** (givenName): "John"
* **INPUT** (nazwisko): "Doe"
* **WYJŚCIE:**"JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuwanie znaków diakrutycznych z ciągu
Należy zastąpić znaki zawierające znaki akcentu równoważnymi znakami, które nie zawierają znaczników akcentu.

**Wyrażenie:** <br>
NormalizeDiacritics([givenName])

**Przykładowe wejście/wyjście:** <br>

* **INPUT** (givenName): "Zoë"
* **WYJŚCIE**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dzielenie ciągu na tablicę o wielu wartościach
Należy wziąć listę ciągów rozdzielanych przecinkami i podzielić je na tablicę, którą można podłączyć do atrybutu wielowartościowego, takiego jak atrybut Uprawnienia Zestawy uprawnień Salesforce. W tym przykładzie lista zestawów uprawnień została wypełniona w extensionAttribute5 w usłudze Azure AD.

**Wyrażenie:** <br>
Split([extensionAttribute5], ",")

**Przykładowe wejście/wyjście:** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **WYJŚCIE**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data wyjściowa jako ciąg znaków w określonym formacie
Chcesz wysłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład chcesz sformatować daty servicenow.

**Wyrażenie:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe wejście/wyjście:**

* **INPUT** (extensionAttribute1): "20150123105347.1Z"
* **WYJŚCIE**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastępowanie wartości na podstawie wstępnie zdefiniowanego zestawu opcji

Należy zdefiniować strefę czasową użytkownika na podstawie kodu stanu przechowywanego w usłudze Azure AD. <br>
Jeśli kod stanu nie pasuje do żadnej ze wstępnie zdefiniowanych opcji, użyj domyślnej wartości "Australia/Sydney".

**Wyrażenie:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (stan): "QLD"
* **PRODUKCJA**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Zamienianie znaków przy użyciu wyrażenia regularnego
Musisz znaleźć znaki pasujące do wartości wyrażenia regularnego i usunąć je.

**Wyrażenie:** <br>

Zamień([mailNickname], "[a-zA-Z_]*", , "", ,

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (mailNickname: "john_doe72"
* **WYJŚCIE:**"72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konwertowanie wygenerowanej wartości userPrincipalName (UPN) na małe
W poniższym przykładzie wartość UPN jest generowana przez łączenie pól źródłowych PreferredFirstName i PreferredLastName, a funkcja ToLower działa na wygenerowanym ciągu w celu przekonwertowania wszystkich znaków na małe litery. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **WYJŚCIE**:john.smith@contoso.com" "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generowanie unikatowej wartości atrybutu userPrincipalName (UPN)
Na podstawie imienia, drugiego imienia i nazwiska użytkownika należy wygenerować wartość atrybutu UPN i sprawdzić jego unikatowość w docelowym katalogu usługi AD przed przypisaniem tej wartości do atrybutu NAZWY UPN.

**Wyrażenie:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **WYJŚCIE**:John.Smith@contoso.com" " jeśli John.Smith@contoso.com wartość UPN nie istnieje jeszcze w katalogu
* **WYJŚCIE**:J.Smith@contoso.com" " jeśli John.Smith@contoso.com wartość UPN już istnieje w katalogu
* **WYJŚCIE**:Jo.Smith@contoso.com" ", jeśli powyższe dwie wartości UPN już istnieją w katalogu

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Wartość poczty przepływu, jeśli nie NULL, w przeciwnym razie przepływ userPrincipalName
Chcesz przepływać atrybut mail, jeśli jest obecny. Jeśli tak nie jest, chcesz przepływać wartość userPrincipalName zamiast.

**Wyrażenie:** <br>
`Coalesce([mail],[userPrincipalName])`

**Przykładowe wejście/wyjście:** <br>

* **WEJŚCIE** (poczta): NULL
* **WEJŚCIE** (userPrincipalName):John.Doe@contoso.com" "
* **WYJŚCIE**:John.Doe@contoso.com" "

## <a name="related-articles"></a>Powiązane artykuły
* [Automatyzacja inicjowania obsługi administracyjnej/anulowania obsługi administracyjnej aplikacji SaaS](../app-provisioning/user-provisioning.md)
* [Dostosowywanie mapowań atrybutów do inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/customize-application-attributes.md)
* [Filtry zakresu do inicjowania obsługi administracyjnej użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Powiadomienia związane z aprowizacją kont](../app-provisioning/user-provisioning.md)
* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
