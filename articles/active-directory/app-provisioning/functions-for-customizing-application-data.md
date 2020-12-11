---
title: Odwołanie do pisania wyrażeń mapowań atrybutów w Azure Active Directory
description: Dowiedz się, jak używać mapowań wyrażeń do przekształcania wartości atrybutów w akceptowalny format podczas zautomatyzowanej aprowizacji obiektów aplikacji SaaS w Azure Active Directory. Zawiera listę odwołań do funkcji.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: fe18513bbfc69fb63f8b1b248b7cf02ad55b467d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027377"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Odwołanie do pisania wyrażeń dla mapowań atrybutów w usłudze Azure AD

Podczas konfigurowania aprowizacji do aplikacji SaaS, jeden z typów mapowań atrybutów, które można określić, jest mapowanie wyrażenia. W tym celu należy napisać wyrażenie podobne do skryptu, które pozwala na Przekształcanie danych użytkowników w formaty, które są bardziej akceptowalne dla aplikacji SaaS.

## <a name="syntax-overview"></a>Omówienie składni

Składnia wyrażeń dla mapowań atrybutów to Reminiscent of Visual Basic for Applications (VBA) Functions.

* Całe wyrażenie musi być zdefiniowane w zakresie funkcji, które składają się z nazwy, a następnie argumentów w nawiasach: *FunctionName ( `<<argument 1>>` , `<<argument N>>` )*
* Funkcje mogą być zagnieżdżane w innych. Na przykład:  *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Można przekazać trzy różne typy argumentów do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe ciągów, które muszą być ujęte w podwójne cudzysłowy. Na przykład: "Stany Zjednoczone"
  3. Inne funkcje. Na przykład: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* W przypadku stałych ciągów, jeśli potrzebujesz ukośnika odwrotnego (\) lub cudzysłowu (") w ciągu, musi to być znak ucieczki z symbolem ukośnika odwrotnego (\). Na przykład: "Nazwa firmy: \\ " contoso \\ ""
* Składnia jest rozróżniana wielkość liter, co należy wziąć pod uwagę podczas wpisywania ich jako ciągów w funkcji vs Copy, wklejając je bezpośrednio z tego miejsca. 

## <a name="list-of-functions"></a>Lista funkcji

[](#append) &nbsp; &nbsp; Dołącz &nbsp; &nbsp; [](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [](#coalesce) &nbsp; &nbsp; Połączenie &nbsp; &nbsp; [](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [](#count) &nbsp; &nbsp; Liczba &nbsp; &nbsp; [](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [Identyfikator](#guid) &nbsp; &nbsp; GUID &nbsp; &nbsp; [](#iif) &nbsp; &nbsp; IIf &nbsp; &nbsp; [](#instr) &nbsp; &nbsp; Instr &nbsp; &nbsp; [](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; [](#ispresent) &nbsp; &nbsp; Isobecne &nbsp; &nbsp; [](#isstring) &nbsp; &nbsp; IsString &nbsp; &nbsp; [](#item) &nbsp; &nbsp; Element &nbsp; &nbsp; [Dołącz](#join) &nbsp; &nbsp; do &nbsp; &nbsp; [](#left) &nbsp; &nbsp; Z &nbsp; lewej &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates —](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Append

**Funkcja:** Dołącz (Źródło, sufiks)

**Opis:** Pobiera wartość ciągu źródłowego i dołącza jej sufiks do końca.

**Wejściowe**

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu w obiekcie źródłowym. |
| **przedrostk** |Wymagane |Ciąg |Ciąg, który ma zostać dołączony do końca wartości źródłowej. |

---
### <a name="bitand"></a>BitAnd
**Funkcja:** BitAnd (wartość1, wartość2)

**Opis:** Ta funkcja konwertuje oba parametry na reprezentację binarną i ustawia bit na:

- 0 — Jeśli jedna lub obie odpowiadające bity w wartość1 i wartość2 są równe 0
- 1 — Jeśli obie odpowiadające bity są 1.

Innymi słowy zwraca 0 we wszystkich przypadkach, z wyjątkiem sytuacji, gdy odpowiadające im bity obu parametrów są 1.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **sekwencj** |Wymagane |num |Wartość liczbowa, która powinna być AND'ed z wartość2|
| **wartość2** |Wymagane |num |Wartość liczbowa, która powinna być AND'ed z wartość1|

**Przyklad**
`BitAnd(&HF, &HF7)`

11110111 i 00000111 = 00000111, więc `BitAnd` zwraca 7, wartość binarną 00000111.

---
### <a name="cbool"></a>CBool
**Funkcyjn** 
`CBool(Expression)`

**Opis:**  
 `CBool` Zwraca wartość logiczną opartą na obliczonym wyrażeniu. Jeśli wyrażenie daje w wyniku wartość różną od zera, a następnie `CBool` zwraca wartość *true*, w przeciwnym razie zwraca *wartość false*.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **expression** |Wymagane | expression | Dowolne prawidłowe wyrażenie |

**Przykład:** 
`CBool([attribute1] = [attribute2])`                                                                    
Zwraca wartość true, jeśli oba atrybuty mają tę samą wartość.

---
### <a name="coalesce"></a>Coalesce
**Funkcja:** Połączenie (source1, SOURCE2,..., DefaultValue)

**Opis:** Zwraca pierwszą wartość źródłową, która nie jest RÓWNa NULL. Jeśli wszystkie argumenty mają wartość NULL, a właściwość DefaultValue jest obecna, zostanie zwrócona wartość DefaultValue. Jeśli wszystkie argumenty mają wartość NULL i właściwość DefaultValue nie istnieje, funkcja łączenia zwraca wartość NULL.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Wymagane | Ciąg |Wymagana, zmienna liczba razy. Zwykle nazwa atrybutu w obiekcie źródłowym. |
| **defaultValue** | Opcjonalne | Ciąg | Wartość domyślna, która ma być używana, jeśli wszystkie wartości źródłowe mają wartość NULL. Może być pustym ciągiem ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Funkcja:** ConvertToBase64 (Źródło)

**Opis:** Funkcja ConvertToBase64 konwertuje ciąg na ciąg Unicode Base64.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Ciąg do przekonwertowania na podstawowy 64|

**Przyklad**
`ConvertToBase64("Hello world!")`

Zwraca wartość "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funkcja:** ConvertToUTF8Hex (Źródło)

**Opis:** Funkcja ConvertToUTF8Hex konwertuje ciąg na zakodowaną wartość szesnastkową UTF8.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Ciąg, który ma zostać przekonwertowany na kodowanie szesnastkowe|

**Przyklad**
`ConvertToUTF8Hex("Hello world!")`

Zwraca 48656C6C6F20776F726C6421

---
### <a name="count"></a>Liczba
**Funkcja:** Count (atrybut)

**Opis:** Funkcja count zwraca liczbę elementów w atrybucie wielowartościowym

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **przypisane** |Wymagane |— atrybut |Wielowartościowy atrybut, który będzie miał zliczane elementy|

---
### <a name="cstr"></a>CStr
**Funkcja:** CStr (wartość)

**Opis:** Funkcja CStr konwertuje wartość na typ danych ciągu.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **wartość** |Wymagane | liczbowe, odwołanie lub wartość logiczna | Może być wartością liczbową, atrybutem odwołania lub wartością logiczną. |

**Przyklad**
`CStr([dn])`

Zwraca wartość "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funkcja:** DateFromNum (wartość)

**Opis:** Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **wartość** |Wymagane | Data | Data usługi AD do przekonwertowania na typ DateTime |

**Przyklad**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Zwraca datę i godzinę reprezentującą 1 stycznia 2012 o wartości 11:13:00.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funkcja:** FormatDateTime (Źródło, inputFormat, outputFormat)

**Opis:** Pobiera ciąg daty z jednego formatu i konwertuje go na inny format.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu w obiekcie źródłowym. |
| **inputFormat** |Wymagane |Ciąg |Oczekiwany format wartości źródłowej. Obsługiwane formaty można znaleźć w temacie [/dotnet/Standard/Base-Types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Wymagane |Ciąg |Format daty wyjściowej. |

---
### <a name="guid"></a>Guid (identyfikator GUID)
**Funkcja:** Identyfikator GUID ()

**Opis:** Identyfikator GUID funkcji generuje nowy losowy identyfikator GUID

---
### <a name="iif"></a>IIF
**Funkcja:** IIF (warunek, valueIfTrue, valueIfFalse)

**Opis:** Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **rozgrzewa** |Wymagane |Zmienna lub wyrażenie |Dowolna wartość lub wyrażenie, które może przyjąć wartość PRAWDA lub FAŁSZ. |
| **valueIfTrue** |Wymagane |Zmienna lub ciąg | Jeśli wynikiem warunku jest wartość true, zwrócona wartość. |
| **valueIfFalse** |Wymagane |Zmienna lub ciąg |Jeśli wynikiem warunku jest false, zwrócona wartość.|

**Przyklad**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Funkcja:** InStr (wartość1, wartość2, początek, comparetype)

**Opis:** Funkcja InStr Znajdowanie pierwszego wystąpienia podciągu w ciągu

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **sekwencj** |Wymagane |Ciąg |Ciąg do przeszukania |
| **wartość2** |Wymagane |Ciąg |Ciąg, który ma zostać znaleziony |
| **Start** |Opcjonalne |Liczba całkowita |Pozycja początkowa do znalezienia podciągu|
| **comparetype** |Opcjonalne |Wyliczenie |Może być vbTextCompare lub vbBinaryCompare |

**Przyklad**
`InStr("The quick brown fox","quick")`

Szacuje się na 5

`InStr("repEated","e",3,vbBinaryCompare)`

Szacuje się w 7

---
### <a name="isnull"></a>IsNull
**Funkcja:** IsNull (wyrażenie)

**Opis:** Jeśli wyrażenie daje w wyniku wartość null, Funkcja IsNull zwraca wartość true. W przypadku atrybutu wartość null jest wyrażana przez nieobecność atrybutu.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **expression** |Wymagane |expression |Wyrażenie, które ma zostać obliczone |

**Przyklad**
`IsNull([displayName])`

Zwraca wartość true, jeśli atrybut nie jest obecny.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funkcja:** IsNullOrEmpty (wyrażenie)

**Opis:** Jeśli wyrażenie ma wartość null lub jest pustym ciągiem, funkcja IsNullOrEmpty zwraca wartość true. W przypadku atrybutu wynikiem tego jest wartość true, jeśli atrybut jest nieobecny lub jest obecny, ale jest ciągiem pustym.
Odwrotność tej funkcji ma nazwę isobecne.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **expression** |Wymagane |expression |Wyrażenie, które ma zostać obliczone |

**Przyklad**
`IsNullOrEmpty([displayName])`

Zwraca wartość true, jeśli atrybut nie jest obecny lub jest ciągiem pustym.

---
### <a name="ispresent"></a>Isobecne
**Funkcja:** Isobecny (wyrażenie)

**Opis:** Jeśli wyrażenie daje w wyniku ciąg, który nie ma wartości null i nie jest pusty, funkcja isprezent zwraca wartość true. Odwrotność tej funkcji ma nazwę IsNullOrEmpty.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **expression** |Wymagane |expression |Wyrażenie, które ma zostać obliczone |

**Przyklad**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Funkcja:** IsString (wyrażenie)

**Opis:** Jeśli wyrażenie może być szacowane do typu ciągu, funkcja IsString daje w wyniku wartość true.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **expression** |Wymagane |expression |Wyrażenie, które ma zostać obliczone |

---
### <a name="item"></a>Element
**Funkcja:** Element (atrybut, indeks)

**Opis:** Funkcja Item zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **przypisane** |Wymagane |Atrybut |Wielowartościowy atrybut do przeszukania |
| **indeks** |Wymagane |Liczba całkowita | Indeksuj do elementu w ciągu wielowartościowym|

**Przyklad**
`Item([proxyAddresses], 1)`

---
### <a name="join"></a>Join
**Funkcja:** Join (separator, Source1, SOURCE2,...)

**Opis:** Join () jest podobny do dołączania (), z tą różnicą, że może połączyć wiele wartości ciągu **źródłowego** w jeden ciąg, a każda wartość zostanie oddzielona przez ciąg **separatora** .

Jeśli jedna z wartości źródłowych jest atrybutem wielowartościowym, każda wartość w tym atrybucie zostanie przyłączona wspólnie, oddzielona przez wartość separatora.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **rozdzielając** |Wymagane |Ciąg |Ciąg używany do oddzielania wartości źródłowych, gdy są one łączone w jeden ciąg. Może to być "", jeśli nie jest wymagany żaden separator. |
| **source1 ... sourceN** |Wymagana, zmienna liczba razy |Ciąg |Wartości ciągu, które mają być połączone ze sobą. |

---
### <a name="left"></a>Lewe
**Funkcja:** Left (ciąg, NumChars)

**Opis:** Funkcja Left Zwraca określoną liczbę znaków z lewej strony ciągu. Jeśli numChars = 0, zwraca pusty ciąg.
Jeśli numChars < 0, zwracany ciąg wejściowy.
Jeśli ciąg ma wartość null, zwracany jest pusty ciąg.
Jeśli ciąg zawiera mniej znaków niż liczba określona w numChars, zwracany jest ciąg identyczny jak ciąg (czyli zawierający wszystkie znaki w parametrze 1).

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **Ciąg** |Wymagane |Atrybut | Ciąg, z którego mają zostać zwrócone znaki |
| **NumChars** |Wymagane |Liczba całkowita | Liczba określająca liczbę znaków do zwrócenia od początku (po lewej) ciągu|

**Przyklad**
`Left("John Doe", 3)`

Zwraca wartość "Joh".

---
### <a name="mid"></a>Mid
**Funkcja:** Mid (Źródło, początek, długość)

**Opis:** Zwraca podciąg wartości źródłowej. Podciąg jest ciągiem zawierającym tylko niektóre znaki z ciągu źródłowego.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu. |
| **Start** |Wymagane |liczba całkowita |Indeks w ciągu **źródłowym** , w którym powinien zostać uruchomiony podciąg. Pierwszy znak w ciągu będzie miał indeks 1, drugi znak będzie miał indeks 2 itd. |
| **length** |Wymagane |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza ciągiem **źródłowym** , funkcja zwróci podciąg z **początkowego** indeksu do końca ciągu **źródłowego** . |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkcja:** NormalizeDiacritics (Źródło)

**Opis:** Wymaga jednego argumentu ciągu. Zwraca ciąg, ale z dowolnymi znakami diakrytycznymi zastąpionymi odpowiednikami znaków niediakrytycznych. Zwykle używany do konwersji pierwszych nazw i ostatnich nazw zawierających znaki diakrytyczne (znaczniki akcentów) do wartości dozwolonych, które mogą być używane w różnych identyfikatorach użytkowników, takich jak główne nazwy użytkowników, nazwy kont SAM i adresy e-mail.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg | Zwykle jest to atrybut imię i nazwisko. |

---
### <a name="not"></a>Not
**Funkcja:** Nie (Źródło)

**Opis:** Odwraca wartość logiczną **źródła**. Jeśli wartością **źródłową** jest true, zwraca wartość false. W przeciwnym razie zwraca wartość true.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg logiczny |Oczekiwane wartości **źródłowe** to "true" lub "false". |

---
### <a name="numfromdate"></a>NumFromDate
**Funkcja:** NumFromDate (wartość)

**Opis:** Funkcja NumFromDate konwertuje wartość DateTime na format Active Directory, który jest wymagany do ustawiania atrybutów, takich jak [accountExpires](/windows/win32/adschema/a-accountexpires). Ta funkcja umożliwia konwertowanie wartości DateTime otrzymywanych z aplikacji w chmurze, takich jak Workday i SuccessFactors, na ich równoważną reprezentację usługi AD. 

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **wartość** |Wymagane | Ciąg | Ciąg daty i godziny w obsługiwanym formacie. Obsługiwane formaty można znaleźć w temacie https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx . |

**Przykład:**
* Przykładem produktu Workday przy założeniu, że chcesz zmapować atrybut *ContractEndDate* z produktu Workday, który znajduje się w formacie *2020-12-31-08:00* do *accountExpires* w usłudze AD, poniżej przedstawiono sposób użycia tej funkcji i zmiany przesunięcia strefy czasowej na zgodność z ustawieniami regionalnymi. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors przykład przy założeniu, że chcesz zmapować atrybut *EndDate* z SuccessFactors, który jest w formacie *M/d/rrrr hh: mm: SS TT* to *accountExpires* w usłudze AD, Oto jak można użyć tej funkcji i zmienić przesunięcie strefy czasowej, tak aby odpowiadało ustawieniom regionalnym.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates —
**Funkcja:** RemoveDuplicates — (atrybut)

**Opis:** Funkcja RemoveDuplicates — przyjmuje ciąg o wartości wielowartościowej i upewnij się, że każda wartość jest unikatowa.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **przypisane** |Wymagane |Wielowartościowy atrybut |Atrybut wielowartościowy, który zostanie usunięty duplikaty|

**Przykład:** 
 `RemoveDuplicates([proxyAddresses])` Zwraca oczyszczony atrybut proxyAddress, w którym wszystkie zduplikowane wartości zostały usunięte.

---
### <a name="replace"></a>Zamień
**Funkcja:** Replace (Source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, Template)

**Opis:** Zamienia wartości w ciągu w sposób uwzględniający wielkość liter. Funkcja działa inaczej w zależności od podanych parametrów:

* Gdy są podane **OldValue** i **replacementValue** :
  
  * Zamienia wszystkie wystąpienia wartości **OldValue** w **źródle**  na **replacementValue**
* Gdy jest udostępniana **OldValue** i **szablon** :
  
  * Zamienia wszystkie wystąpienia wartości **OldValue** w **szablonie** na wartość **Source**
* Jeśli **regexPattern** i **replacementValue** są podane:

  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i można użyć nazw grup wyrażeń regularnych do konstruowania ciągu dla **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** są podane:
  
  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementAttributeName** są podane:
  
  * Jeśli **Źródło** nie ma wartości, zwracane jest **Źródło**
  * Jeśli **Źródło** ma wartość, funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** wartością skojarzoną z **replacementAttributeName**

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu w obiekcie **źródłowym** . |
| **oldValue** |Opcjonalne |Ciąg |Wartość, która ma zostać zastąpiona w **źródle** lub **szablonie**. |
| **regexPattern** |Opcjonalne |Ciąg |Wzorzec wyrażenia regularnego dla wartości, która ma zostać zastąpiona w **źródle**. Lub, gdy **replacementPropertyName** jest używany, wzorzec wyodrębniania wartości z **replacementPropertyName**. |
| **regexGroupName** |Opcjonalne |Ciąg |Nazwa grupy w **regexPattern**. Tylko wtedy, gdy  **replacementPropertyName** jest używany, wyodrębnimy wartość tej grupy jako **replacementValue** z **replacementPropertyName**. |
| **replacementValue** |Opcjonalne |Ciąg |Nowa wartość, aby zastąpić starą. |
| **replacementAttributeName** |Opcjonalne |Ciąg |Nazwa atrybutu, który ma być używany na potrzeby wartości zamiennej |
| **formularza** |Opcjonalne |Ciąg |Gdy zostanie podana wartość **szablonu** , poszukamy wartości **OldValue** wewnątrz szablonu i Zastąp ją wartością **Source** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkcja:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Opis:** Wymaga co najmniej dwóch argumentów, które są unikatowymi regułami generowania wartości zdefiniowanych przy użyciu wyrażeń. Funkcja oblicza każdą regułę, a następnie sprawdza wartość wygenerowaną w celu zapewnienia unikatowości w docelowej aplikacji/katalogu. Pierwsza unikatowa wartość zostanie znaleziona. Jeśli wszystkie wartości istnieją już w miejscu docelowym, wpis zostanie zapisany w trybie Escrow, a powód zostanie zarejestrowany w dziennikach inspekcji. Nie ma górnej granicy liczby argumentów, które można dostarczyć.


 - Jest to funkcja najwyższego poziomu, która nie może być zagnieżdżona.
 - Nie można zastosować tej funkcji do atrybutów, które mają pasujące pierwszeństwo.   
 - Ta funkcja jest przeznaczona tylko do użycia podczas tworzenia wpisów. Gdy jest używany z atrybutem, ustaw właściwość **Zastosuj mapowanie** na **tylko podczas tworzenia obiektu**.
 - Ta funkcja jest obecnie obsługiwana tylko w przypadku "Workday, Active Directory aprowizacji użytkowników". Nie można jej używać z innymi aplikacjami aprowizacji. 


**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Wymagane są co najmniej 2, brak górnej granicy |Ciąg | Lista unikatowych reguł generowania wartości do obliczenia. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkcja:** SingleAppRoleAssignment ([appRoleAssignments])

**Opis:** Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do przekonwertowania obiektu appRoleAssignments na ciąg o pojedynczej nazwie roli. Należy pamiętać, że najlepszym rozwiązaniem jest upewnienie się, że tylko jedna appRoleAssignment jest przypisana do jednego użytkownika w danym momencie i Jeśli przypiszesz wiele ról, zwracany ciąg roli może nie być przewidywalny. 

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Wymagane |Ciąg |**[appRoleAssignments]** obiekt. |

---
### <a name="split"></a>Podział
**Funkcja:** Split (Źródło, ogranicznik)

**Opis:** Dzieli ciąg na tablicę wielowartościową przy użyciu określonego znaku ogranicznika.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |wartość **źródłowa** do zaktualizowania. |
| **ogranicznik** |Wymagane |Ciąg |Określa znak, który będzie używany do dzielenia ciągu (przykład: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funkcja:** StripSpaces (Źródło)

**Opis:** Usuwa wszystkie znaki spacji ("") z ciągu źródłowego.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |wartość **źródłowa** do zaktualizowania. |

---
### <a name="switch"></a>Przełącznik
**Funkcja:** Przełącznik (Źródło, DefaultValue, Klucz1, wartość1, klucz2, wartość2,...)

**Opis:** Gdy wartość **źródłowa** jest zgodna z **kluczem**, zwraca **wartość** dla tego **klucza**. Jeśli wartość **źródłowa** nie jest zgodna z żadnymi kluczami, zwraca wartość **DefaultValue**.  Parametry **klucza** i **wartości** muszą zawsze znajdować się w parach. Funkcja zawsze oczekuje parzystej liczby parametrów. Funkcja nie powinna być używana dla atrybutów referencyjnych, takich jak Menedżer. 

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Wartość **źródłowa** do zaktualizowania. |
| **defaultValue** |Opcjonalne |Ciąg |Wartość domyślna, która ma być używana, jeśli źródło nie jest zgodne z żadnymi kluczami. Może być pustym ciągiem (""). |
| **głównych** |Wymagane |Ciąg |**Klucz** do porównywania wartości **źródłowej** z. |
| **wartość** |Wymagane |Ciąg |Wartość zastępcza dla **źródła** pasującego do klucza. |

---
### <a name="tolower"></a>ToLower
**Funkcja:** ToLower (Źródło, kultura)

**Opis:** Pobiera wartość ciągu *źródłowego* i konwertuje ją na małe litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu z obiektu źródłowego |
| **dziedzinie** |Opcjonalne |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funkcja:** ToUpper (Źródło, kultura)

**Opis:** Pobiera wartość ciągu *źródłowego* i konwertuje ją na wielkie litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu w obiekcie źródłowym. |
| **dziedzinie** |Opcjonalne |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---
### <a name="word"></a>Word
**Funkcja:** Słowo (String, WordNumber, ograniczniki)

**Opis:** Funkcja słowa zwraca słowo zawarte w ciągu, w oparciu o parametry opisujące ograniczniki do użycia i numer wyrazu do zwrócenia. Każdy ciąg znaków w ciągu rozdzielony przez jeden ze znaków w ogranicznikach jest identyfikowany jako wyrazy:

Jeśli liczba < 1, zwraca pusty ciąg.
Jeśli ciąg ma wartość null, zwraca pusty ciąg.
Jeśli ciąg zawiera mniej niż liczbowe słowa lub ciąg nie zawiera słów identyfikowanych przez ograniczniki, zwracany jest pusty ciąg.

**Wejściowe** 

| Nazwa | Wymagane/powtarzane | Typ | Uwagi |
| --- | --- | --- | --- |
| **Ciąg** |Wymagane |Wielowartościowy atrybut |Ciąg, z którego ma zostać zwrócony wyraz.|
| **WordNumber** |Wymagane | Liczba całkowita | Numer identyfikacyjny, który ma zwracać numer wyrazu|
| **Ograniczniki** |Wymagane |Ciąg| Ciąg reprezentujący ograniczniki, które powinny być używane do identyfikowania wyrazów|

**Przyklad**
`Word("The quick brown fox",3," ")`

Zwraca wartość "brązowy".

`Word("This,string!has&many separators",3,",!&#")`

Zwraca "ma".

---

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Nazwa znanego paska
Aby uzyskać nazwę użytkownika, należy rozdzielić znaną nazwę domeny z wiadomości e-mail użytkownika. Na przykład jeśli domena ma wartość "contoso.com", można użyć następującego wyrażenia:

**Wyrażenia** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe dane wejściowe/wyjściowe:** 

* **Dane wejściowe** (poczta): " john.doe@contoso.com "
* **Wynik**: "Jan. Nowak"

### <a name="append-constant-suffix-to-user-name"></a>Dołącz stały sufiks do nazwy użytkownika
Jeśli używasz piaskownicy usługi Salesforce, może być konieczne dołączenie dodatkowego sufiksu do wszystkich nazw użytkowników przed ich synchronizacją.

**Wyrażenia** 
`Append([userPrincipalName], ".test")`

**Przykładowe dane wejściowe/wyjściowe:** 

* **Dane wejściowe**: (userPrincipalName): " John.Doe@contoso.com "
* **Wynik**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie aliasu użytkownika przez łączenie części imię i nazwisko
Musisz wygenerować alias użytkownika, pobierając pierwsze 3 litery nazwiska użytkownika i pierwszych 5 liter w imieniu użytkownika.

**Wyrażenia** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Przykładowe dane wejściowe/wyjściowe:** 

* **Dane wejściowe** (imię): "Jan"
* **Dane wejściowe** (nazwisko): "Nowak"
* **Dane wyjściowe**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuwanie znaków diakrytycznych z ciągu
Należy zastąpić znaki zawierające znaki akcentu znakami równoważnymi, które nie zawierają znaków akcentu.

**Wyrażenie:** NormalizeDiacritics ([podanąname])

**Przykładowe dane wejściowe/wyjściowe:** 

* **Dane wejściowe** (o podanej): "Zoë"
* **Dane wyjściowe**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dzielenie ciągu na tablicę wielowartościową
Należy wykonać rozdzielaną przecinkami listę ciągów i podzielić je na tablicę, która może być podłączona do atrybutu wielowartościowego, takiego jak PermissionSets. W tym przykładzie lista zestawów uprawnień została wypełniona w extensionAttribute5 w usłudze Azure AD.

**Wyrażenie:** Split ([extensionAttribute5], ",")

**Przykładowe dane wejściowe/wyjściowe:** 

* **Input** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data wyjściowa jako ciąg w określonym formacie
Chcesz wysyłać daty do aplikacji SaaS w określonym formacie. Na przykład, chcesz sformatować daty dla usługi ServiceNow.

**Wyrażenia** 

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (extensionAttribute1): "20150123105347.1 z"
* **Dane wyjściowe**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastąp wartość na podstawie wstępnie zdefiniowanego zestawu opcji

Należy zdefiniować strefę czasową użytkownika na podstawie kodu stanu przechowywanego w usłudze Azure AD. Jeśli kod stanu nie jest zgodny z żadną ze wstępnie zdefiniowanych opcji, użyj wartości domyślnej "Australia/Sydney".

**Wyrażenia** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (stan): "Qld"
* **Dane wyjściowe**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Zamienianie znaków przy użyciu wyrażenia regularnego
Musisz znaleźć znaki, które pasują do wartości wyrażenia regularnego, i usunąć je.

**Wyrażenia** 

Replace ([mailNickname],, "[a-za-Z_] *",, "",,)

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (mailNickname: "john_doe72"
* **Dane wyjściowe**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konwertuj wygenerowaną wartość userPrincipalName (UPN) na małe litery
W poniższym przykładzie wartość UPN jest generowana przez połączenie pól źródłowych PreferredFirstName i PreferredLastName, a funkcja ToLower działa na wygenerowanym ciągu, aby przekonwertować wszystkie znaki na małe litery. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (PreferredFirstName): "Jan"
* **Wejście** (PreferredLastName): "Smith"
* **Wynik**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generuj unikatową wartość atrybutu userPrincipalName (UPN)
Na podstawie imienia i nazwiska użytkownika należy wygenerować wartość atrybutu UPN i sprawdzić jej unikatowość w docelowym katalogu usługi AD przed przypisaniem wartości do atrybutu UPN.

**Wyrażenia** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (PreferredFirstName): "Jan"
* **Wejście** (PreferredLastName): "Smith"
* **Wynik**: " John.Smith@contoso.com ", jeśli wartość UPN John.Smith@contoso.com nie istnieje już w katalogu
* **Wynik**: " J.Smith@contoso.com ", jeśli wartość UPN John.Smith@contoso.com już istnieje w katalogu
* **Wynik**: " Jo.Smith@contoso.com ", jeśli powyższe dwie wartości nazwy UPN już istnieją w katalogu

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Wartość poczty w przepływie, jeśli nie ma wartości NULL, w przeciwnym wypadku Flow
Jeśli ten atrybut jest obecny, należy go przepływać. Jeśli tak nie jest, chcesz zamiast tego przepływać wartość userPrincipalName.

**Wyrażenia** 
`Coalesce([mail],[userPrincipalName])`

**Przykładowe dane wejściowe/wyjściowe:** 

* **Dane wejściowe** (poczta): null
* **Dane wejściowe** (userPrincipalName): " John.Doe@contoso.com "
* **Wynik**: " John.Doe@contoso.com "

## <a name="related-articles"></a>Powiązane artykuły
* [Automatyzacja aprowizacji użytkowników/anulowania obsługi administracyjnej w aplikacjach SaaS](../app-provisioning/user-provisioning.md)
* [Dostosowywanie mapowań atrybutów na potrzeby aprowizacji użytkowników](../app-provisioning/customize-application-attributes.md)
* [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Powiadomienia związane z aprowizacją kont](../app-provisioning/user-provisioning.md)
* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
