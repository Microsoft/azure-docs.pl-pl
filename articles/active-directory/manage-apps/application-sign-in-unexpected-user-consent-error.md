---
title: Nieoczekiwany błąd podczas wyrażania zgody na aplikację | Microsoft Docs
description: Omówienie błędów, które mogą wystąpić podczas procesu wyrażania zgody na aplikację, oraz o tym, co można z nimi zrobić
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad216d0062928fc16b0f2226daabb6258d09063c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378129"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Nieoczekiwany błąd podczas wyrażania zgody na aplikację

W tym artykule omówiono błędy, które mogą wystąpić podczas procesu wyrażania zgody na aplikację. W przypadku rozwiązywania problemów z nieoczekiwanymi monitami o wyrażenie zgody, które nie zawierają żadnych komunikatów o błędach, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD.](../develop/authentication-vs-authorization.md)

Wiele aplikacji zintegrowanych z Azure Active Directory wymaga uprawnień dostępu do innych zasobów w celu działania. Gdy te zasoby są również zintegrowane z Azure Active Directory, często żąda się uprawnień dostępu do nich przy użyciu wspólnej struktury wyrażania zgody. Zostanie wyświetlony monit o wyrażenie zgody, który zazwyczaj pojawia się przy pierwszym użyciu aplikacji, ale może również wystąpić przy kolejnym użyciu aplikacji.

Niektóre warunki muszą być spełnione, aby użytkownik wyraził zgodę na uprawnienia wymagane przez aplikację. Jeśli te warunki nie są spełnione, mogą wystąpić następujące błędy.

## <a name="requesting-not-authorized-permissions-error"></a>Błąd żądania nieu autoryzacji uprawnień
* **AADSTS90093:** &lt; ClientAppDisplayName żąda co najmniej jednego uprawnienia, do udzielania których &gt; nie masz uprawnień. Skontaktuj się z administratorem, który może wyrazić zgodę na tę aplikację w Twoim imieniu.
* **AADSTS90094:** &lt; ClientAppDisplayName wymaga uprawnień dostępu do zasobów w &gt; organizacji, które może przyznać tylko administrator. Poproś administratora o udzielenie uprawnienia do tej aplikacji, aby można było z niej korzystać.

Ten błąd występuje, gdy użytkownik, który nie jest administratorem globalnym, próbuje użyć aplikacji, która żąda uprawnień, które może przyznać tylko administrator. Ten błąd może zostać rozwiązany przez administratora, który udziela dostępu do aplikacji w imieniu swojej organizacji.

Ten błąd może również wystąpić, gdy użytkownik nie może wyrazić zgody na aplikację z powodu wykrycia przez firmę Microsoft, że żądanie uprawnień jest ryzykowne. W takim przypadku zdarzenie inspekcji zostanie również zarejestrowane z kategorią "ApplicationManagement", typem działania "Zgoda na aplikację" i przyczyną stanu "Wykryto ryzykowną aplikację".

Inny scenariusz, w którym ten błąd może wystąpić, występuje, gdy przypisanie użytkownika jest wymagane dla aplikacji, ale nie podano zgody administratora. W takim przypadku administrator musi najpierw wyrazić zgodę administratora.   

## <a name="policy-prevents-granting-permissions-error"></a>Zasady uniemożliwiają błąd udzielania uprawnień
* **AADSTS90093:** Administrator &lt; parametru tenantDisplayName ustawił zasady, które uniemożliwiają nadanie aplikacji uprawnień, &gt; &lt; których &gt; żąda. Skontaktuj się z administratorem &lt; dzierżawy tenantDisplayName, który może udzielić uprawnień do tej &gt; aplikacji w Twoim imieniu.

Ten błąd występuje, gdy administrator globalny wyłącza możliwość wyrażania zgody przez użytkowników na aplikacje, a następnie użytkownik niebędący administratorem próbuje użyć aplikacji wymagającej zgody. Ten błąd może zostać rozwiązany przez administratora, który udziela dostępu do aplikacji w imieniu swojej organizacji.

## <a name="intermittent-problem-error"></a>Sporadyczne błędy problemów
* **AADSTS90090:** Wygląda na to, że proces logowania napotkał sporadyczny problem podczas rejestrowania uprawnień, które próbowano udzielić &lt; elementowi clientAppDisplayName. &gt; Spróbuj ponownie później.

Ten błąd wskazuje, że wystąpił sporadyczny problem po stronie usługi. Problem można rozwiązać, próbując ponownie wyrazić zgodę na aplikację.

