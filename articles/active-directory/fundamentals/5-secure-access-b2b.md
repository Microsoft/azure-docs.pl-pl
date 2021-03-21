---
title: Przejście do regulowanej współpracy przy Azure Active Directory współpracy B2B
description: Przenieś do współpracy regulowanej przy użyciu funkcji współpracy B2B usługi Azure AD.
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
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648591"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Przejście do regulowanej współpracy przy Azure Active Directory współpracy B2B 

Rozpoczęcie współpracy w obszarze kontroli jest kluczem do zabezpieczania zewnętrznego dostępu do zasobów. Przed przejściem do następnego artykułu upewnij się, że masz:

* [Określono stan zabezpieczeń](1-secure-access-posture.md)

* [Odnaleziono bieżący stan](2-secure-access-current-state.md)

* [Utworzono plan zabezpieczeń](3-secure-access-plan.md)

* [Zrozumienie, jak grupy i zabezpieczenia współdziałają ze sobą](4-secure-access-groups.md)

Po wykonaniu tych czynności wszystko jest gotowe do przejścia do kontrolowanej współpracy. Ten artykuł przeprowadzi Cię przez proces przenoszenia całej współpracy zewnętrznej do [Azure Active Directory współpracy B2B](../external-identities/what-is-b2b.md) (Azure AD B2B). Usługa Azure AD B2B to funkcja [tożsamości zewnętrznych usługi Azure AD](../external-identities/compare-with-b2c.md).

## <a name="control-who-your-organization-collaborates-with"></a>Kontroluj, komu Twoja organizacja współpracuje z

Należy zdecydować, czy należy ograniczyć zakres organizacji, z którymi użytkownicy mogą pracować, i kto w organizacji może inicjować współpracę. Większość organizacji przyjmuje podejście umożliwiające jednostkom biznesowym decydowanie o współpracy i delegowanie zatwierdzenia oraz nadzoru zgodnie z potrzebami. Na przykład niektóre organizacje rządowe, edukacja i usługi finansowe nie zezwalają na otwieranie współpracy. Możesz użyć funkcji usługi Azure AD, aby określić zakres współpracy, jak to opisano w pozostałej części tej sekcji.

### <a name="determine-collaboration-partners"></a>Określanie partnerów współpracy

Najpierw upewnij się, że zostały udokumentowane organizacje, w których obecnie pracujesz, oraz domeny dla użytkowników tych organizacji. Jeden partner współpracy może mieć wiele domen. Na przykład partner może mieć wiele jednostek gospodarczych z oddzielnymi domenami.

Następnie ustal, czy chcesz włączyć przyszłą współpracę z usługą 

* Dowolna domena (najbardziej włącznie)

* wszystkie domeny z wyjątkiem tych jawnie odmowa 

* tylko określone domeny (najbardziej restrykcyjne)

> [!NOTE]
> Im bardziej restrykcyjne ustawienia współpracy, tym bardziej prawdopodobnie użytkownicy przechodzą poza zatwierdzoną strukturę współpracy. Zalecamy umożliwienie szerszej współpracy, dzięki której będziesz mieć dostęp do potrzeb związanych z bezpieczeństwem, i ściśle sprawdzają, czy nie są nadmiernie restrykcyjne. 

Należy również zauważyć, że ograniczenie do pojedynczej domeny może przypadkowo uniemożliwić autoryzowaną współpracę z organizacjami, które mają inne niepowiązane domeny dla swoich użytkowników. Na przykład w przypadku prowadzenia działalności w organizacji contoso pierwszy punkt kontaktu z firmą Contoso może być jednym z pracowników korzystających z usługi USA, którzy mają wiadomość e-mail z domeną "com". Jeśli jednak zezwolisz tylko na domenę ". com", możesz przypadkowo pominąć ich pracowników w Kanadzie, którzy mają domenę ". ca". 

Istnieją sytuacje, w których chcemy zezwolić tylko określonym partnerom współpracy. Na przykład system akademicki może chcieć zezwolić na dostęp do dzierżawy zasobów tylko własnemu wykładowcy. Lub konglomerat może chcieć zezwolić tylko określonym podmiotom zależnym na współpracę ze sobą w celu zapewnienia zgodności z wymaganą strukturą.

#### <a name="using-allow-and-deny-lists"></a>Używanie list dozwolonych i Odmów

Lista dozwolonych lub lista zablokowanych może służyć do [ograniczania zaproszeń do użytkowników B2B](../external-identities/allow-deny-list.md) z określonych organizacji. Można użyć tylko listy dozwolonych lub zablokowanych, a nie obu.

