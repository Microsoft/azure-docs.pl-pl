---
title: Dodawanie i weryfikowanie niestandardowych nazw domen — Azure Active Directory | Microsoft Docs
description: Koncepcje dotyczące zarządzania i porady dotyczące zarządzania nazwą domeny w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b87dac89f991525e51d4f9dd0a92291eaac3e2c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488910"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Zarządzanie niestandardowymi nazwami domen w Azure Active Directory

Nazwa domeny to ważna część identyfikatora dla wielu zasobów Azure Active Directory (Azure AD): jest to część nazwy użytkownika lub adresu e-mail użytkownika, część adresu dla grupy, a czasami jest częścią identyfikatora URI aplikacji. Zasób w usłudze Azure AD może zawierać nazwę domeny, która należy do organizacji zawierającej zasób. Tylko administrator globalny może zarządzać domenami w usłudze Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-organization"></a>Ustawianie podstawowej nazwy domeny dla organizacji usługi Azure AD

Po utworzeniu organizacji początkowa nazwa domeny, taka jak "contoso.onmicrosoft.com", jest również nazwą domeny podstawowej. Domena podstawowa jest domyślną nazwą domeny dla nowego użytkownika podczas tworzenia nowego użytkownika. Ustawienie nazwy domeny podstawowej upraszcza proces tworzenia nowych użytkowników w portalu przez administratora. Aby zmienić podstawową nazwę domeny:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym w organizacji.
2. Wybierz pozycję **Azure Active Directory**.
3. Wybierz pozycję **Niestandardowe nazwy domen**.
  
   ![Otwieranie strony zarządzania użytkownikami](./media/domains-manage/add-custom-domain.png)
4. Wybierz nazwę domeny, która ma być domeną podstawową.
5. Wybierz polecenie **Utwórz podstawowe** . Potwierdź wybór po wyświetleniu monitu.
  
   ![Ustaw nazwę domeny jako podstawową](./media/domains-manage/make-primary-domain.png)

Nazwę domeny podstawowej dla organizacji można zmienić na dowolną zweryfikowaną domenę niestandardową, która nie jest federacyjny. Zmiana domeny podstawowej dla organizacji nie spowoduje zmiany nazwy użytkownika dla żadnych istniejących użytkowników.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Dodawanie niestandardowych nazw domen do organizacji usługi Azure AD

Można dodać maksymalnie 900 nazw domen zarządzanych. Jeśli konfigurujesz wszystkie domeny dla Federacji przy użyciu Active Directory lokalnych, możesz dodać do 450 nazw domen w każdej organizacji.

## <a name="add-subdomains-of-a-custom-domain"></a>Dodawanie poddomen domen niestandardowych

Jeśli chcesz dodać nazwę poddomeny, taką jak "europe.contoso.com", należy najpierw dodać i zweryfikować domenę główną, taką jak contoso.com. Poddomena jest automatycznie weryfikowana przez usługę Azure AD. Aby sprawdzić, czy dododana poddomena została zweryfikowana, Odśwież listę domen w przeglądarce.

Jeśli dodano już domenę contoso.com do jednej organizacji usługi Azure AD, można także zweryfikować poddomenę europe.contoso.com w innej organizacji usługi Azure AD. Podczas dodawania poddomeny zostanie wyświetlony monit o dodanie rekordu TXT w dostawcy hostingu usługi DNS.



## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co zrobić, jeśli zmienisz Rejestrator DNS dla niestandardowej nazwy domeny

W przypadku zmiany rejestratorów DNS nie ma dodatkowych zadań konfiguracyjnych w usłudze Azure AD. Możesz nadal używać nazwy domeny z usługą Azure AD bez przeszkód. Jeśli używasz niestandardowej nazwy domeny z Microsoft 365, Intune lub innymi usługami, które korzystają z niestandardowych nazw domen w usłudze Azure AD, zapoznaj się z dokumentacją tych usług.

## <a name="delete-a-custom-domain-name"></a>Usuń niestandardową nazwę domeny

Możesz usunąć niestandardową nazwę domeny z usługi Azure AD, jeśli Twoja organizacja nie korzysta już z tej nazwy domeny lub jeśli musisz użyć tej nazwy domeny z inną usługą Azure AD.

Aby usunąć niestandardową nazwę domeny, należy najpierw upewnić się, że żadne zasoby w organizacji nie zależą od nazwy domeny. Nie można usunąć nazwy domeny z organizacji, jeśli:

* Każdy użytkownik ma nazwę użytkownika, adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Każda grupa ma adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Dowolna aplikacja w usłudze Azure AD ma identyfikator URI aplikacji, który zawiera nazwę domeny.

Aby można było usunąć niestandardową nazwę domeny, należy zmienić lub usunąć każdy taki zasób w organizacji usługi Azure AD.

### <a name="forcedelete-option"></a>ForceDelete — opcja

