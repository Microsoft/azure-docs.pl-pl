---
title: Microsoft 365 udostępniania zewnętrznego i współpracy B2B — Azure AD
description: W tym artykule omówiono udostępnianie zasobów partnerom zewnętrznym przy użyciu Microsoft 365 i Azure Active Directory współpracy B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0950c92b5a7bb1e782537aa46acad2e949e565c7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441797"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Microsoft 365 udostępnianie zewnętrzne i Azure Active Directory (Azure AD) Współpraca B2B

Zarówno w przypadku współpracy B2B usługi Azure AD, jak i Microsoft 365 udostępniania zewnętrznego (OneDrive, SharePoint Online, ujednoliconych grup itp.) użytkownicy zewnętrzni są uwierzytelniani przy użyciu usługi Azure AD B2B.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Jak usługa Azure AD B2B różni się od udostępniania zewnętrznego w usłudze SharePoint Online?

Usługa OneDrive/SharePoint Online ma osobny Menedżer zaproszeń. Obsługa udostępniania zewnętrznego w usłudze OneDrive/SharePoint Online rozpoczęta przed opracowaniem pomocy technicznej usługi Azure AD. W czasie usługa OneDrive/SharePoint Online — Udostępnianie zewnętrzne zostało naliczone kilka funkcji i wiele milionów użytkowników, którzy korzystają z wbudowanego wzorca udostępniania produktu. Jednak istnieją pewne delikatne różnice między działaniem usługi OneDrive/SharePoint Online i sposobem działania współpracy z usługą Azure AD B2B. Więcej informacji na temat udostępniania zewnętrznego usługi OneDrive/SharePoint Online można znaleźć w temacie [udostępnianie zewnętrzne — Omówienie](/sharepoint/external-sharing-overview). Proces ten zwykle różni się od B2B usługi Azure AD w następujący sposób:

- Usługa OneDrive/SharePoint Online dodaje użytkowników do katalogu po zrealizowaniu zaproszeń przez użytkowników. Dlatego przed wykupu nie widzisz użytkownika w portalu usługi Azure AD. Jeśli inna witryna zaprasza użytkownika w międzyczasie, zostanie wygenerowane nowe zaproszenie. Jednak w przypadku korzystania z funkcji współpracy B2B usługi Azure AD użytkownicy są dodawani natychmiast po zaproszeniu, aby były wyświetlane wszędzie.

- Środowisko pracy w usłudze OneDrive/SharePoint Online wygląda inaczej niż środowisko współpracy B2B usługi Azure AD. Gdy użytkownik zrealizuje zaproszenie, doświadczenia wyglądają podobnie.

- Zaproszeni użytkownicy funkcji współpracy B2B usługi Azure AD mogą być wybierani z okien dialogowych udostępniania usługi OneDrive/SharePoint Online. Zaproszeni użytkownicy usługi OneDrive/SharePoint Online również są wyświetlani w usłudze Azure AD po zrealizowaniu zaproszeń.

- Wymagania licencyjne różnią się. Dla każdej płatnej licencji usługi Azure AD można zezwolić maksymalnie 5 użytkownikom-Gościom na dostęp do płatnych funkcji usługi Azure AD. Aby dowiedzieć się więcej o licencjonowaniu, zobacz temat [Licencjonowanie B2B usługi Azure AD](./external-identities-pricing.md) i ["co to jest użytkownik zewnętrzny" w temacie Omówienie udostępniania zewnętrznego w usłudze SharePoint Online](/sharepoint/external-sharing-overview#what-happens-when-users-share).

Aby zarządzać udostępnianiem zewnętrznym w usłudze OneDrive/SharePoint Online przy użyciu funkcji współpracy B2B usługi Azure AD, ustaw ustawienie Udostępnianie zewnętrzne w usłudze OneDrive/SharePoint Online, aby **zezwolić na udostępnianie tylko użytkownikom zewnętrznym, który już istnieje w katalogu organizacji**. Użytkownicy mogą przechodzić do witryn udostępnionych zewnętrznie i wybierać z zewnętrznych współpracowników, które zostały dodane przez administratora. Administrator może dodać zewnętrznych współpracowników za pośrednictwem interfejsów API zaproszeń współpracy B2B.


![Ustawienia udostępniania zewnętrznego w usłudze OneDrive/SharePoint](media/o365-external-user/odsp-sharing-setting.png)

Po włączeniu udostępniania zewnętrznego możliwość wyszukiwania istniejących użytkowników-Gości w selektorze osób usługi SharePoint Online (SPO) jest domyślnie wyłączona, aby dopasować starsze zachowanie.

Tę funkcję można włączyć za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie dzierżawy i kolekcji witryn. Funkcję można ustawić za pomocą poleceń cmdlet Set-SPOTenant i Set-SPOSite, które umożliwiają członkom przeszukiwanie wszystkich istniejących użytkowników-Gości w katalogu. Zmiany w zakresie dzierżawy nie mają wpływu na już inicjowane witryny SPO.

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)
* [Delegowanie zaproszeń do współpracy B2B](delegate-invitations.md)
* [Grupy dynamiczne i współpraca B2B](use-dynamic-groups.md)
* [Rozwiązywanie problemów Azure Active Directory współpracy B2B](troubleshoot.md)