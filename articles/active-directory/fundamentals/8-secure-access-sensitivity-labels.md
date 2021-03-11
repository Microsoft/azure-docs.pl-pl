---
title: Kontroluj dostęp zewnętrzny do zasobów w Azure Active Directory z etykietami czułości.
description: Używaj etykiet czułości jako części ogólnego planu zabezpieczeń dla dostępu zewnętrznego.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565176"
---
# <a name="control-access-with-sensitivity-labels"></a>Kontrola dostępu z etykietami czułości 

[Etykiety czułości](/microsoft-365/compliance/sensitivity-labels) ułatwiają kontrolowanie dostępu do zawartości w aplikacjach pakietu Office 365 oraz w kontenerach, takich jak Microsoft Teams, Microsoft 365 Groups i SharePoint. Mogą chronić zawartość bez zakłócania współpracy użytkowników i możliwości produkcyjnych. Etykiety czułości umożliwiają wysyłanie zawartości organizacji między urządzeniami, aplikacjami i usługami, a jednocześnie chronią dane i spełnianie zasad dotyczących zgodności i zabezpieczeń. 

Etykiety czułości można:

* **Klasyfikuj zawartość bez dodawania ustawień ochrony**. Można przypisać klasyfikację do zawartości (np. naklejkę), która będzie trwała i przenoszona do zawartości w miarę ich używania i udostępniania. Ta klasyfikacja służy do generowania raportów użycia i wyświetlania danych o aktywności związanej z poufną zawartością.

* **Wymuszaj ustawienia ochrony, takie jak szyfrowanie, znaki wodne i ograniczenia dostępu**. Na przykład użytkownicy mogą zastosować poufną etykietę do dokumentu lub wiadomości e-mail, a etykieta może [zaszyfrować zawartość](/microsoft-365/compliance/encryption-sensitivity-labels) i dodać "poufne" znak wodny. Ponadto można [zastosować etykietę czułości do kontenera](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) , takiego jak witryna programu SharePoint, i wymusić użytkownikom zewnętrznym dostęp do zawartości, która zawiera.

Etykiety czułości dotyczące poczty e-mail i innej zawartości przesyłanej z zawartością. Etykiety czułości w kontenerach mogą ograniczać dostęp do kontenera, ale zawartość w kontenerze nie dziedziczy etykiety. Na przykład użytkownik może wykonać zawartość z chronionej lokacji, pobrać ją, a następnie udostępnić bez ograniczeń, chyba że zawartość ma także etykietę czułości.

 >[!NOTE]
>Aby zastosować etykiety czułości, użytkownicy muszą być zalogowani do konta służbowego firmy Microsoft. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Uprawnienia niezbędne do tworzenia poziomów czułości i zarządzania nimi

Członkowie zespołu ds. zgodności, którzy będą tworzyć etykiety czułości, muszą mieć uprawnienia do centrum zgodności Microsoft 365, Microsoft 365 Security Center lub Centrum zabezpieczeń & zgodności.

Domyślnie Administratorzy globalni dla dzierżawy mają dostęp do tych centrów administracyjnych i mogą udzielać funkcjonariuszom ds. zgodności i innym osobom, bez udzielania im wszystkich uprawnień administratora dzierżawy. Dla tego delegowanego dostępu administratora, należy dodać użytkowników do grupy rola Administrator danych zgodności, administrator zgodności lub administrator zabezpieczeń.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Ustalanie strategii etykietowania czułości

W miarę jak sądzisz o sposobie zarządzania dostępem zewnętrznym do zawartości, ustal następujące kwestie:

**Dla całej zawartości i kontenerów**

* Jak można zdefiniować, co to jest wysoki, średni lub niski wpływ na działalność (HBI, MBI, LBI)? Jeśli określone typy zawartości są udostępniane w sposób niewłaściwy, należy wziąć pod uwagę wpływ na organizację.

   * Zawartość o określonych typach [zawartości poufnej](/microsoft-365/compliance/apply-sensitivity-label-automatically), takiej jak karty kredytowe lub numery paszportów

   * Zawartość utworzona przez określone grupy lub osoby (na przykład funkcjonariusze ds. zgodności, urzędy finansowe lub kierownicy)

   * Zawartość w określonych bibliotekach lub witrynach. Na przykład kontenery obsługujące strategię organizacyjną lub prywatne dane finansowe

   * Inne kryteria

* Których kategorii zawartości (na przykład HBI Content) powinna mieć ograniczony dostęp użytkowników zewnętrznych?

   * Ograniczenia mogą obejmować akcje, takie jak ograniczanie dostępu do kontenerów i szyfrowanie zawartości.

* Jakie wartości domyślne powinny być stosowane w przypadku HBI danych, witryn lub grup Microsoft 365?

* Gdzie będą używane etykiety czułości do [etykietowania i monitorowania](/microsoft-365/compliance/sensitivity-labels), a nie do [wymuszania szyfrowania](/microsoft-365/compliance/encryption-sensitivity-labels) lub [wymuszania ograniczeń dostępu do kontenera](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)?

