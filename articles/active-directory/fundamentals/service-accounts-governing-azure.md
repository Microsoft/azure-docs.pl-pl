---
title: Zarządzanie kontami usług Azure Active Directory
description: Zasady i procedury zarządzania cyklem życia kont usług w Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c43125edab0f5ed097b99798ca22e5543e15a2d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693130"
---
# <a name="governing-azure-ad-service-accounts"></a>Zarządzanie kontami usług Azure AD

Istnieją trzy typy kont usług w Azure Active Directory (Azure AD): [zarządzane tożsamości](service-accounts-managed-identities.md), [nazwy główne usług](service-accounts-principal.md)i konta użytkowników, które są używane jako konta usług. Podczas tworzenia tych kont usługi do automatycznego używania są przyznawane uprawnienia dostępu do zasobów na platformie Azure i w usłudze Azure AD. Zasoby mogą obejmować aplikacje Microsoft 365 Services, oprogramowanie jako usługa (SaaS), niestandardowe aplikacje, bazy danych, systemy kadr i tak dalej. Zarządzanie kontami usług Azure AD oznacza, że można zarządzać tworzeniem, uprawnieniami i cyklem życia w celu zapewnienia bezpieczeństwa i ciągłości.

> [!IMPORTANT] 
> Nie zaleca się używania kont użytkowników jako kont usług, ponieważ są one z nieodłączą ochroną. Obejmuje to lokalne konta usług, które są synchronizowane z usługą Azure AD, ponieważ nie są konwertowane na jednostki usługi. Zamiast tego zalecamy korzystanie z zarządzanych tożsamości lub jednostek usługi. Należy pamiętać, że w tym momencie korzystanie z zasad dostępu warunkowego nie jest możliwe w przypadku jednostek usługi, ale funkcje te są dostępne.


## <a name="plan-your-service-account"></a>Planowanie konta usługi

Przed utworzeniem konta usługi lub zarejestrowaniem aplikacji należy udokumentować informacje o kluczu konta usługi. Mając udokumentowane informacje, ułatwiają efektywne monitorowanie konta i zarządzanie nim. Zalecamy zbieranie następujących danych i śledzenie ich w scentralizowanej bazie danych zarządzania konfiguracją (CMDB).

| Dane| Opis| Szczegóły |
| - | - | - |
| Właściciel| Użytkownik lub Grupa, która jest odpowiedzialna za zarządzanie i monitorowanie konta usługi.| Zapewnij właścicielowi uprawnienia niezbędne do monitorowania konta i Implementuj sposób, aby ograniczyć problemy. Rozwiązywanie problemów może odbywać się przez właściciela lub przez żądanie. |
| Przeznaczenie| Sposób użycia konta.| Mapowanie konta usługi do określonej usługi, aplikacji lub skryptu. Unikaj tworzenia kont usługi wieloskładnikowej. |
| Uprawnienia (zakresy)| Przewidziany zestaw uprawnień.| Udokumentowanie zasobów, do których ma dostęp, oraz uprawnień do tych zasobów. |
| Link CMDB| Połącz z zasobami, które mają być dostępne, oraz skrypty, w których używane jest konto usługi.| Upewnij się, że są udokumentowane właściciele zasobów i skryptów, aby można było komunikować wszystkie niepotrzebne efekty nadrzędne i podrzędne zmian. |
| Ocena ryzyka| Ryzyko i wpływ na działanie w przypadku naruszenia zabezpieczeń konta.| Te informacje służą do zawężenia zakresu uprawnień i określania, kto powinien mieć dostęp do informacji o koncie. |
| Okres przeglądu| Harmonogram, według którego konto usługi ma być przeglądane przez właściciela.| Służy do zaplanowania komunikacji i przeglądów recenzji. Zapoznaj się z informacjami o tym, co się stanie, jeśli przegląd nie zostanie przeprowadzony przez określony czas po upływie zaplanowanego okresu recenzji. |
| Okres istnienia| Przewidywany maksymalny okres istnienia konta.| Użyj tego do zaplanowania komunikacji z właścicielem i ostatecznie Wyłącz, a następnie usuń konta. Jeśli to możliwe, ustaw datę wygaśnięcia poświadczeń, w której nie można automatycznie przecofać poświadczeń. |
| Nazwa| Znormalizowana nazwa konta| Utwórz schemat nazewnictwa dla wszystkich kont usług, aby można było łatwo wyszukiwać, sortować i filtrować konta usług. |


## <a name="use-the-principle-of-least-privileges"></a>Stosuj zasadę najniższych uprawnień
Udziel kontu usługi tylko uprawnień niezbędnych do wykonywania swoich zadań i nie tylko. Jeśli konto usługi wymaga uprawnień wysokiego poziomu, na przykład poziom uprawnień administratora globalnego, Oceń przyczynę i spróbuj zmniejszyć niezbędne uprawnienia.

