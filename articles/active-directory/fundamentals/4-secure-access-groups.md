---
title: Zabezpiecz dostęp zewnętrzny z grupami w Azure Active Directory i Microsoft 365
description: Grupy Azure Active Directory i Microsoft 365 mogą służyć do zwiększania bezpieczeństwa, gdy użytkownicy zewnętrzni uzyskują dostęp do zasobów.
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
ms.openlocfilehash: 3e28714e2557027a3f8f5504f7052973a77720b6
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744260"
---
# <a name="securing-external-access-with-groups"></a>Zabezpieczanie dostępu zewnętrznego z grupami 

Grupy są istotną częścią każdej strategii kontroli dostępu. Usługi Azure Active Directory (Azure AD) grupy zabezpieczeń i Microsoft 365 (M365) mogą służyć jako podstawa zabezpieczenia dostępu do zasobów.

Grupy są najlepszą opcją, która ma być używana jako podstawa dla następujących mechanizmów kontroli dostępu:

* Zasady dostępu warunkowego

* Pakiety dostępu do zarządzania prawami 

* Dostęp do zasobów M365, zespołów firmy Microsoft i witryn programu SharePoint

Grupy mają następujące role:

* Właściciele — właściciele grupy zarządzają ustawieniami grupy i jej członkostwem.

* Członkowie — członkowie, którzy dziedziczą uprawnienia i dostęp przypisany do grupy.

* Goście — Goście są członkami spoza organizacji. 

## <a name="determine-your-group-strategy"></a>Ustalanie strategii grupy

Podczas opracowywania strategii grupy w celu zabezpieczenia zewnętrznego dostępu do zasobów należy wziąć pod uwagę [pożądaną stan zabezpieczeń](1-secure-access-posture.md) , aby określić poniższe opcje.

