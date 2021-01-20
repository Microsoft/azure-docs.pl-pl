---
title: Azure AD Connect wyrażeń synchronizacji z chmurą i odwołania do funkcji
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869d60d4ccb3bae58924fe4221f83b1b0125ae04
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613536"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Pisanie wyrażeń mapowania atrybutów w Azure Active Directory
Podczas konfigurowania synchronizacji z chmurą jednym z typów mapowań atrybutów, które można określić, jest mapowanie wyrażenia. 

Mapowanie wyrażenia umożliwia dostosowanie atrybutów przy użyciu wyrażenia przypominającego skrypt.  Dzięki temu można przekształcić dane lokalne w nową lub inną wartość.  Na przykład możesz chcieć połączyć dwa atrybuty w jeden atrybut, ponieważ ten pojedynczy atrybut jest używany przez jedną z aplikacji w chmurze.

Następujący dokument obejmuje wyrażenia podobne do skryptu, które są używane do przekształcania danych.  Jest to tylko część procesu.  Następnie konieczne będzie użycie tego wyrażenia i umieszczenie go w żądaniu sieci Web w dzierżawie.  Aby uzyskać więcej informacji na temat, zobacz [przekształcenia](how-to-transformation.md)

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń dla mapowań atrybutów to Reminiscent of Visual Basic for Applications (VBA) Functions.

* Całe wyrażenie musi być zdefiniowane w zakresie funkcji, które składają się z nazwy, a następnie argumentów w nawiasach: <br>
  *FunctionName ( `<<argument 1>>` , `<<argument N>>` )*