## <a name="resource-not-available-error"></a>Błąd Niedostępny zasób
* **AADSTS65005:** Aplikacja &lt; clientAppDisplayName &gt; zażądała uprawnień dostępu do &lt; zasobuAppDisplayName, &gt; który jest niedostępny. 

Skontaktuj się z deweloperem aplikacji.

##  <a name="resource-not-available-in-tenant-error"></a>Zasób jest niedostępny w błędzie dzierżawy
* **AADSTS65005:** &lt; ClientAppDisplayName żąda dostępu do zasobu zasóbAppDisplayName, który nie jest dostępny w dzierżawie &gt; &lt; Twojej &gt; &lt; organizacjiNazwawyświetlania &gt; . 

Upewnij się, że ten zasób jest dostępny, lub skontaktuj się z administratorem &lt; dzierżawyDisplayName &gt; .

## <a name="permissions-mismatch-error"></a>Błąd niezgodności uprawnień
* **AADSTS65005:** Aplikacja zażądała zgody na dostęp do zasobu &lt; resourceAppDisplayName &gt; . To żądanie nie powiodło się, ponieważ nie jest zgodne ze wstępnie skonfigurowaną aplikacją podczas rejestracji aplikacji. Skontaktuj się z dostawcą aplikacji.**

Wszystkie te błędy występują, gdy aplikacja, na które użytkownik próbuje wyrazić zgodę, żąda uprawnień dostępu do aplikacji zasobów, których nie można znaleźć w katalogu organizacji (dzierżawie). Taka sytuacja może wystąpić z kilku powodów:

-   Deweloper aplikacji klienckiej nieprawidłowo skonfigurował swoją aplikację, co powoduje żądanie dostępu do nieprawidłowego zasobu. W takim przypadku deweloper aplikacji musi zaktualizować konfigurację aplikacji klienckiej, aby rozwiązać ten problem.

-   Główna usługa reprezentująca docelową aplikację zasobów nie istnieje w organizacji ani nie istniała w przeszłości, ale została usunięta. Aby rozwiązać ten problem, należy aprowizować jednostkę usługi dla aplikacji zasobów w organizacji, aby aplikacja kliency zażądała uprawnień do tej jednostki. Jednostkę usługi można aprowizowania na wiele sposobów, w zależności od typu aplikacji, w tym:

    -   Uzyskiwanie subskrypcji dla aplikacji zasobów (aplikacje opublikowane przez firmę Microsoft)

    -   Wyrażanie zgody na aplikację zasobów

    -   Udzielanie uprawnień aplikacji za pośrednictwem Azure Portal

    -   Dodawanie aplikacji z galerii aplikacji usługi Azure AD

## <a name="risky-app-error-and-warning"></a>Ryzykowny błąd i ostrzeżenie aplikacji
* **AADSTS900941:** Wymagana jest zgoda administratora. Aplikacja jest uznawana za ryzykowną. (AdminConsentRequiredDueToRiskyApp)
* Ta aplikacja może być ryzykowna. Jeśli ufasz tej aplikacji, poproś administratora o udzielenie Ci dostępu.
* **AADSTS900981:** Otrzymano żądanie zgody administratora dotyczące ryzykownych aplikacji. (AdminConsentRequestRiskyAppWarning)
* Ta aplikacja może być ryzykowna. Kontynuuj tylko wtedy, gdy ufasz tej aplikacji.

Oba te komunikaty będą wyświetlane, gdy firma Microsoft ustali, że żądanie zgody może być ryzykowne. Może to mieć wpływ na wiele innych czynników, [jeśli](../develop/publisher-verification-overview.md) zweryfikowany wydawca nie został dodany do rejestracji aplikacji. Pierwszy kod błędu i komunikat będą widoczne dla użytkowników końcowych po [wyłączeniu przepływu](configure-admin-consent-workflow.md) pracy zgody administratora. Drugi kod i komunikat będą widoczne dla użytkowników końcowych po włączeniu przepływu pracy zgody administratora oraz dla administratorów. 

Użytkownicy końcowi nie będą mogli udzielić zgody aplikacjom, które zostały wykryte jako ryzykowne. Administratorzy są w stanie, ale powinni bardzo ostrożnie oceniać aplikację i zachować ostrożność. Jeśli aplikacja wydaje się podejrzana podczas dalszej weryfikacji, można ją zgłosić firmie Microsoft na ekranie wyrażania zgody. 

## <a name="next-steps"></a>Następne kroki 

[Aplikacje, uprawnienia i zgody w Azure Active Directory (punkt końcowy w wersji 1)](../develop/quickstart-register-app.md)<br>

[Zakresy, uprawnienia i zgody w Azure Active Directory (punkt końcowy w wersji 2.0)](../develop/v2-permissions-and-consent.md)