Można **ForceDelete** nazwę domeny w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) lub przy użyciu [interfejsu API Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true). Te opcje używają operacji asynchronicznej i aktualizują wszystkie odwołania z niestandardowej nazwy domeny, takiej jak " user@contoso.com ", do początkowej domyślnej nazwy domeny, takiej jak " user@contoso.onmicrosoft.com ."

Aby wywołać **ForceDelete** w Azure Portal, należy się upewnić, że istnieje mniej niż 1000 odwołań do nazwy domeny i wszystkie odwołania, w których program Exchange jest usługą aprowizacji, należy zaktualizować lub usunąć w [centrum administracyjnym programu Exchange](https://outlook.office365.com/ecp/). Obejmuje to następujące grupy zabezpieczeń: Exchange Mail-Enabled i listy rozproszone; Aby uzyskać więcej informacji, zobacz [usuwanie grup zabezpieczeń z włączoną obsługą poczty](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups&preserve-view=true). Ponadto operacja **ForceDelete** nie powiedzie się, jeśli jest spełniony jeden z następujących warunków:

* Użytkownik kupił domenę za pośrednictwem usług subskrypcji domeny Microsoft 365
* Jesteś partnerem administrowania w imieniu innej organizacji klienta

Następujące akcje są wykonywane w ramach operacji **ForceDelete** :

* Zmienia nazwę UPN, EmailAddress i ProxyAddress użytkowników z odwołaniami do niestandardowej nazwy domeny do początkowej domyślnej nazwy domeny.
* Zmienia nazwę EmailAddress grup z odwołaniami do niestandardowej nazwy domeny do początkowej domyślnej nazwy domeny.
* Zmienia nazwę identifierUris aplikacji z odwołaniami do niestandardowej nazwy domeny do początkowej domyślnej nazwy domeny.

Błąd jest zwracany w przypadku:

* Liczba obiektów, których nazwa ma zostać zmieniona, jest większa niż 1000
* Jedna z aplikacji, które mają zostać zmienione, jest aplikacją z wieloma dzierżawcami

### <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: Dlaczego Usuwanie domeny kończy się niepowodzeniem z powodu błędu informującego, że w tej nazwie domeny są dostępne grupy programu Exchange Master?** <br>
Odp **.:** Obecnie niektóre grupy, takie jak Mail-Enabled grupy zabezpieczeń i listy rozproszone, są obsługiwane przez program Exchange i muszą zostać ręcznie oczyszczone w [centrum administracyjnym programu Exchange (SKK)](https://outlook.office365.com/ecp/). Może istnieć ProxyAddresses, które są zależne od niestandardowej nazwy domeny i należy je ręcznie zaktualizować do innej nazwy domeny. 

**Pytanie: jestem zalogowany jako administrator \@ contoso.com, ale nie mogę usunąć nazwy domeny "contoso.com"?**<br>
Odp **.:** Nie można odwołać się do niestandardowej nazwy domeny, którą próbujesz usunąć, w nazwie konta użytkownika. Upewnij się, że konto administratora globalnego używa początkowej domyślnej nazwy domeny (. onmicrosoft.com), takiej jak admin@contoso.onmicrosoft.com . Zaloguj się przy użyciu innego konta administratora globalnego, takiego jak admin@contoso.onmicrosoft.com lub innej niestandardowej nazwy domeny, takiej jak "fabrikam.com", w której znajduje się konto admin@fabrikam.com .

**P: kliknę przycisk Usuń domenę i zobacz `In Progress` stan operacji usuwania. Jak długo trwa? Co się stanie w przypadku niepowodzenia?**<br>
Odp **.:** Operacja Usuń domenę to asynchroniczne zadanie w tle, które zmienia nazwy wszystkich odwołań do nazwy domeny. Powinno to zakończyć się w ciągu minuty lub dwóch. Jeśli usunięcie domeny nie powiedzie się, upewnij się, że nie masz:

* Aplikacje skonfigurowane dla nazwy domeny z appIdentifierURI
* Dowolna Grupa z włączoną obsługą poczty odwołująca się do niestandardowej nazwy domeny
* Więcej niż 1000 odwołań do nazwy domeny

Jeśli okaże się, że którykolwiek z warunków nie został spełniony, ręcznie Wyczyść odwołania i spróbuj ponownie usunąć domenę.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Zarządzanie nazwami domen przy użyciu programu PowerShell lub interfejsu API Microsoft Graph

Większość zadań zarządzania dla nazw domen w Azure Active Directory można również ukończyć przy użyciu programu Microsoft PowerShell lub programowo przy użyciu interfejsu API Microsoft Graph.

* [Zarządzanie nazwami domen w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/?view=azureadps-2.0#domains&preserve-view=true)
* [Typ zasobu domeny](/graph/api/resources/domain?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>Następne kroki

* [Dodawanie niestandardowych nazw domen](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Usuwanie grup zabezpieczeń z włączoną obsługą poczty programu Exchange w centrum administracyjnym programu Exchange w przypadku niestandardowej nazwy domeny w usłudze Azure AD](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups&preserve-view=true)
* [ForceDelete niestandardową nazwę domeny za pomocą interfejsu API Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)