**Dla wiadomości e-mail i zawartości**

* Czy chcesz [automatycznie zastosować etykiety czułości](/microsoft-365/compliance/apply-sensitivity-label-automatically) do zawartości lub zrobić to ręcznie?

   * Jeśli zdecydujesz się to zrobić ręcznie, czy chcesz [zalecić użytkownikom stosowanie etykiet](/microsoft-365/compliance/apply-sensitivity-label-automatically)?

**Kontenery**

* Jakie kryterium określi, czy grupy M365, zespoły lub witryny programu SharePoint wymagają ograniczenia dostępu przy użyciu etykiet czułości?

* Czy chcesz, aby tylko etykieta zawartość w tych kontenerach była przenoszona do przodu czy chcesz [automatycznie oznaczyć](/microsoft-365/compliance/apply-sensitivity-label-automatically) istniejące pliki w programie SharePoint i usłudze OneDrive?

Zapoznaj się z tymi [typowymi scenariuszami etykiet czułości](/microsoft-365/compliance/get-started-with-sensitivity-labels) , aby poznać inne pomysły dotyczące używania etykiet czułości.

### <a name="sensitivity-labels-on-email-and-content"></a>Etykiety czułości dotyczące wiadomości e-mail i zawartości

Przypisanie etykiety czułości do dokumentu lub wiadomości e-mail przypomina pieczęć zastosowana do zawartości, którą można dostosowywać, wyczyścić tekst i trwałe. 

* **Dostosowywalne** oznacza, że można tworzyć etykiety odpowiednie dla organizacji i określać, co się dzieje w przypadku ich zastosowania.

* **Czysty tekst** oznacza, że jest częścią metadanych elementu i jest możliwy do odczytania przez aplikacje i usługi, dzięki czemu mogą stosować własne działania ochronne.

* **Trwałe** oznacza, że etykieta i wszelkie powiązane zabezpieczenia są przekazywane wraz z zawartością i stają się podstawą stosowania i wymuszania zasad.

 

> [!NOTE]
> Każdy element zawartości może mieć zastosowaną etykietę o pojedynczej czułości.


### <a name="sensitivity-labels-on-containers"></a>Etykiety czułości w kontenerach

Etykiety czułości można stosować w kontenerach, takich jak [grupy Microsoft 365](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)i [witryny programu SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites). Po zastosowaniu tej etykiety czułości do obsługiwanego kontenera, etykieta automatycznie stosuje ustawienia Klasyfikacja i ochrona do podłączonej lokacji lub grupy. Etykiety czułości w tych kontenerach mogą kontrolować następujące aspekty kontenerów:

* **Prywatność**. Możesz wybrać, kto może wyświetlać witrynę: określeni użytkownicy, wszyscy użytkownicy wewnętrzni lub wszyscy.

* **Dostęp użytkowników zewnętrznych**. Określa, czy właściciel grupy może dodawać Gości do grupy.

* **Dostęp z urządzeń niezarządzanych**. Określa, czy i w jaki sposób niezarządzane urządzenia mogą uzyskać dostęp do zawartości.

 

![Zrzut ekranu przedstawiający edytowanie etykiet czułości](media/secure-external-access/8-edit-label.png)

 

Po zastosowaniu etykiety czułości do kontenera, takiego jak witryna programu SharePoint, nie jest ona stosowana do zawartości tam: etykiety czułości na kontenerach kontrolują dostęp do zawartości w kontenerze. 

* Jeśli chcesz automatycznie zastosować etykiety do zawartości w kontenerze, zobacz [Automatyczne stosowanie czułości do zawartości](/microsoft-365/compliance/apply-sensitivity-label-automatically).

* Jeśli chcesz, aby użytkownicy mogli ręcznie zastosować etykiety do tej zawartości, upewnij się, że zostały [włączone etykiety czułości dla plików pakietu Office w programach SharePoint i OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files).

### <a name="plan-to-implement-sensitivity-labels"></a>Zaplanuj implementację etykiet czułości

Po ustaleniu, w jaki sposób chcesz używać etykiet informacji o czułości oraz do zawartości i witryn, które mają zostać zastosowane, zapoznaj się z poniższą dokumentacją, aby ułatwić wykonanie implementacji.

1. [Wprowadzenie do etykiet czułości](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Tworzenie strategii wdrażania](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Tworzenie i publikowanie etykiet czułości](/microsoft-365/compliance/create-sensitivity-labels)

4. [Ograniczanie dostępu do zawartości przy użyciu etykiet czułości do zastosowania szyfrowania](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Używanie etykiet czułości z zespołami, grupami i witrynami](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [Włącz etykiety czułości dla plików pakietu Office w programie SharePoint i usłudze OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określ żądany stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md) (Jesteś tutaj).

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)