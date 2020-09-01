---
title: Raporty dotyczące inspekcji w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących inspekcji w portalu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01c9449a996929eb20acf35897a100dfbb722e82
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229926"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące inspekcji w portalu usługi Azure Active Directory 

Za pomocą raportów usługi Azure Active Directory (Azure AD) możesz uzyskać informacje potrzebne do określenia sposobu działania środowiska.

Architektura raportowania składa się z następujących składników:

- **Działanie** 
    - **Logowania** — [raport logowania](concept-sign-ins.md) zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - **Dzienniki inspekcji** — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — [ryzykowne logowanie](../identity-protection/overview-identity-protection.md) jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika. 
    - **Użytkownicy oflagowani do ryzyka** — [ryzykowny użytkownik](../identity-protection/overview-identity-protection.md) jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Ten artykuł zawiera omówienie raportu inspekcji.
 
## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?

* Użytkownicy z **uprawnieniami administrator zabezpieczeń**, **czytelnik zabezpieczeń**, **czytelnik raportu** , **czytelnik globalny** lub role **administratora globalnego**

## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji usługi Azure AD zawierają rekordy działań systemowych pod kątem zgodności. Aby uzyskać dostęp do raportu inspekcji, wybierz pozycję **dzienniki inspekcji** w sekcji **monitorowanie** w **Azure Active Directory**. Należy pamiętać, że dzienniki inspekcji mogą mieć czas oczekiwania na godzinę, więc może upłynąć, że dane działania inspekcji będą wyświetlane w portalu po zakończeniu zadania.



Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- datę i godzinę wystąpienia,
- Usługa, która zarejestrowała wystąpienie
- Kategoria i nazwa działania (*co*) 
- stan działania (powodzenie lub niepowodzenie)
- element docelowy.
- inicjatora/aktora (kto) działania,

![Dzienniki inspekcji](./media/concept-audit-logs/listview.png "Dzienniki inspekcji")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Dzienniki inspekcji](./media/concept-audit-logs/columns.png "Dzienniki inspekcji")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Dzienniki inspekcji](./media/concept-audit-logs/columnselect.png "Dzienniki inspekcji")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Dzienniki inspekcji](./media/concept-audit-logs/details.png "Dzienniki inspekcji")


## <a name="filtering-audit-logs"></a>Dzienniki inspekcji filtrowania

Dane inspekcji można filtrować w następujących polach:

- Usługa
- Kategoria
- Działanie
- Stan
- Cel
- Zainicjowane przez (aktor)
- Zakres dat

![Dzienniki inspekcji](./media/concept-audit-logs/filter.png "Dzienniki inspekcji")

Filtr **usługi** umożliwia wybranie z listy rozwijanej następujących usług:

- Wszystko
- ŚRODOWISKO użytkownika zarządzania usługą AAD
- Przeglądy dostępu
- Aprowizacja kont
- Serwer proxy aplikacji
- Metody uwierzytelniania
- B2C
- Dostęp warunkowy
- Katalog podstawowy
- Zarządzanie prawami
- Uwierzytelnianie hybrydowe
- Identity Protection
- Zaproszeni użytkownicy
- Usługa MIM
- MyApps
- PIM
- Samoobsługowe zarządzanie grupami
- Samoobsługowe zarządzanie hasłami
- Warunki użytkowania

Filtr **Kategoria** umożliwia wybranie jednego z następujących filtrów:

- Wszystko
- AdministrativeUnit
- ApplicationManagement
- Uwierzytelnianie
- Autoryzacja
- Kontakt
- Urządzenie
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- Zarządzanie nimi
- Etykieta
- Inne
- PermissionGrantPolicy
- Zasady
- ResourceManagement
- RoleManagement
- UserManagement

Filtr **działania** jest oparty na wybranej kategorii i typie zasobu działania. Możesz wybrać konkretne działanie, które chcesz zobaczyć, lub wybrać wszystkie działania. 

Listę wszystkich działań związanych z inspekcją można uzyskać przy użyciu interfejs API programu Graph: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Filtr **stanu** umożliwia filtrowanie na podstawie stanu operacji inspekcji. Może to być jeden z następujących stanów:

- Wszystko
- Powodzenie
- Niepowodzenie

Filtr **docelowy** umożliwia wyszukiwanie określonego elementu docelowego według początku nazwy lub głównej nazwy użytkownika (UPN). Nazwy docelowej i nazwy UPN są rozróżniane wielkości liter. 