Zalecamy stosowanie następujących zasad dotyczących uprawnień konta usługi.

**Uprawnienia**

* Nie należy przypisywać wbudowanych ról do kont usług. Zamiast tego należy użyć [OAuth2ego modelu przydzielenia uprawnień dla Microsoft Graph](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant?view=graph-rest-1.0),

* Jeśli jednostka usługi musi mieć przypisaną rolę uprzywilejowaną, należy rozważyć przypisanie [roli niestandardowej](https://docs.microsoft.com/azure/active-directory/roles/custom-create) z określonym, wymaganym uprzywilejowanym, w sposób związany z czasem.

* Nie dołączaj kont usług jako członków grup z podwyższonym poziomem uprawnień. 

* [Użyj programu PowerShell, aby wyliczyć członków ról uprzywilejowanych](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0), takich jak   
`Get-AzureADDirectoryRoleMember`i filtru dla elementu ObjectType "Nazwa główna usługi".

   lub użyj  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Zakresy OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) umożliwiają ograniczenie funkcjonalności konta usługi do uzyskiwania dostępu do zasobu.
* Jednostki usługi i zarządzane tożsamości mogą korzystać z zakresów OAuth 2,0 w delegowanym kontekście, który personifikuje zalogowanego użytkownika lub jako konto usługi w kontekście aplikacji. W kontekście aplikacji nie jest zalogowany.

