---
title: 'Azure AD Connect: wyjaśnienie aprowizacji deklaracyjnej | Microsoft Docs'
description: Wyjaśnia model konfiguracji aprowizacji deklaratywnej w Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313624"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect Sync: wyjaśnienie aprowizacji deklaracyjnej
W tym temacie opisano model konfiguracji w Azure AD Connect. Model jest nazywany aprowizacji deklaratywnej i umożliwia łatwe wprowadzenie zmian w konfiguracji. Wiele czynności opisanych w tym temacie jest zaawansowana i nie jest wymagana w przypadku większości scenariuszy klientów.

## <a name="overview"></a>Omówienie
Inicjowanie aprowizacji polega na przetwarzaniu obiektów, które pochodzą z katalogu źródłowego połączonego, i określa sposób przekształcania obiektu i atrybutów ze źródła do obiektu docelowego. Obiekt jest przetwarzany w potoku synchronizacji, a potok jest taki sam dla reguł ruchu przychodzącego i wychodzącego. Reguła ruchu przychodzącego pochodzi z przestrzeni łącznika do Metaverse, a Reguła ruchu wychodzącego pochodzi z elementu Metaverse do obszaru łącznika.

![Diagram przedstawiający przykład potoku synchronizacji.](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Potok ma kilka różnych modułów. Każdy z nich jest odpowiedzialny za jedną koncepcję w synchronizacji obiektów.

![Diagram przedstawiający moduły w potoku.](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Źródło, obiekt źródłowy
* [Zakres](#scope), znajduje wszystkie reguły synchronizacji, które znajdują się w zakresie
* [Join](#join), określa relację między przestrzenią łącznika a Metaverse
* Przekształcanie, obliczanie sposobu, w jaki atrybuty powinny być przekształcone i przepływ
* [Priorytet](#precedence), rozwiązywanie konfliktów udziałów atrybutów
* Target, obiekt docelowy

## <a name="scope"></a>Zakres
Moduł zakresu ocenia obiekt i określa reguły, które znajdują się w zakresie i powinny być zawarte w przetwarzaniu. W zależności od wartości atrybutów obiektu, różne reguły synchronizacji są oceniane jako należące do zakresu. Na przykład wyłączony użytkownik bez skrzynek pocztowych programu Exchange ma inne reguły niż włączony użytkownik ze skrzynką pocztową.  
![Diagram pokazujący moduł zakresu dla obiektu.](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Zakres jest definiowany jako grupy i klauzule. Klauzule znajdują się w grupie. Koniunkcja logiczna i jest używana między wszystkimi klauzulami w grupie. Na przykład (dział = IT i Country = Dania). Wartość logiczna OR jest używana między grupami.

![Zakres](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Zakres na tym obrazie powinien być odczytywany jako (dział = IT i Country = Dania) lub (Country = Szwecja). Jeśli grupa 1 lub 2 ma wartość true, reguła znajduje się w zakresie.

Moduł Scope obsługuje następujące operacje.

| Operacja | Opis |
| --- | --- |
| RÓWNA SIĘ, NOTEQUAL |Porównywany ciąg, który oblicza, czy wartość jest równa wartości w atrybucie. Aby uzyskać atrybuty wielowartościowe, zobacz ISIN i ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Porównywany ciąg, który oblicza, czy wartość jest mniejsza niż wartość w atrybucie. |
| ZAWIERA, NOTCONTAINS |Porównywany ciąg, który oblicza, czy można znaleźć wartość w miejscu wewnątrz wartości atrybutu. |
| STARTSWITH, NOTSTARTSWITH |Porównywany ciąg, który oblicza, czy wartość jest na początku wartości w atrybucie. |
| ENDSWITH, NOTENDSWITH |Porównywany ciąg, który oblicza, czy wartość znajduje się na końcu wartości atrybutu. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Porównywany ciąg, który oblicza, czy wartość jest większa niż wartość w atrybucie. |
| ISNULL, ISNOTNULL |Zwraca wartość, jeśli atrybut nie jest obecny w obiekcie. Jeśli atrybut nie jest obecny i w związku z tym ma wartość null, reguła jest w zakresie. |
| ISIN, ISNOTIN |Oblicza, czy wartość jest obecna w zdefiniowanym atrybucie. Ta operacja to wielowartościowa odmiana RÓWNa i NOTEQUAL. Atrybut powinien być atrybutem wielowartościowym i jeśli wartość może być znaleziona w dowolnych wartościach atrybutów, reguła jest w zakresie. |
| ISBITSET, ISNOTBITSET |Oblicza, czy określony bit jest ustawiony. Na przykład można użyć do obliczenia bitów w kontroli konta użytkownika, aby sprawdzić, czy użytkownik jest włączony lub wyłączony. |
| ISMEMBEROF, ISNOTMEMBEROF |Wartość powinna zawierać nazwę DN do grupy w obszarze łącznika. Jeśli obiekt jest członkiem określonej grupy, reguła znajduje się w zakresie. |

## <a name="join"></a>Join
Moduł JOIN w potoku synchronizacji jest odpowiedzialny za znalezienie relacji między obiektem w obiekcie źródłowym a obiektem w obiekcie docelowym. W regule ruchu przychodzącego ta relacja powinna być obiektem w obszarze łącznika, który umożliwia znalezienie relacji do obiektu w obiekcie Metaverse.  
![Dołącz do między cs i MV](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Celem jest sprawdzenie, czy istnieje obiekt znajdujący się już w magazynie Metaverse utworzonym przez inny łącznik, powinien zostać skojarzony z. Na przykład w lesie zasobów konta użytkownik z lasu kont powinien być przyłączony do użytkownika z lasu zasobów.

Sprzężenia są używane głównie w regułach ruchu przychodzącego do przyłączania obiektów przestrzeni łącznika do tego samego obiektu Metaverse.

Sprzężenia są zdefiniowane jako co najmniej jedna grupa. Wewnątrz grupy znajdują się klauzule. Koniunkcja logiczna i jest używana między wszystkimi klauzulami w grupie. Wartość logiczna OR jest używana między grupami. Grupy są przetwarzane w kolejności od góry do dołu. Gdy jedna grupa odnalazła dokładnie jedno dopasowanie z obiektem w obiekcie docelowym, nie są oceniane żadne inne reguły sprzężenia. Jeśli zostanie znaleziony zero lub więcej niż jeden obiekt, przetwarzanie będzie kontynuowane dla następnej grupy reguł. Z tego powodu zasady powinny być tworzone w kolejności najdokładniejszych pierwszej i większej liczby elementów rozmytych na końcu.  
![Definicja sprzężenia](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Sprzężenia na tym obrazie są przetwarzane od góry do dołu. Pierwszy potok synchronizacji jest widoczny, jeśli istnieje dopasowanie w polu IDPracownika. W przeciwnym razie druga reguła widzi, czy nazwa konta może zostać użyta do łączenia się z obiektami. Jeśli to nie jest dopasowanie, trzecia i końcowa reguła jest bardziej rozmytym dopasowaniem przy użyciu nazwy użytkownika.

Jeśli wszystkie reguły sprzężenia zostały ocenione i nie istnieje dokładnie jedno dopasowanie, **Typ łącza** na stronie **Opis** jest używany. Jeśli ta opcja jest ustawiona na wartość **Udostępnij**, zostanie utworzony nowy obiekt w obiekcie docelowym.  
![Zrzut ekranu, na którym jest otwarte menu rozwijane "typ łącza".](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Obiekt powinien mieć tylko jedną regułę z pojedynczą synchronizacją z regułami sprzężenia w zakresie. Jeśli istnieje wiele reguł synchronizacji, w których zdefiniowano element join, wystąpi błąd. Pierwszeństwo nie jest używane do rozwiązywania konfliktów sprzężeń. Obiekt musi mieć regułę sprzężenia w zakresie dla atrybutów do przepływu z tym samym kierunkiem ruchu przychodzącego/wychodzącego. Jeśli musisz przepływać atrybuty przychodzące i wychodzące do tego samego obiektu, musisz mieć regułę ruchu przychodzącego i wychodzącego z sprzężeniem.

Sprzężenie wychodzące ma specjalne zachowanie, gdy próbuje zainicjować obsługę obiektu w docelowym miejscu łącznika. Atrybut DN jest używany do pierwszej próby sprzężenia zwrotnego. Jeśli w docelowym miejscu łącznika istnieje już obiekt o tej samej nazwie, obiekty są przyłączone.

Moduł Join jest obliczany tylko raz, gdy Nowa reguła synchronizacji znajduje się w zakresie. Gdy obiekt jest przyłączony, nie jest odłączany nawet wtedy, gdy kryteria sprzężenia nie są już spełnione. Jeśli chcesz rozłączyć obiekt, reguła synchronizacji przyłączona do obiektów musi wykroczyć zakres.

### <a name="metaverse-delete"></a>Usuwanie z magazynu Metaverse
Obiekt Metaverse pozostaje tak długo, jak istnieje jedna reguła synchronizacji w zakresie z **typem linku** ustawionym na **wartość** lub **StickyJoin**. StickyJoin jest używany, gdy łącznik nie może zainicjować obsługi administracyjnej nowego obiektu w obiekcie Metaverse, ale gdy ma on przyłączony, należy go usunąć ze źródła przed usunięciem obiektu Metaverse.

Po usunięciu obiektu metaverse wszystkie obiekty skojarzone z regułą synchronizacji wychodzącej oznaczonej jako do **zainicjowania** są oznaczone do usunięcia.

## <a name="transformations"></a>Przekształcenia
Przekształcenia są używane do definiowania sposobu przepływu atrybutów z lokalizacji źródłowej do docelowej. Przepływy mogą mieć jeden z następujących **typów przepływów**: bezpośrednie, stałe lub wyrażenie. Przepływ bezpośredni, przepływ wartości atrybutu bez dodatkowych transformacji. Wartość stała ustawia określoną wartość. W wyrażeniu jest stosowane deklaratywne wyrażenie inicjowania obsługi administracyjnej, które wyraża, jak powinna być transformacja. Szczegóły dotyczące języka wyrażeń można znaleźć w temacie [Opis języka wyrażeń deklaracyjne Inicjowanie obsługi](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

![Inicjowanie obsługi administracyjnej lub dołączanie](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Pole wyboru **Zastosuj raz** definiuje, że atrybut ma być ustawiane tylko wtedy, gdy obiekt jest początkowo tworzony. Na przykład ta konfiguracja może służyć do ustawiania początkowego hasła dla nowego obiektu użytkownika.

### <a name="merging-attribute-values"></a>Scalanie wartości atrybutów
W polu przepływy atrybutu istnieje ustawienie umożliwiające ustalenie, czy atrybuty wielowartościowe mają być scalane z kilku różnych łączników. Wartość domyślna to **Update**, co oznacza, że reguła synchronizacji o najwyższym priorytecie powinna zostać wygrana.

![Zrzut ekranu przedstawiający sekcję "Dodawanie przekształceń" z menu rozwijanym "typy scalania" otwarte.](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Istnieje również **scalanie** i **MergeCaseInsensitive**. Te opcje umożliwiają scalanie wartości z różnych źródeł. Na przykład może służyć do scalania elementu członkowskiego lub atrybutu proxyAddresses z kilku różnych lasów. W przypadku korzystania z tej opcji wszystkie reguły synchronizacji w zakresie dla obiektu muszą używać tego samego typu scalania. Nie można zdefiniować **aktualizacji** z jednego łącznika i **scalić** z innego. Jeśli spróbujesz, zostanie wyświetlony komunikat o błędzie.

Różnica między **scalaniem** i **MergeCaseInsensitive** polega na tym, jak przetwarzać zduplikowane wartości atrybutów. Aparat synchronizacji sprawdza, czy zduplikowane wartości nie są wstawiane do atrybutu docelowego. W przypadku **MergeCaseInsensitive** należy duplikować wartości z tylko różnicą w przypadku, gdy nie będą one obecne. Na przykład nie powinno być widoczne " SMTP:bob@contoso.com " i " smtp:bob@contoso.com " w atrybucie Target. **Scalanie** sprawdza tylko dokładne wartości i wiele wartości, w przypadku których może istnieć tylko różnica w przypadku wystąpienia.

Opcja **Zamień** jest taka sama jak **Aktualizacja**, ale nie jest używana.

### <a name="control-the-attribute-flow-process"></a>Sterowanie procesem przepływu atrybutów
W przypadku skonfigurowania wielu reguł synchronizacji dla ruchu przychodzącego w celu współtworzenia tego samego atrybutu Metaverse, do określenia zwycięzcy służy pierwszeństwo. Reguła synchronizacji o najwyższym priorytecie (najniższa wartość liczbowa) będzie współtworzyć wartość. Dzieje się tak samo w przypadku reguł ruchu wychodzącego. Reguła synchronizacji z najwyższym priorytetem usługi WINS i Współtworzenie wartości w podłączonym katalogu.

W niektórych przypadkach, zamiast współtworzenia wartości, reguła synchronizacji powinna określić, jak powinny być zachowane inne reguły. W tym przypadku są używane pewne specjalne literały.

W przypadku reguł synchronizacji ruchu przychodzącego literał **wartości null** można użyć, aby wskazać, że przepływ nie ma wartości do współtworzenia. Inna reguła z niższym pierwszeństwem może współtworzyć wartość. Jeśli żadna reguła nie wniesie żadnej wartości, atrybut Metaverse zostanie usunięty. W przypadku reguły ruchu wychodzącego, jeśli wartość **null jest równa** wartości końcowej po przetworzeniu wszystkich reguł synchronizacji, wartość zostanie usunięta w połączonym katalogu.

Literał **AuthoritativeNull** jest podobny do **wartości null** , ale z różnicą, że żadne reguły o niższym priorytecie nie mogą współtworzyć wartości.

Przepływ atrybutu może również używać **IgnoreThisFlow**. Jest ona podobna do wartości NULL w sensie, że oznacza to, że nie ma niczego do współtworzenia. Różnica polega na tym, że nie usuwa już istniejącej wartości w elemencie docelowym. Jest to podobne do przepływu atrybutu.

Oto przykład:

W przypadku użycia *hybrydowego programu Exchange dla użytkowników usługi AD* można znaleźć następujący przepływ:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
To wyrażenie powinno być odczytane jako: Jeśli skrzynka pocztowa użytkownika znajduje się w usłudze Azure AD, a następnie zostanie przeniesiona z usługi Azure AD do usługi AD. W przeciwnym razie nie należy przepływać niczego z powrotem do Active Directory. W takim przypadku zachowuje istniejącą wartość w usłudze AD.

### <a name="importedvalue"></a>ImportedValue
Funkcja ImportedValue jest inna niż wszystkie inne funkcje, ponieważ nazwa atrybutu musi być ujęta w cudzysłów zamiast nawiasów kwadratowych:  
`ImportedValue("proxyAddresses")`.

Zwykle podczas synchronizacji atrybut używa oczekiwanej wartości, nawet jeśli nie został jeszcze wyeksportowany lub wystąpił błąd podczas eksportowania ("góra obudowy typu Tower"). W przypadku synchronizacji przychodzącej przyjęto, że atrybut, który jeszcze nie osiągnął połączonego katalogu, dotrze do niego. W niektórych przypadkach ważne jest tylko synchronizowanie wartości, która została potwierdzona przez podłączony katalog ("hologram i Delta importowe Wieża").

Przykład tej funkcji można znaleźć w regule wstępnej synchronizacji *w programie z usługi AD — wspólne dla użytkownika z programu Exchange*. W przypadku wymiany hybrydowej wartość dodana przez program Exchange Online powinna być synchronizowana tylko po potwierdzeniu, że wartość została pomyślnie wyeksportowana:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Pierwszeństwo
Gdy kilka reguł synchronizacji próbuje współtworzyć tę samą wartość atrybutu w celu, wartość pierwszeństwa jest używana do określenia zwycięzcy. Reguła z najwyższym priorytetem, najmniejszą wartością liczbową, powoduje, że atrybut w konflikcie.

![Scalanie typów](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Ta kolejność może służyć do definiowania dokładniejszych przepływów atrybutów dla małego podzestawu obiektów. Na przykład w przypadku reguł out-of-Box nie można mieć pewności, że atrybuty z włączonym kontem (**User AccountEnabled**) mają pierwszeństwo przed innymi kontami.

Pierwszeństwo można definiować między łącznikami. Dzięki temu łączniki z lepszymi danymi mogą najpierw współtworzyć wartości.

### <a name="multiple-objects-from-the-same-connector-space"></a>Wiele obiektów z tego samego łącznika
Jeśli masz kilka obiektów w tym samym miejscu łącznika przyłączone do tego samego obiektu metaverse, należy dostosować pierwszeństwo. Jeśli kilka obiektów należy do tej samej reguły synchronizacji, aparat synchronizacji nie będzie w stanie określić pierwszeństwa. Jest niejednoznaczny, który obiekt źródłowy powinien współtworzyć wartość w magazynie Metaverse. Ta konfiguracja jest raportowana jako niejednoznaczna, nawet jeśli atrybuty w źródle mają tę samą wartość.  
![Diagram, który pokazuje wiele obiektów przyłączonych do tego samego obiektu MV z przezroczystą czerwoną nakładką X. ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

W tym scenariuszu należy zmienić zakres reguł synchronizacji, aby obiekty źródłowe miały różne reguły synchronizacji w zakresie. Pozwala to definiować różne pierwszeństwo.  
![Wiele obiektów przyłączonych do tego samego obiektu MV](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Następne kroki
* Przeczytaj więcej na temat języka wyrażeń w temacie [Opis wyrażeń aprowizacji deklaracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Zobacz, w jaki sposób deklaracyjne Inicjowanie obsługi jest używane w [opisie konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).
* Zobacz, jak wprowadzić praktyczną zmianę przy użyciu aprowizacji deklaracyjnej, [Aby wprowadzić zmianę konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md).
* Kontynuuj odczytywanie, jak użytkownicy i Kontakty współpracują ze sobą [, opisując użytkowników i kontakty](concept-azure-ad-connect-sync-user-and-contacts.md).

**Tematy dotyczące omówienia**

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

**Tematy informacyjne**

* [Azure AD Connect Sync: informacje o funkcjach](reference-connect-sync-functions-reference.md)
