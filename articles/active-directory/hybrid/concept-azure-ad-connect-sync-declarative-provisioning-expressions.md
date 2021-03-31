---
title: 'Azure AD Connect: deklaratywne wyrażenia aprowizacji | Microsoft Docs'
description: Wyjaśnia wyrażenia aprowizacji deklaracyjnej.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02490839a9e35695ae2e8b3f750e139ad7413aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280217"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect Sync: wyjaśnienie wyrażeń aprowizacji deklaracyjnej
Usługa Azure AD Connect Sync kompiluje się w ramach aprowizacji deklaracyjnej po raz pierwszy wprowadzona w programie Forefront Identity Manager 2010. Umożliwia wdrożenie kompletnej logiki biznesowej integracji tożsamości bez konieczności pisania skompilowanego kodu.

Istotną częścią deklaratywnej aprowizacji jest język wyrażeń używany w przepływach atrybutów. Używany język to podzbiór programu Microsoft® Visual Basic® for Applications (VBA). Ten język jest używany w Microsoft Office i użytkownicy korzystający z języka VBScript również będą mogli je rozpoznać. Język wyrażeń aprowizacji deklaracyjnej korzysta wyłącznie z funkcji i nie jest językiem strukturalnym. Brak metod lub instrukcji. Funkcje są zagnieżdżone zamiast przepływu programu Express.

Aby uzyskać więcej informacji, zobacz [Zapraszamy do Visual Basic for Applications dokumentacji języka dla pakietu Office 2013](/office/vba/api/overview/language-reference).

Atrybuty są jednoznacznie wpisane. Funkcja akceptuje tylko atrybuty poprawnego typu. Jest również rozróżniana wielkość liter. Nazwy funkcji i nazwy atrybutów muszą mieć poprawną wielkość liter lub występuje błąd.

## <a name="language-definitions-and-identifiers"></a>Definicje i identyfikatory języka
* Funkcje mają nazwę, po której następuje argumenty w nawiasach: FunctionName (argument 1, argument N).
* Atrybuty są identyfikowane za pomocą nawiasów kwadratowych: [attributeName]
* Parametry są identyfikowane przez znaki procentowe:% ParameterName%
* Stałe ciągów są ujęte w cudzysłowy: na przykład "contoso" (Uwaga: muszą używać cudzysłowów prostych "", a nie inteligentnych cudzysłowów "")
* Wartości liczbowe są wyrażane bez cudzysłowów i oczekiwano, że jest to liczba dziesiętna. Wartości szesnastkowe są poprzedzone prefiksem &H. Na przykład 98052, &HFF
* Wartości logiczne są wyrażane za pomocą stałych: true, false.
* Wbudowane stałe i literały są wyrażane tylko ich nazwą: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funkcje
Inicjowanie obsługi deklaracyjnej używa wielu funkcji, aby umożliwić przekształcenie wartości atrybutów. Te funkcje mogą być zagnieżdżane, dzięki czemu wynik z jednej funkcji jest przenoszona do innej funkcji.

`Function1(Function2(Function3()))`

Pełną listę funkcji można znaleźć w [dokumentacji funkcji](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametry
Parametr jest definiowany przez łącznik lub przez administratora przy użyciu programu PowerShell. Parametry zwykle zawierają wartości, które różnią się od systemu do systemu, na przykład nazwę domeny, w której znajduje się użytkownik. Te parametry mogą być używane w przepływach atrybutów.

Łącznik Active Directory dostarczył następujące parametry reguł synchronizacji ruchu przychodzącego:

| Nazwa parametru | Komentarz |
| --- | --- |
| Domena. NetBIOS |Format NetBIOS aktualnie importowanej domeny, na przykład FABRIKAMSALES |
| Domena. FQDN |Format nazwy FQDN aktualnie importowanej domeny, na przykład sales.fabrikam.com |
| Domena. LDAP |Format LDAP aktualnie importowanej domeny, na przykład DC = Sales, DC = Fabrikam, DC = com |
| Las. NetBIOS |Format NetBIOS aktualnie importowanej nazwy lasu, na przykład FABRIKAMCORP |
| Las. FQDN |Format nazwy FQDN aktualnie importowanego lasu, na przykład fabrikam.com |
| Las. LDAP |Format LDAP aktualnie importowanej nazwy lasu, na przykład DC = Fabrikam, DC = com |

System udostępnia następujący parametr, który jest używany do uzyskania identyfikatora aktualnie uruchomionego łącznika:  
`Connector.ID`

Oto przykład, który wypełnia domenę atrybutu Metaverse nazwą NetBIOS domeny, w której znajduje się użytkownik:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatory
Można użyć następujących operatorów:

* **Porównanie**: <, <=,  <>, =, >, >=
* **Matematyka**: +,-, \* ,-
* **String**: & (łączenie)
* **Logiczne**:  &&  (i), | | oraz
* **Kolejność oceny**: ()

Operatory są szacowane od lewej do prawej i mają ten sam priorytet oceny. Oznacza to, że \* (mnożnik) nie jest oceniane przed-(Odejmowanie). 2 \* (5 + 3) nie jest taka sama jak 2 \* 5 + 3. Nawiasy () służą do zmiany kolejności oceny, gdy kolejność od lewej do prawej nie jest odpowiednia.

## <a name="multi-valued-attributes"></a>Atrybuty wielowartościowe
Funkcje mogą działać na atrybutach o wartości pojedynczej i wielowartościowej. W przypadku atrybutów wielowartościowych funkcja działa nad każdą wartością i stosuje tę samą funkcję do każdej wartości.

Na przykład:  
`Trim([proxyAddresses])` Wykonaj przycinanie każdej wartości w atrybucie proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Dla każdej wartości z @-sign , Zastąp domenę wartością @contoso.com .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Wyszukaj wartość SIP-Address i usuń ją z wartości.

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat modelu konfiguracji można znaleźć w temacie [Inicjowanie obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Zobacz, w jaki sposób deklaracyjne Inicjowanie obsługi jest używane w [opisie konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).
* Zobacz, jak wprowadzić praktyczną zmianę przy użyciu aprowizacji deklaracyjnej, [Aby wprowadzić zmianę konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md).

**Tematy dotyczące omówienia**

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

**Tematy informacyjne**

* [Azure AD Connect Sync: informacje o funkcjach](reference-connect-sync-functions-reference.md)