* Sprawdź, czy w polu Zakresy kont usługi są wymagane zasoby, aby upewnić się, że są one odpowiednie. Na przykład jeśli konto żąda plików. ReadWrite. All, Oceń, czy w rzeczywistości wymaga tylko pliku. Read. ALL. Aby uzyskać więcej informacji o uprawnieniach, zobacz [Microsoft Graph informacje o uprawnieniach](https://docs.microsoft.com/graph/permissions-reference).

* Upewnij się, że masz zaufanie do deweloperów aplikacji lub interfejsu API z dostępem żądanym do Twoich zasobów.

**Czas trwania**

* Ogranicz poświadczenia konta usługi (klucz tajny klienta, certyfikat) do przewidywanego okresu użycia.

* Zaplanuj okresowe przeglądy użycia i przeznaczenia kont usług. Upewnij się, że przeglądy są przeprowadzane przed wygaśnięciem konta.

Jeśli masz jasne zrozumienie przeznaczenia, zakresu i wymaganych uprawnień, Utwórz konto usługi. 

[Tworzenie tożsamości zarządzanych i korzystanie z nich](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

[Tworzenie i używanie jednostek usługi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

Jeśli jest to możliwe, użyj zarządzanej tożsamości. Jeśli nie możesz użyć tożsamości zarządzanej, użyj nazwy głównej usługi. Jeśli nie możesz użyć nazwy głównej usługi, a następnie użyj tylko konta użytkownika usługi Azure AD.

 

## <a name="build-a-lifecycle-process"></a>Kompiluj proces cyklu życia

Zarządzanie cyklem życia konta usługi rozpoczyna się od zaplanowania i zakończenia jego trwałego usunięcia. 

Ten artykuł został wcześniej omówiony w części Planowanie i tworzenie. Należy również monitorować, przeglądać uprawnienia, określać ciągłe użycie konta i ostatecznie anulować obsługę konta.

### <a name="monitor-service-accounts"></a>Monitorowanie kont usług

Aktywnie Monitoruj konta usług, aby upewnić się, że wzorce użycia konta usługi odzwierciedlają zamierzone wzorce i że konto usługi jest nadal używane.

**Zbierz i monitoruj logowanie do konta usługi przy użyciu jednej z następujących metod:**

* Korzystanie z dzienników Sign-In usługi Azure AD w portalu usługi Azure AD.

* Eksportowanie dzienników Sign-In usługi Azure AD do [usługi Azure Storage](https://docs.microsoft.com/azure/storage/), [azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)lub [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/logs/data-platform-logs).


![Zrzut ekranu przedstawiający ekran logowania jednostki usługi.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Analiza, która ma być wyszukiwana w dziennikach Sign-In, obejmuje następujące funkcje:**

* Czy istnieją konta usług, które nie logują się już do dzierżawy?

* Czy wzorce logowania są zmieniane na kontach usług?

Zalecamy wyeksportowanie dzienników logowania usługi Azure AD i zaimportowanie ich do istniejących narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), takich jak Azure — wskaźnik. Użyj SIEM, aby utworzyć alerty i pulpity nawigacyjne.

### <a name="review-service-account-permissions"></a>Przejrzyj uprawnienia konta usługi

Regularnie sprawdzaj przyznane uprawnienia i zakresy dostępne dla kont usług, aby sprawdzić, czy można je zmniejszyć.

* Użyj [programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant?view=azureadps-2.0) , aby [skompilować automatyzację do sprawdzania i dokumentowania](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) zakresów, do których wyraża zgodę na konto usługi.

* Użyj programu PowerShell, aby [przejrzeć istniejące poświadczenia nazw podmiotów usługi](https://github.com/AzureAD/AzureADAssessment) i sprawdzić ich poprawność.

* Nie ustawiaj poświadczeń jednostki usługi na "nigdy nie wygasa".

* Użyj certyfikatów lub poświadczeń przechowywanych w magazynie kluczy platformy Azure, jeśli jest to możliwe.

Bezpłatny przykład programu PowerShell firmy Microsoft zbiera informacje o OAuth2 i poświadczeniach jednostki usługi, rejestruje je w pliku wartości rozdzielanych przecinkami (CSV) oraz Power BI przykładowym pulpicie nawigacyjnym do interpretacji i używania danych. Aby uzyskać więcej informacji, zobacz [AzureAD/AzureADAssessment: narzędzia do oceny stanu i konfiguracji dzierżawy usługi Azure AD (GitHub.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Ponowne zatwierdzenie użycia konta usługi

Ustanów proces przeglądu, aby upewnić się, że konta usług są regularnie przeglądane przez właścicieli i członków zespołu IT w regularnych odstępach czasu. 

**Proces powinien obejmować:**

* Jak określić cykl przeglądu poszczególnych kont usług (powinien być udokumentowany w Twoim CMDB).

* Komunikacja do właściciela i zabezpieczeń lub zespołów IT przed rozpoczęciem przeglądu.

* Chronometraż i zawartość komunikatu ostrzegawczego, jeśli nie podano przeglądu.

* Instrukcje dotyczące czynności, które należy wykonać, jeśli właściciele nie mogą sprawdzić lub odpowiedzieć. Na przykład możesz chcieć wyłączyć (ale nie usuwać) konto do momentu ukończenia przeglądu.

* Instrukcje dotyczące określania zależności nadrzędnych i podrzędnych oraz powiadamiania innych właścicieli zasobów o wszelkich efektach.

**Przegląd powinien obejmować właściciela i partnera IT poświadczający, że:**

* Konto jest nadal konieczne.

* Uprawnienia przyznane kontu są odpowiednie i niezbędne lub zażądano zmiany.

* Dostęp do konta i jego poświadczeń jest kontrolowany.

* Poświadczenia, których używa konto, są odpowiednie w odniesieniu do ryzyka, z którym konto zostało ocenione (zarówno typ poświadczenia, jak i okres istnienia poświadczenia)

* Ocena ryzyka konta nie zmieniła się od czasu ostatniej recertyfikacji

* Aktualizacja w oczekiwanym okresie istnienia konta oraz Data kolejnej recertyfikacji.

### <a name="deprovision-service-accounts"></a>Cofanie aprowizacji kont usługi

* * Wycofaj obsługę administracyjną kont usługi w następujących okolicznościach: * * * *

* Skrypt lub aplikacja, dla której zostało utworzone konto usługi, zostało wycofane.

* Funkcja w skrypcie lub aplikacji używanej przez konto usługi (na przykład dostęp do określonego zasobu) jest wycofywana.


* Konto usługi jest zastępowane innym kontem usługi.

* Wygasłe poświadczenia lub konto jest w inny sposób niefunkcjonalne i nie ma żadnych skarg.

**Proces anulowania aprowizacji powinien obejmować następujące zadania.**

1. Po cofnięciu aprowizacji skojarzonej aplikacji lub skryptu Monitoruj konta usługi [logowania](../reports-monitoring/concept-all-sign-ins#sign-ins-report.md) i dostępu do zasobów.

   * Jeśli konto nadal jest aktywne, określ, jak jest używane przed wykonaniem kolejnych kroków.
 
2. Jeśli jest to tożsamość usługi zarządzanej, Wyłącz logowanie przy użyciu konta usługi, ale nie usuwaj go z katalogu.

3. Odwołaj przypisania ról i OAuth2 zgodę na konto usługi.

4. Po upływie określonego czasu i bardzo dużo ostrzeżeń dla właścicieli Usuń konto usługi z katalogu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat zabezpieczania kont usług platformy Azure, zobacz:

[Wprowadzenie do kont usług platformy Azure](service-accounts-introduction-azure.md)

[Zabezpieczanie tożsamości zarządzanych](service-accounts-managed-identities.md)

[Zabezpieczanie zasad usługi](service-accounts-principal.md)




 

 