* Funkcje mogą być zagnieżdżane w innych. Przykład: <br> *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Można przekazać trzy różne typy argumentów do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe ciągów, które muszą być ujęte w podwójne cudzysłowy. Na przykład: "Stany Zjednoczone"
  3. Inne funkcje. Na przykład: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* W przypadku stałych ciągów, jeśli potrzebujesz ukośnika odwrotnego (\) lub cudzysłowu (") w ciągu, musi to być znak ucieczki z symbolem ukośnika odwrotnego (\). Na przykład: "Nazwa firmy: \\ " contoso \\ ""

## <a name="list-of-functions"></a>Lista funkcji
| Lista funkcji | Opis |
|-----|----|
|[Append](#append)|Pobiera wartość ciągu źródłowego i dołącza jej sufiks do końca.|
|[BitAnd](#bitand)|Funkcja BitAnd ustawia określoną liczbę bitów w wartości.|
|[CBool](#cbool)|Funkcja CBool zwraca wartość logiczną opartą na obliczanym wyrażeniu|
|[ConvertFromBase64](#convertfrombase64)|Funkcja ConvertFromBase64 konwertuje określoną zakodowaną wartość Base64 na zwykły ciąg.|
|[ConvertToBase64](#converttobase64)|Funkcja ConvertToBase64 konwertuje ciąg na ciąg Unicode Base64. |
|[ConvertToUTF8Hex](#converttoutf8hex)|Funkcja ConvertToUTF8Hex konwertuje ciąg na zakodowaną wartość szesnastkową UTF8.|
|[Liczbą](#count)|Funkcja count zwraca liczbę elementów w atrybucie wielowartościowym|
|[CStr](#cstr)|Funkcja CStr konwertuje na typ danych ciągu.|
|[DateFromNum](#datefromnum)|Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.|
|[DNComponent](#dncomponent)|Funkcja DNComponent zwraca wartość określonego składnika DN z lewej strony.|
|[Błąd](#error)|Funkcja Error służy do zwrócenia błędu niestandardowego.|
|[FormatDateTime](#formatdatetime) |Pobiera ciąg daty z jednego formatu i konwertuje go na inny format.| 
|[IDENT](#guid)|Identyfikator GUID funkcji generuje nowy losowy identyfikator GUID.|           
|[IIF](#iif)|Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.|
|[InStr](#instr)|Funkcja InStr Znajdowanie pierwszego wystąpienia podciągu w ciągu.|
|[IsNull](#isnull)|Jeśli wyrażenie daje w wyniku wartość null, Funkcja IsNull zwraca wartość true.|
|[IsNullOrEmpty](#isnullorempty)|Jeśli wyrażenie ma wartość null lub jest pustym ciągiem, funkcja IsNullOrEmpty zwraca wartość true.|         
|[Isobecne](#ispresent)|Jeśli wyrażenie daje w wyniku ciąg, który nie ma wartości null i nie jest pusty, funkcja isprezent zwraca wartość true.|    
|[IsString](#isstring)|Jeśli wyrażenie może być szacowane do typu ciągu, funkcja IsString daje w wyniku wartość true.|
|[Element](#item)|Funkcja Item zwraca jeden element z wielowartościowego ciągu/atrybutu.|
|[Dołącz](#join) |Join () jest podobny do dołączania (), z tą różnicą, że może połączyć wiele wartości ciągu **źródłowego** w jeden ciąg, a każda wartość zostanie oddzielona przez ciąg **separatora** .| 
|[Lewym](#left)|Funkcja Left Zwraca określoną liczbę znaków z lewej strony ciągu.|
|[Mid](#mid) |Zwraca podciąg wartości źródłowej. Podciąg jest ciągiem zawierającym tylko niektóre znaki z ciągu źródłowego.|
|[NormalizeDiacritics](#normalizediacritics)|Wymaga jednego argumentu ciągu. Zwraca ciąg, ale z dowolnymi znakami diakrytycznymi zastąpionymi odpowiednikami znaków niediakrytycznych.|
|[Niemożliwe](#not) |Odwraca wartość logiczną **źródła**. Jeśli wartością **źródłową** jest "*true*", zwraca wartość "*false*". W przeciwnym razie zwraca wartość "*true*".| 
|[RemoveDuplicates —](#removeduplicates)|Funkcja RemoveDuplicates — przyjmuje ciąg o wartości wielowartościowej i upewnij się, że każda wartość jest unikatowa.| 
|[Stępować](#replace) |Zamienia wartości w ciągu. | 
|[SelectUniqueValue](#selectuniquevalue)|Wymaga co najmniej dwóch argumentów, które są unikatowymi regułami generowania wartości zdefiniowanych przy użyciu wyrażeń. Funkcja oblicza każdą regułę, a następnie sprawdza wartość wygenerowaną w celu zapewnienia unikatowości w docelowej aplikacji/katalogu.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji.| 
|[Podziału](#split)|Dzieli ciąg na tablicę wielowartościową przy użyciu określonego znaku ogranicznika.|
|[StringFromSID](#stringfromsid)|Funkcja StringFromSid konwertuje tablicę bajtową zawierającą identyfikator zabezpieczeń na ciąg.| 
|[StripSpaces](#stripspaces) |Usuwa wszystkie znaki spacji ("") z ciągu źródłowego.| 
|[Przełącznik](#switch)|Gdy wartość **źródłowa** jest zgodna z **kluczem**, zwraca **wartość** dla tego **klucza**. | 
|[ToLower](#tolower)|Pobiera wartość ciągu *źródłowego* i konwertuje ją na małe litery przy użyciu określonych reguł kultury.| 
|[ToUpper](#toupper)|Pobiera wartość ciągu *źródłowego* i konwertuje ją na wielkie litery przy użyciu określonych reguł kultury.|
|[Trim](#trim)|Funkcja Trim usuwa wiodące i końcowe białe znaki z ciągu.|
|[Word](#word)|Funkcja słowa zwraca słowo zawarte w ciągu, w oparciu o parametry opisujące ograniczniki do użycia i numer wyrazu do zwrócenia.|

---
### <a name="append"></a>Append
**Funkcyjn**<br> Dołącz (Źródło, sufiks)

**Opis:**<br> Pobiera wartość ciągu źródłowego i dołącza jej sufiks do końca.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu w obiekcie źródłowym. |
   | **przedrostk** |Wymagane |Ciąg |Ciąg, który ma zostać dołączony do końca wartości źródłowej. |

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
 
 `BitAnd(&HF, &HF7)`</br>
 Zwraca wartość 7, ponieważ w liczbie szesnastkowej "F" i "F7" można obliczyć tę wartości.

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
### <a name="error"></a>Błąd
**Opis:**  
Funkcja Error służy do zwrócenia błędu niestandardowego.

**Obowiązuje**  
`void Error(str ErrorMessage)`

**Przykład:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Jeśli atrybut AccountName nie istnieje, zgłoś błąd w obiekcie.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funkcyjn**<br> FormatDateTime (Źródło, inputFormat, outputFormat)

**Opis:**<br> Pobiera ciąg daty z jednego formatu i konwertuje go na inny format.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu w obiekcie źródłowym. |
   | **inputFormat** |Wymagane |Ciąg |Oczekiwany format wartości źródłowej. Obsługiwane formaty można znaleźć w temacie [/dotnet/Standard/Base-Types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
   | **outputFormat** |Wymagane |Ciąg |Format daty wyjściowej. |

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
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

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
### <a name="isstring"></a>IsString
**Opis:**  
Jeśli wyrażenie może być szacowane do typu ciągu, funkcja IsString daje w wyniku wartość true.

**Obowiązuje**  
`bool IsString(var expression)`

**Uwagi**  
Służy do określenia, czy CStr () może pomyślnie przeanalizować wyrażenie.

---
### <a name="join"></a>Join
**Funkcyjn**<br> Join (separator, Source1, SOURCE2,...)

**Opis:**<br> Join () jest podobny do dołączania (), z tą różnicą, że może połączyć wiele wartości ciągu **źródłowego** w jeden ciąg, a każda wartość zostanie oddzielona przez ciąg **separatora** .

Jeśli jedna z wartości źródłowych jest atrybutem wielowartościowym, każda wartość w tym atrybucie zostanie przyłączona wspólnie, oddzielona przez wartość separatora.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **rozdzielając** |Wymagane |Ciąg |Ciąg używany do oddzielania wartości źródłowych, gdy są one łączone w jeden ciąg. Może to być "", jeśli nie jest wymagany żaden separator. |
   | **source1 ... sourceN** |Wymagana, zmienna liczba razy |Ciąg |Wartości ciągu, które mają być połączone ze sobą. |

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
Zwraca wartość `Joh`.

---
### <a name="mid"></a>Mid
**Funkcyjn**<br> Mid (Źródło, początek, długość)

**Opis:**<br> Zwraca podciąg wartości źródłowej. Podciąg jest ciągiem zawierającym tylko niektóre znaki z ciągu źródłowego.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu. |
   | **Start** |Wymagane |liczba całkowita |Indeks w ciągu **źródłowym** , w którym powinien zostać uruchomiony podciąg. Pierwszy znak w ciągu będzie miał indeks 1, drugi znak będzie miał indeks 2 itd. |
   | **length** |Wymagane |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza ciągiem **źródłowym** , funkcja zwróci podciąg z **początkowego** indeksu do końca ciągu **źródłowego** . |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkcyjn**<br> NormalizeDiacritics (Źródło)

**Opis:**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale z dowolnymi znakami diakrytycznymi zastąpionymi odpowiednikami znaków niediakrytycznych. Zwykle używany do konwersji pierwszych nazw i ostatnich nazw zawierających znaki diakrytyczne (znaczniki akcentów) do wartości dozwolonych, które mogą być używane w różnych identyfikatorach użytkowników, takich jak główne nazwy użytkowników, nazwy kont SAM i adresy e-mail.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg | Zwykle jest to atrybut imię i nazwisko. |

---
### <a name="not"></a>Not
**Funkcyjn**<br> Nie (Źródło)

**Opis:**<br> Odwraca wartość logiczną **źródła**. Jeśli wartością **źródłową** jest "*true*", zwraca wartość "*false*". W przeciwnym razie zwraca wartość "*true*".

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg logiczny |Oczekiwane wartości **źródłowe** to "true" lub "false". |

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
**Funkcyjn**<br> Replace (Source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, Template)

**Opis:**<br>
Zamienia wartości w ciągu. Działa inaczej w zależności od podanych parametrów:

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

**Wejściowe**<br> 

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
**Funkcyjn**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Opis:**<br> Wymaga co najmniej dwóch argumentów, które są unikatowymi regułami generowania wartości zdefiniowanych przy użyciu wyrażeń. Funkcja oblicza każdą regułę, a następnie sprawdza wartość wygenerowaną w celu zapewnienia unikatowości w docelowej aplikacji/katalogu. Pierwsza unikatowa wartość zostanie znaleziona. Jeśli wszystkie wartości istnieją już w miejscu docelowym, wpis zostanie zapisany w trybie Escrow, a powód zostanie zarejestrowany w dziennikach inspekcji. Nie ma górnej granicy liczby argumentów, które można dostarczyć.

> [!NOTE]
> - Jest to funkcja najwyższego poziomu, która nie może być zagnieżdżona.
> - Nie można zastosować tej funkcji do atrybutów, które mają pasujące pierwszeństwo.  
> - Ta funkcja jest przeznaczona tylko do użycia podczas tworzenia wpisów. Gdy jest używany z atrybutem, ustaw właściwość **Zastosuj mapowanie** na **tylko podczas tworzenia obiektu**.
> - Ta funkcja jest obecnie obsługiwana tylko w przypadku "Workday, Active Directory aprowizacji użytkowników". Nie można jej używać z innymi aplikacjami aprowizacji. 


**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Wymagane są co najmniej 2, brak górnej granicy |Ciąg | Lista unikatowych reguł generowania wartości do obliczenia. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkcyjn**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Opis:**<br> Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do przekonwertowania obiektu appRoleAssignments na ciąg o pojedynczej nazwie roli. Należy pamiętać, że najlepszym rozwiązaniem jest upewnienie się, że tylko jedna appRoleAssignment jest przypisana do jednego użytkownika w danym momencie i Jeśli przypiszesz wiele ról, zwracany ciąg roli może nie być przewidywalny. 

**Wejściowe**<br> 

  | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Wymagane |Ciąg |**[appRoleAssignments]** obiekt. |

---
### <a name="split"></a>Podział
**Funkcyjn**<br> Split (Źródło, ogranicznik)

**Opis:**<br> Dzieli ciąg na tablicę wielowartościową przy użyciu określonego znaku ogranicznika.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg |wartość **źródłowa** do zaktualizowania. |
   | **ogranicznik** |Wymagane |Ciąg |Określa znak, który będzie używany do dzielenia ciągu (przykład: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Opis:**  
Funkcja StringFromSid konwertuje tablicę bajtową zawierającą identyfikator zabezpieczeń na ciąg.

**Obowiązuje**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Funkcyjn**<br> StripSpaces (Źródło)

**Opis:**<br> Usuwa wszystkie znaki spacji ("") z ciągu źródłowego.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg |wartość **źródłowa** do zaktualizowania. |

---
### <a name="switch"></a>Przełącznik
**Funkcyjn**<br> Przełącznik (Źródło, DefaultValue, Klucz1, wartość1, klucz2, wartość2,...)

**Opis:**<br> Gdy wartość **źródłowa** jest zgodna z **kluczem**, zwraca **wartość** dla tego **klucza**. Jeśli wartość **źródłowa** nie jest zgodna z żadnymi kluczami, zwraca wartość **DefaultValue**.  Parametry **klucza** i **wartości** muszą zawsze znajdować się w parach. Funkcja zawsze oczekuje parzystej liczby parametrów.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg |Wartość **źródłowa** do sprawdzenia. |
   | **defaultValue** |Opcjonalne |Ciąg |Wartość domyślna, która ma być używana, jeśli źródło nie jest zgodne z żadnymi kluczami. Może być pustym ciągiem (""). |
   | **głównych** |Wymagane |Ciąg |**Klucz** do porównywania wartości **źródłowej** z. |
   | **wartość** |Wymagane |Ciąg |Wartość zastępcza dla **źródła** pasującego do klucza. |

---
### <a name="tolower"></a>ToLower
**Funkcyjn**<br> ToLower (Źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na małe litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe**<br> 

   | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu z obiektu źródłowego |
   | **dziedzinie** |Opcjonalne |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---

### <a name="toupper"></a>ToUpper
**Funkcyjn**<br> ToUpper (Źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na wielkie litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe**<br> 

  | Nazwa | Wymagane/powtarzane | Typ | Uwagi |
  | --- | --- | --- | --- |
  | **zewnętrz** |Wymagane |Ciąg |Zwykle nazwa atrybutu w obiekcie źródłowym. |
  | **dziedzinie** |Opcjonalne |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

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

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Nazwa znanego paska
Aby uzyskać nazwę użytkownika, należy rozdzielić znaną nazwę domeny z wiadomości e-mail użytkownika. <br>
Na przykład jeśli domena ma wartość "contoso.com", można użyć następującego wyrażenia:

**Wyrażenia** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (poczta): " john.doe@contoso.com "
* **Wynik**: "Jan. Nowak"

### <a name="append-constant-suffix-to-user-name"></a>Dołącz stały sufiks do nazwy użytkownika
Jeśli używasz piaskownicy usługi Salesforce, może być konieczne dołączenie dodatkowego sufiksu do wszystkich nazw użytkowników przed ich synchronizacją.

**Wyrażenia** <br>
`Append([userPrincipalName], ".test")`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe**: (userPrincipalName): " John.Doe@contoso.com "
* **Wynik**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie aliasu użytkownika przez łączenie części imię i nazwisko
Musisz wygenerować alias użytkownika, pobierając pierwsze 3 litery nazwiska użytkownika i pierwszych 5 liter w imieniu użytkownika.

**Wyrażenia** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (imię): "Jan"
* **Dane wejściowe** (nazwisko): "Nowak"
* **Dane wyjściowe**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuwanie znaków diakrytycznych z ciągu
Należy zastąpić znaki zawierające znaki akcentu znakami równoważnymi, które nie zawierają znaków akcentu.

**Wyrażenia** <br>
NormalizeDiacritics ([podanąname])

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (o podanej): "Zoë"
* **Dane wyjściowe**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dzielenie ciągu na tablicę wielowartościową
Należy wykonać rozdzielaną przecinkami listę ciągów i podzielić je na tablicę, która może być podłączona do atrybutu wielowartościowego, takiego jak PermissionSets. W tym przykładzie lista zestawów uprawnień została wypełniona w extensionAttribute5 w usłudze Azure AD.

**Wyrażenia** <br>
Split ([extensionAttribute5], ",")

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Input** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data wyjściowa jako ciąg w określonym formacie
Chcesz wysyłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład, chcesz sformatować daty dla usługi ServiceNow.

**Wyrażenia** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (extensionAttribute1): "20150123105347.1 z"
* **Dane wyjściowe**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastąp wartość na podstawie wstępnie zdefiniowanego zestawu opcji

Należy zdefiniować strefę czasową użytkownika na podstawie kodu stanu przechowywanego w usłudze Azure AD. <br>
Jeśli kod stanu nie jest zgodny z żadną ze wstępnie zdefiniowanych opcji, użyj wartości domyślnej "Australia/Sydney".

**Wyrażenia** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (stan): "Qld"
* **Dane wyjściowe**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Zamienianie znaków przy użyciu wyrażenia regularnego
Musisz znaleźć znaki, które pasują do wartości wyrażenia regularnego, i usunąć je.

**Wyrażenia** <br>

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

**Wyrażenia** <br>

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


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)