Filtr **zainicjowane przez** umożliwia określenie, jak zaczyna się nazwa aktora lub uniwersalna nazwa główna (UPN). Nazwa i nazwy UPN są rozróżniane wielkości liter.

Filtr **zakresu dat** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 7 dni
- 24 godziny
- Niestandardowy

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Możesz również pobrać przefiltrowane dane, do 250 000 rekordów, wybierając przycisk **Pobierz** . Dzienniki można pobrać w formacie CSV lub JSON. Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania raportów Azure Active Directory](reference-reports-data-retention.md).

![Dzienniki inspekcji](./media/concept-audit-logs/download.png "Dzienniki inspekcji")

## <a name="audit-logs-shortcuts"></a>Skróty dzienników inspekcji

Poza usługą **Azure Active Directory** witryna Azure Portal zapewnia dwa dodatkowe punkty wejścia do danych inspekcji:

- Użytkownicy i grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-and-groups-audit-logs"></a>Dzienniki inspekcji użytkowników i grup

Za pomocą raportów inspekcji opartych na użytkownikach i grupach można uzyskać odpowiedzi na pytania, takie jak:

- Jakie typy aktualizacji zostały zastosowane przez użytkowników?

- Ilu użytkowników zostało zmienionych?

- Ile haseł zostało zmienionych?

- Jakich zmian dokonał administrator w katalogu?

- Jakie grupy zostały dodane?

- Czy istnieją grupy, w których dokonano zmiany członkostwa?

- Czy właściciele grup zostali zmienieni?

- Jakie licencje zostały przypisane do grupy lub użytkownika?

Jeśli chcesz przejrzeć tylko dane inspekcji powiązane z użytkownikami, możesz znaleźć widok filtrowany w obszarze **dzienniki inspekcji** w sekcji **monitorowanie** na karcie **Użytkownicy** . Ten punkt wejścia ma **UserManagement** jako wybraną kategorię.

![Dzienniki inspekcji](./media/concept-audit-logs/users.png "Dzienniki inspekcji")

Jeśli chcesz przejrzeć tylko dane inspekcji powiązane z grupami, możesz znaleźć widok filtrowany w obszarze **dzienniki inspekcji** w sekcji **monitorowanie** na karcie **grupy** . Ten punkt wejścia ma **GroupManagement** jako wybraną kategorię.

![Dzienniki inspekcji](./media/concept-audit-logs/groups.png "Dzienniki inspekcji")

### <a name="enterprise-applications-audit-logs"></a>Dzienniki inspekcji aplikacji dla przedsiębiorstw

Za pomocą raportów inspekcji opartych na aplikacjach można uzyskać odpowiedzi na pytania, takie jak:

* Jakie aplikacje zostały dodane lub zaktualizowane?
* Jakie aplikacje zostały usunięte?
* Czy nazwa główna usługi dla aplikacji została zmieniona?
* Czy nazwy aplikacji zostały zmienione?
* Kto udzielił zezwolenia dla aplikacji?

Jeśli chcesz przejrzeć dane inspekcji powiązane z aplikacjami, możesz znaleźć widok filtrowany w obszarze **dzienniki inspekcji** w sekcji **działanie** w bloku **aplikacje dla przedsiębiorstw** . Ten punkt wejścia ma **aplikacje dla przedsiębiorstw** , które są wybierane jako **Typ aplikacji**.

![Dzienniki inspekcji](./media/concept-audit-logs/enterpriseapplications.png "Dzienniki inspekcji")

## <a name="office-365-activity-logs"></a>Dzienniki aktywności pakietu Office 365

Dzienniki aktywności pakietu Office 365 można wyświetlić w [centrum administracyjnym Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Mimo że działania pakietu Office 365 i dzienniki aktywności usługi Azure AD współdzielą wiele zasobów katalogu, tylko Microsoft 365 centrum administracyjnym zapewnia pełny wgląd w dzienniki aktywności pakietu Office 365. 

Możesz również programowo uzyskać dostęp do dzienników aktywności pakietu Office 365 przy użyciu [interfejsów API zarządzania pakietu office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Następne kroki

- [Informacje o działaniach inspekcji usługi Azure AD](reference-audit-activities.md)
- [Dokumentacja przechowywania raportów usługi Azure AD](reference-reports-data-retention.md)
- [Informacje o opóźnieniu rejestrowania w usłudze Azure AD](reference-reports-latencies.md)