* **Kto powinien mieć możliwość tworzenia grup?** Czy chcesz, aby tylko Administratorzy mogli tworzyć grupy, czy też chcesz, aby pracownicy i użytkownicy zewnętrzni utworzyli te grupy.

   * *Domyślnie każdy członek dzierżawy może utworzyć grupy zabezpieczeń usługi Azure AD*. 

      * Można [ograniczyć dostęp do portalu dla administratorów innych niż administratorzy](../develop/howto-restrict-your-app-to-a-set-of-users.md) i wyłączyć możliwość tworzenia grupy w programie [PowerShell.](../users-groups-roles/groups-troubleshooting.md) 

      * Możesz również skonfigurować samoobsługowe [Zarządzanie grupami w Azure Active Directory](../users-groups-roles/groups-self-service-management.md). 

   * *Domyślnie wszyscy użytkownicy mogą tworzyć grupy i grupy M365 dla wszystkich (wewnętrznych i zewnętrznych) użytkowników w dzierżawie do przyłączenia*. 

      * [Można ograniczyć tworzenie grup Microsoft 365](https://docs.microsoft.com/microsoft-365/solutions/manage-creation-of-groups?view=o365-worldwide) do członków określonej grupy zabezpieczeń. Skonfiguruj to ustawienie przy użyciu programu Windows PowerShell. 

* **Kto powinien mieć możliwość zapraszania osób do grup?** Czy wszyscy członkowie grupy mogą dodawać innych członków, czy tylko właściciele grupy dodają członków?

* **Kto może być zapraszany do grup?** Domyślnie użytkownikom zewnętrznym można dodawać do grup. 

### <a name="assign-users-to-groups"></a>Przypisywanie użytkowników do grup

Użytkownicy mogą być przypisani do grup ręcznie na podstawie atrybutów użytkownika w obiekcie użytkownika lub w innych kryteriach. Użytkownicy mogą być przypisani tylko do grup dynamicznie na podstawie ich atrybutów.

Można na przykład przypisać użytkowników do grup na podstawie ich:

* określony tytuł lub dział zadania

* organizacja partnerska, do której należą (ręcznie lub przez połączone organizacje)

* Typ użytkownika (członek lub gość)

* uczestnictwo w określonym projekcie (ręcznie)

* location

Grupy dynamiczne mogą zawierać użytkowników lub urządzenia, ale nie oba jednocześnie. Dodaj zapytania na podstawie atrybutów użytkownika, aby przypisać użytkowników do grupy dynamicznej. W poniższym przykładzie przedstawiono zapytania, które dodają użytkowników do grupy, jeśli są członkami (nie Goście) i w dziale finansowym.

![Zrzut ekranu przedstawiający Konfigurowanie reguł członkostwa dynamicznego.](media/secure-external-access/4-dynamic-membership-rules.png)

Aby uzyskać więcej informacji na temat grup dynamicznych, zobacz [Tworzenie lub aktualizowanie grupy dynamicznej w Azure Active Directory.](../users-groups-roles/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Nie używaj grup w wielu celach

W przypadku korzystania z grup w celu zapewnienia bezpieczeństwa lub dostępu do zasobów należy pamiętać, że mają one jedną funkcję. Jeśli grupa jest używana do udzielania dostępu do zasobów, nie należy jej używać w żadnym innym celu. Jeśli grupa jest używana do celów ogólnych, takich jak Definiowanie przynależności do lokalizacji lub zespołu, nie należy jej również używać w celu zabezpieczenia dostępu. 

Zalecamy stosowanie konwencji nazewnictwa dla grup zabezpieczeń, które przeznaczenie ma być jednoznaczne. Na przykład:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Typy grup

Zarówno grupy zabezpieczeń usługi Azure AD, jak i grupy Microsoft 365 można tworzyć w portalu usługi Azure AD lub w portalu administratora M365. Oba typy mogą służyć jako podstawa do zabezpieczania dostępu zewnętrznego:

|Zagadnienia do rozważenia | Grupy zabezpieczeń usługi Azure AD (ręczne i dynamiczne)| Grupy Microsoft 365 |
| - | - | - |
| Co może zawierać Grupa?| Użytkownicy<br>Grupy<br>Jednostki usługi<br>Urządzenia| Tylko użytkownicy |
| Gdzie jest utworzona grupa?| Portal usługi Azure AD<br>Portal M365 (jeśli jest włączona poczta)<br>PowerShell<br>Microsoft Graph<br>Portal użytkowników końcowych| Portal M365<br>Portal usługi Azure AD<br>PowerShell<br>Microsoft Graph<br>W aplikacjach Microsoft 365 |
| Kto jest domyślnie tworzony?| Administratorzy <br>Użytkownicy końcowi| Administratorzy<br>Użytkownicy końcowi |
| Kto może być domyślnie dodany?| Użytkownicy wewnętrzni (członkowie)| Członkowie dzierżawy i Goście z dowolnej organizacji |
| Do czego służy dostęp?| Tylko zasoby, do których jest przypisany.| Wszystkie zasoby związane z grupą:<br>(Skrzynka pocztowa grupy, witryna, zespół, rozmowy i inne dołączone zasoby M365)<br>Wszystkie inne zasoby, do których grupa jest dodawana |
| Może być używany z| Dostęp warunkowy<br>Zarządzanie prawami<br>Licencjonowanie grup| Dostęp warunkowy<br>Zarządzanie prawami<br>Etykiety poufności |



Za pomocą grup Microsoft 365 można tworzyć i zarządzać zestawem zasobów Microsoft 365, takich jak zespół i skojarzone z nią witryny i zawartość. Są one doskonałym wyborem dla nakładu pracy na podstawie projektu. 

 

## <a name="azure-ad-security-groups"></a>Grupy zabezpieczeń usługi Azure AD 

[Grupy zabezpieczeń usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) mogą zawierać użytkowników lub urządzenia i mogą być używane do zarządzania dostępem do programu 

* Zasoby platformy Azure, takie jak aplikacje Microsoft 365, aplikacje niestandardowe i aplikacje SaaS (Software as a Service), takie jak usługi ServiceNow usługi Dropbox.

* Dane i subskrypcje platformy Azure.

* Usługi platformy Azure.

Grupy zabezpieczeń usługi Azure AD mogą również służyć do:

* Przypisywanie licencji dla usług takich jak M365, Dynamics 365 i Enterprise Mobility and Security. Aby uzyskać więcej informacji, zobacz [Licencjonowanie oparte na grupach](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal).

* Przypisywanie podwyższonych uprawnień. Aby uzyskać więcej informacji, zobacz temat [Używanie grup w chmurze do zarządzania przypisaniami ról (wersja zapoznawcza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)). 

Aby utworzyć grupę [w Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) przejdź do Azure Active Directory, a następnie do grupy. Możesz również utworzyć grupy zabezpieczeń usługi Azure AD za pomocą [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets). 

> [!NOTE]
> Grupy zabezpieczeń można używać do przypisywania do 1500 aplikacji, ale nie do innych. 

![Zrzut ekranu przedstawiający tworzenie grupy zabezpieczeń.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Aby utworzyć grupę zabezpieczeń z włączoną obsługą poczty, przejdź do [centrum administracyjnego Microsoft 365](https://admin.microsoft.com/)**. Nie można go utworzyć w portalu usługi Azure AD. 
<br>W momencie tworzenia należy włączyć grupę zabezpieczeń dla wiadomości e-mail. Nie można włączyć go później.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Organizacje hybrydowe i grupy zabezpieczeń usługi Azure AD

Organizacje hybrydowe mają zarówno infrastrukturę lokalną, jak i infrastrukturę chmurową usługi Azure AD. Wiele organizacji hybrydowych, które używają Active Directory tworzyć swoje grupy zabezpieczeń lokalnie i synchronizować je z chmurą. Korzystając z tej metody, można dodać tylko użytkowników w środowisku lokalnym do grup zabezpieczeń.

**Ochrona lokalnej infrastruktury przed naruszeniem zabezpieczeń może być używana w celu uzyskania dostępu do dzierżawy Microsoft 365**. Zapoznaj się z tematem [ochrona Microsoft 365 z lokalnych ataków](https://aka.ms/protectm365) na wskazówki.

## <a name="microsoft-365-groups"></a>Grupy Microsoft 365

[Grupy Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) to fundamentowa usługa członkowska, która zapewnia wszystkie prawa dostępu w M365. Można je utworzyć na podstawie [Azure Portal](https://portal.azure.com/)lub [portalu M365](https://admin.microsoft.com/). Po utworzeniu grupy M365 udzielany jest dostęp do grupy zasobów używanych do współpracy. Aby zapoznać się z pełną listą tych zasobów, zobacz [Omówienie grup Microsoft 365 dla administratorów](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) .

Grupy M365 mają następujące wszystkie szczegóły dla ich ról

* **Właściciele** — właściciele grupy mogą dodawać i usuwać członków oraz mieć unikatowe uprawnienia, takie jak możliwość usuwania konwersacji z udostępnionej skrzynki odbiorczej lub zmiany ustawień grupy. Właściciele grupy mogą zmieniać nazwę grupy, aktualizować opis lub obraz i nie tylko.

* **Członkowie** — członkowie mogą uzyskiwać dostęp do wszystkich elementów w grupie, ale nie mogą zmieniać ustawień grupy. Domyślnie członkowie grupy mogą zapraszać Gości do dołączenia do grupy, chociaż można [kontrolować to ustawienie](https://docs.microsoft.com/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide).

* **Goście** — Goście grupy są członkami spoza organizacji. Domyślnie Goście mają pewne ograniczenia dotyczące funkcjonalności w zespołach.

 

### <a name="m365-group-settings"></a>Ustawienia grupy M365

Wybierasz alias adresu e-mail, prywatność i, czy chcesz włączyć grupę dla zespołów w czasie konfigurowania. 

![Zrzut ekranu przedstawiający edytowanie ustawień grupy Microsoft 365](media/secure-external-access/4-edit-group-settings.png)

Po zakończeniu instalacji należy dodać członków i skonfigurować ustawienia dotyczące użycia poczty e-mail itp.

### <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określ żądany stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używaj grup do zabezpieczeń](4-secure-access-groups.md) (Jesteś tutaj).

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)