* [Lista dozwolonych](../external-identities/allow-deny-list.md) ogranicza współpracę tylko do tych domen wymienionych; wszystkie inne domeny są skutecznie na liście Odmów.

* [Lista Odmów](../external-identities/allow-deny-list.md) pozwala na współpracę z dowolną domeną, a nie na liście Odmów.

> [!IMPORTANT]
> Te listy nie dotyczą użytkowników, którzy znajdują się już w katalogu. Nie dotyczą one również usługi OneDrive dla firm i programu SharePoint — Zezwalaj na używanie oddzielnych list Odmów.

Niektóre organizacje używają listy znanych nieprawidłowych domen aktora dostarczonych przez ich zarządzanego dostawcę zabezpieczeń dla ich listy Odmów. Na przykład jeśli organizacja ma legalną działalność biznesową z firmą Contoso i używaną w domenie. com, może istnieć niezwiązana organizacja, która korzysta z domeny contoso. org, i próbuje atak wyłudzania informacji w celu personifikacji pracowników firmy Contoso. 

## <a name="control-how-external-users-gain-access"></a>Kontrolowanie sposobu uzyskiwania dostępu do użytkowników zewnętrznych

Istnieje wiele sposobów współpracy z partnerami zewnętrznymi przy użyciu usługi Azure AD B2B. Aby rozpocząć współpracę, możesz zaprosić lub w inny sposób umożliwić partnerowi dostęp do Twoich zasobów. Użytkownicy mogą uzyskać dostęp, odpowiadając na:

* Zrealizowanie [zaproszenia wysłanego za pośrednictwem poczty e-mail](../external-identities/redemption-experience.md)lub [bezpośredniego linku do udostępniania](../external-identities/redemption-experience.md) zasobu.

* Żądanie dostępu [przy użyciu utworzonej aplikacji](../external-identities/self-service-sign-up-overview.md)

* Żądanie dostępu za pomocą portalu [dostępu](../governance/entitlement-management-request-access.md)

Po włączeniu funkcji B2B usługi Azure AD można umożliwić Zapraszanie użytkowników-Gości przez bezpośrednie linki i zaproszenia e-mail. Zaproszenia za pośrednictwem poczty E-mail OTP i portalu samoobsługowego są obecnie w wersji zapoznawczej i muszą być włączone w ramach tożsamości zewnętrznych | Ustawienia współpracy zewnętrznej w portalu usługi Azure AD.

### <a name="control-who-can-invite-guest-users"></a>Kontrolowanie osób, które mogą zapraszać użytkowników-Gości

Określ, kto może zapraszać użytkowników-Gości do uzyskiwania dostępu do zasobów.

* Najbardziej restrykcyjne ustawienie to Zezwalanie tylko administratorom i użytkownikom, że [rola zapraszania gościa](../external-identities/delegate-invitations.md) może zapraszać Gości.

* Jeśli Twoje wymagania dotyczące zabezpieczeń zezwalają na to, zalecamy zezwolenie wszystkim użytkownikom z członkiem użytkownika na zapraszanie Gości.

* Określ, czy chcesz, aby użytkownicy z gościem użytkownika, który jest domyślnym typem konta dla użytkowników B2B usługi Azure AD, mogli zapraszać innych Gości. 

