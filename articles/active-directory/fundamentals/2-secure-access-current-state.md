---
title: Odkryj bieżący stan współpracy zewnętrznej z Azure Active Directory
description: Poznaj metody, aby poznać bieżący stan swojej współpracy.
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
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553429"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Odkryj bieżący stan współpracy zewnętrznej w organizacji 

Przed rozpoczęciem odnajdywania bieżącego stanu współpracy zewnętrznej należy [określić żądany stan zabezpieczeń](1-secure-access-posture.md). Należy wziąć pod uwagę potrzeby organizacji scentralizowanej i delegowanej kontroli oraz wszelkie odpowiednie cele ładu, przepisów i zgodności. 

Osoby w organizacji prawdopodobnie już współpracują z użytkownikami z innych organizacji. Współpraca może być za pośrednictwem funkcji w aplikacjach biurowych, takich jak Microsoft 365, wysyłanie pocztą e-mail lub w inny sposób udostępnianie zasobów użytkownikom zewnętrznym. Filary planu ładu będą zgodne z wykryciem 
*   Użytkownicy inicjujący współpracę zewnętrzną.
*   Użytkownicy zewnętrzni i organizacje, z którymi pracujesz.
*   dostęp przyznawany użytkownikom zewnętrznym.


## <a name="users-initiating-external-collaboration"></a>Użytkownicy inicjujący współpracę zewnętrzną

Użytkownicy inicjujący współpracę zewnętrzną najlepiej znają aplikacje, które są najbardziej odpowiednie do współpracy zewnętrznej, i kiedy ten dostęp powinien się zakończyć. Zrozumienie tych użytkowników może pomóc określić, kto ma mieć uprawnienia do delegowania użytkownikom zewnętrznym, tworzyć pakiety dostępu i dokończyć przeglądy dostępu.

Aby znaleźć użytkowników, którzy obecnie współpracują z usługą, zapoznaj się z [dziennikiem inspekcji Microsoft 365 pod kątem działań związanych z udostępnianiem i żądaniem dostępu](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). Możesz również przejrzeć [Dziennik inspekcji usługi Azure AD, aby uzyskać szczegółowe informacje na temat tego, kto zaprosił użytkowników B2B](../external-identities/auditing-and-reporting.md) do katalogu.

## <a name="find-current-collaboration-partners"></a>Znajdź bieżących partnerów współpracy

Użytkownicy zewnętrzni mogą być użytkownikami [B2B usługi Azure AD](../external-identities/what-is-b2b.md) (preferowanymi) z poświadczeniami zarządzanymi przez partnera lub użytkownikami zewnętrznymi przy użyciu poświadczeń inicjowanych lokalnie. Ci użytkownicy są zwykle (ale nie zawsze) oznaczone atrybutem UserType gościa. Użytkowników-Gości można wyliczyć za pomocą [interfejsu API Microsoft Graph](/graph/api/user-list?tabs=http), [programu PowerShell](/graph/api/user-list?tabs=http)lub [Azure Portal](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Użyj domen poczty e-mail i właściwości NazwaFirmy

Organizacje zewnętrzne mogą być określane przez nazwy domen adresów e-mail użytkowników zewnętrznych. Jeśli są obsługiwane dostawcy tożsamości klientów, np. Google, może to nie być możliwe. W takim przypadku zalecamy zapisanie atrybutu NazwaFirmy, aby jasno określić organizację zewnętrzną użytkownika.

### <a name="use-allow-or-deny-lists"></a>Użyj list dozwolonych lub zablokowanych

Zastanów się, czy organizacja chce zezwolić na współpracę tylko z określonymi organizacjami. Alternatywnie należy rozważyć, czy organizacja chce blokować współpracę z określonymi organizacjami.  Na poziomie dzierżawy istnieje [Lista dozwolonych lub zablokowanych](../external-identities/allow-deny-list.md), która może służyć do kontrolowania ogólnych zaproszeń B2B i wykupu niezależnie od źródła (np. zespołów, SharePoint i Azure Portal).
W przypadku korzystania z funkcji zarządzania prawami można także przekazywać pakiety dostępu do podzbioru partnerów przy użyciu określonych ustawień połączonych organizacji, jak pokazano poniżej.


![Zrzut ekranu listy Zezwalaj na odmowę podczas tworzenia nowego pakietu dostępu.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Znajdowanie dostępu udzielanego użytkownikom zewnętrznym

Po utworzeniu spisu użytkowników zewnętrznych i organizacji można określić dostęp udzielony dla tych użytkowników przy użyciu interfejsu API Microsoft Graph, aby określić [członkostwo w grupach](/graph/api/resources/groups-overview) usługi Azure AD lub [bezpośrednie przypisanie aplikacji](/graph/api/resources/approleassignment) w usłudze Azure AD.


### <a name="enumerate-application-specific-permissions"></a>Wyliczanie uprawnień specyficznych dla aplikacji

Można również wykonać Wyliczenie uprawnień specyficznych dla aplikacji. Na przykład możesz programowo wygenerować raport o uprawnieniach dla usługi SharePoint Online za pomocą [tego skryptu](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Zapoznaj się z tym tematem, aby uzyskać dostęp do wszystkich aplikacji o znaczeniu biznesowym i ważnych dla firmy, aby uzyskać pełną świadomość dostępu zewnętrznego.

### <a name="detect-ad-hoc-sharing"></a>Wykrywanie udostępniania ad hoc
Jeśli w Twoim planie poczty e-mail i sieci będzie można zbadać zawartość udostępnioną za pośrednictwem poczty e-mail lub nieautoryzowane oprogramowanie jako usługa (SaaS). [Ochrona przed utratą danych Microsoft 365](/microsoft-365/compliance/data-loss-prevention-policies) pomaga identyfikować, zapobiegać i monitorować przypadkowe udostępnianie poufnych informacji w infrastrukturze Microsoft 365. [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) może pomóc w zidentyfikowaniu użycia nieautoryzowanych aplikacji SaaS w danym środowisku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określanie stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md) (Jesteś tutaj).

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)