![Zrzut ekranu ustawień zaproszenia gościa.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Zbierz dodatkowe informacje o użytkownikach zewnętrznych

W przypadku korzystania z usługi Azure AD do zarządzania prawami do odpowiedzi można skonfigurować pytania dla użytkowników zewnętrznych. Pytania będą następnie widoczne dla osób zatwierdzających, aby ułatwić im podejmowanie decyzji. Można skonfigurować różne zestawy pytań dla każdej [zasady pakietów dostępu](../governance/entitlement-management-access-package-approval-policy.md) , tak aby osoby zatwierdzające mogły uzyskać odpowiednie informacje dotyczące dostępu, które są zatwierdzane. Na przykład jeśli jeden pakiet dostępu jest przeznaczony dla dostępu dostawcy, żądanie może zostać poproszony o podanie numeru kontraktu dostawcy. Innym pakietem dostępu przeznaczonym dla dostawców może być poproszony o ich kraj pochodzenia.

Jeśli używasz portalu samoobsługowego, możesz użyć [łączników interfejsu API](../external-identities/api-connectors-overview.md) , aby zebrać dodatkowe atrybuty dotyczące użytkowników podczas tworzenia konta. Następnie można użyć tych atrybutów do przypisania dostępu. Jeśli na przykład podczas procesu tworzenia konta zbierasz swój identyfikator dostawcy, możesz użyć tego atrybutu do dynamicznego przypisywania ich do grupy lub pakietu dostępu dla tego dostawcy. Możesz tworzyć niestandardowe atrybuty w Azure Portal i używać ich w przepływach użytkownika samoobsługowego tworzenia konta. Można również odczytywać i zapisywać te atrybuty przy użyciu [interfejsu API Microsoft Graph](../../active-directory-b2c/microsoft-graph-operations.md). 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Rozwiązywanie problemów z wykupumi zaproszeń do użytkowników usługi Azure AD

Istnieją trzy wystąpienia, gdy zaproszeni użytkownicy-Goście z partnera współpracy przy użyciu usługi Azure AD będą mieć problemy z zrealizowaniem zaproszenia.

* Jeśli jest używana lista dozwolonych, a domena użytkownika nie znajduje się na liście dozwolonych.

* Jeśli dzierżawa główna partnera współpracy ma ograniczenia dzierżawy, które uniemożliwiają współpracę z użytkownikami zewnętrznymi.

* Jeśli użytkownik nie jest częścią dzierżawy usługi Azure AD partnera. Na przykład użytkownicy są contoso.com, którzy są tylko w Active Directory (lub innym lokalnym dostawcy tożsamości), będą mogli zrealizować zaproszenia wyłącznie za pośrednictwem procesu OTP. Aby uzyskać więcej informacji, zobacz [przepływ wykupu zaproszeń](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Kontrolowanie możliwości dostępu do użytkowników zewnętrznych

Większość organizacji nie jest lity. Oznacza to, że niektóre zasoby mogą być udostępniane użytkownikom zewnętrznym, a niektóre z nich nie będą miały dostępu do nich. W związku z tym należy kontrolować dostęp użytkowników zewnętrznych. Należy rozważyć użycie funkcji [zarządzania prawami i pakietów dostępu w celu kontrolowania dostępu](6-secure-access-entitlement-managment.md) do określonych zasobów.

Domyślnie użytkownicy-Goście mogą zobaczyć informacje i atrybuty dotyczące członków dzierżawy oraz innych partnerów, w tym członkostwa w grupach. Należy wziąć pod uwagę, czy wymagania dotyczące zabezpieczeń wymagają ograniczenia dostępu użytkowników zewnętrznych do tych informacji.

![Zrzut ekranu przedstawiający Konfigurowanie ustawień współpracy zewnętrznej.](media/secure-external-access/5-external-collaboration-settings.png)

Zalecamy stosowanie następujących ograniczeń dla użytkowników-Gości.

* **Ogranicz dostęp gościa do grup przeglądania i innych właściwości w katalogu**

   * Użyj zewnętrznych ustawień współpracy, aby ograniczyć możliwość odczytywania grup, których nie są członkami.

* **Blokuj dostęp do aplikacji tylko dla pracowników**.

   * Utwórz zasady dostępu warunkowego, aby blokować dostęp do aplikacji zintegrowanych z usługą Azure AD, które są odpowiednie tylko dla użytkowników niebędących gośćmi. 

* **Blokuj dostęp do Azure Portal. Można dokonać rzadkich niepotrzebnych wyjątków**. 

   * Utwórz zasady dostępu warunkowego, które obejmują wszystkich Gości i użytkowników zewnętrznych, a następnie [Zaimplementuj zasady w celu zablokowania dostępu](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>Usuń użytkowników, którzy nie potrzebują już dostępu

Oceń bieżący dostęp, aby można było [przeglądać i usuwać użytkowników, którzy nie potrzebują już dostępu](../governance/access-reviews-external-users.md). Uwzględnij użytkowników zewnętrznych w dzierżawie jako Goście oraz z kontami członków. 

Niektóre organizacje dodaliśmy użytkowników zewnętrznych, takich jak dostawcy, partnerzy i wykonawcy jako członkowie. Te elementy członkowskie mogą mieć określony atrybut lub nazwy użytkowników zaczynające się od, na przykład

* v — dla dostawców

* p — dla partnerów

* c — dla wykonawców

Oceń wszystkich użytkowników zewnętrznych z kontami członków, aby określić, czy nadal wymagają dostępu. W takim przypadku należy przenieść tych użytkowników do usługi Azure AD B2B zgodnie z opisem w następnej sekcji.

Możesz również mieć użytkowników-Gości, którzy nie zostali zaproszeni przez Zarządzanie prawami lub B2B usługi Azure AD

Aby znaleźć tych użytkowników, możesz:

* [Znajdowanie użytkowników-Gości nie zapraszanych przez Zarządzanie uprawnieniami](../governance/access-reviews-external-users.md).

   * Udostępniamy [przykładowy skrypt programu PowerShell.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Przechodzenie tych użytkowników do użytkowników B2B usługi Azure AD, zgodnie z opisem w poniższej sekcji.

## <a name="transition-your-current-external-users-to-b2b"></a>Przeniesienie bieżących użytkowników zewnętrznych do B2B

Jeśli nie korzystasz z usługi Azure AD B2B, najkorzystniej nie masz użytkowników w dzierżawie. Zalecamy przeniesienie tych kont do zewnętrznych kont użytkowników B2B usługi Azure AD, a następnie zmianę ich typu UserType na gość. Dzięki temu można wykorzystać wiele sposobów na korzystanie z usługi Azure AD i Microsoft 365, aby umożliwić traktowanie użytkowników zewnętrznych w różny sposób. Poniżej wymieniono niektóre z tych metod:

* Łatwe dołączanie i wykluczanie użytkowników-Gości w zasadach dostępu warunkowego

* Łatwe dołączanie i wykluczanie użytkowników-Gości w pakietach dostępu i przeglądach dostępu

* Łatwe dołączanie i wykluczanie zewnętrznego dostępu do zespołów, programu SharePoint i innych zasobów.

Aby przenieść tych użytkowników wewnętrznych przy zachowaniu ich bieżącego dostępu, nazwy UPN i członkostwa w grupach, zobacz [Zapraszanie użytkowników zewnętrznych do współpracy B2B](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>Likwidowanie niepotrzebnych metod współpracy

Aby dokończyć przejście do regulowanej współpracy, należy zlikwidować niepożądane metody współpracy. Likwidowanie jest uzależnione od stopnia kontroli nad współpracą i stan bezpieczeństwa. Aby uzyskać informacje na temat informacji o tym i kontroli użytkownika końcowego, zobacz [Określanie stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md).

Poniżej przedstawiono pojazdy do współpracy, które warto oszacować.

### <a name="direct-invitation-through-microsoft-teams"></a>Bezpośrednie zaproszenie za pośrednictwem zespołów Microsoft Teams

Domyślnie zespoły umożliwiają dostęp zewnętrzny, co oznacza, że organizacja może komunikować się ze wszystkimi domenami zewnętrznymi. Jeśli chcesz ograniczyć lub zezwolić na określone domeny tylko dla zespołów, możesz to zrobić w [portalu administracyjnym zespoły](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Bezpośrednie udostępnianie za pośrednictwem programu SharePoint i usługi OneDrive

Bezpośrednie udostępnianie za pośrednictwem programów SharePoint i OneDrive pozwala dodawać użytkowników poza procesem zarządzania uprawnieniami. Aby zapoznać się z szczegółowymi krokami w tych konfiguracjach [, zobacz Zarządzanie dostępem za pomocą programu Microsoft Teams, SharePoint i OneDrive dla firm](9-secure-access-teams-sharepoint.md) . można także [zablokować użycie osobistej usługi OneDrive użytkownika](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) , jeśli jest to konieczne.

### <a name="sending-documents-through-email"></a>Wysyłanie dokumentów za pośrednictwem poczty e-mail

Użytkownicy będą wysyłać dokumenty pocztą e-mail do użytkowników zewnętrznych. Rozważ, jak chcesz ograniczyć i zaszyfrować dostęp do tych dokumentów przy użyciu etykiet czułości. Aby uzyskać więcej informacji, zobacz Zarządzanie dostępem z etykietami czułości.

### <a name="unsanctioned-collaboration-tools"></a>Narzędzia współpracy niezatwierdzonej

Narzędzia do współpracy są rozległe. Użytkownicy prawdopodobnie korzystają z wielu osób poza nimi, w tym z platform, takich jak Google Docs, DropBox, zapasowe lub powiększanie. Można zablokować korzystanie z takich narzędzi z firmowej sieci na poziomie zapory oraz za pomocą zarządzania aplikacjami mobilnymi dla urządzeń zarządzanych przez organizację. Jednak spowoduje to również zablokowanie wszelkich zaakceptowanych wystąpień tych platform i uniemożliwi dostęp z urządzeń niezarządzanych. Zablokuj platformy, których nie chcesz używać w razie potrzeby, i Utwórz zasady biznesowe bez zaakceptowania niezatwierdzonych platform w przypadku platformy, których należy użyć. 

Aby uzyskać więcej informacji na temat zarządzania niezatwierdzonymi aplikacjami, zobacz:

* [Zarządzanie połączonymi aplikacjami](/cloud-app-security/governance-actions)

* [Zaakceptowanie i odrzucanie aplikacji.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określanie stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md) (Jesteś tutaj).

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)