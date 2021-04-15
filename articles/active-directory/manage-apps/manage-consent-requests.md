---
title: Zarządzanie zgodą na aplikacje i ocenianie żądań zgody w Azure Active Directory
description: Dowiedz się, jak zarządzać żądaniami zgody, gdy zgoda użytkownika jest wyłączona lub ograniczona, oraz jak ocenić żądanie zgody administratora w całej dzierżawie na aplikację w Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.openlocfilehash: 3405181f9bace023950e583dfe1a334216bf0aa0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373948"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Zarządzanie zgodą na aplikacje i ocenianie żądań zgody

Firma Microsoft [zaleca](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) wyłączenie zgody użytkownika końcowego na aplikacje. Scentralizuje to proces podejmowania decyzji z zespołem administratora ds. zabezpieczeń i tożsamości w organizacji.

Po wyłączeniu lub ograniczeniu zgody użytkownika końcowego należy wziąć pod uwagę kilka ważnych kwestii, które mają na celu zapewnienie bezpieczeństwa organizacji przy jednoczesnym umożliwieniu korzystania z aplikacji o krytycznym znaczeniu dla firmy. Te kroki mają kluczowe znaczenie dla zminimalizowania wpływu na zespół pomocy technicznej twojej organizacji i administratorów IT, a jednocześnie uniemożliwiają korzystanie z kont niezamażnych w aplikacjach innych firm.

## <a name="process-changes-and-education"></a>Przetwarzanie zmian i edukacji

 1. Rozważ włączenie [przepływu pracy zgody administratora,](configure-admin-consent-workflow.md) aby umożliwić użytkownikom żądanie zatwierdzenia przez administratora bezpośrednio z ekranu zgody.

 2. Upewnij się, że wszyscy administratorzy [](../develop/application-consent-experience.md) rozumieją platformę uprawnień i [zgody,](../develop/consent-framework.md)sposób działania monitu o wyrażenie zgody oraz sposób oceny żądania zgody [administratora dla całej dzierżawy.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Przejrzyj istniejące procesy organizacji, aby użytkownicy prosili o zatwierdzenie aplikacji przez administratora i w razie potrzeby dokonali aktualizacji. Jeśli procesy zostaną zmienione:
    * Zaktualizuj odpowiednią dokumentację, monitorowanie, automatyzację i tak dalej.
    * Przekaż zmiany procesów wszystkim użytkownikom, deweloperom, zespołom pomocy technicznej i administratorom IT.

## <a name="auditing-and-monitoring"></a>Inspekcja i monitorowanie

1. [Przejmij aplikacje i przyznaj](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) uprawnienia w organizacji, aby upewnić się, że żadne nieoprawdowane lub podejrzane aplikacje nie otrzymały wcześniej dostępu do danych.

2. Aby uzyskać dodatkowe najlepsze rozwiązania i zabezpieczenia dotyczące podejrzanych aplikacji żądających zgody OAuth, zapoznaj się z dodatkowymi najlepszymi rozwiązaniami i zabezpieczeniami w zakresie wykrywania i korygowania niedozwolonych zgód w usłudze [Office 365.](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)

3. Jeśli Twoja organizacja ma odpowiednią licencję:

    * Użyj dodatkowych [funkcji inspekcji aplikacji OAuth w Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Używanie [Azure Monitor skoroszytów do monitorowania uprawnień i działań związanych z](../reports-monitoring/howto-use-azure-monitor-workbooks.md) zgodą. Skoroszyt *Consent Insights* zawiera widok aplikacji według liczby nieudanych żądań zgody. Może to być pomocne podczas ustalania priorytetów aplikacji dla administratorów w celu sprawdzenia i podjęcia decyzji o udzieleniu im zgody administratora.

### <a name="additional-considerations-for-reducing-friction"></a>Dodatkowe zagadnienia dotyczące zmniejszania liczby apór

Aby zminimalizować wpływ na zaufane, krytyczne dla działania firmy aplikacje, które są już w użyciu, rozważ proaktywne udzielanie zgody administratora aplikacjom, które mają dużą liczbę udzielania zgody użytkownika:

1. Na podstawie dzienników logowania lub działań udzielania zgody zrób spis aplikacji już dodanych do organizacji z wysokim użyciem. Skrypt programu PowerShell [może](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) służyć do szybkiego i łatwego odnajdywania aplikacji z dużą liczbą udzielanych zgody użytkownika.

2. Oceń najważniejsze aplikacje, które nie otrzymały jeszcze zgody administratora.

   > [!IMPORTANT]
   > Dokładnie oceń aplikację przed udzieleniem zgody administratora w całej dzierżawie, nawet jeśli wielu użytkowników w organizacji wyraziło już zgodę na siebie.

3. Dla każdej zatwierdzonej aplikacji przyznaj zgodę administratora całej dzierżawy, korzystając z jednej z metod udokumentowanych poniżej.

4. W przypadku każdej zatwierdzonej aplikacji należy [rozważyć ograniczenie dostępu użytkowników.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Ocenianie żądania zgody administratora w całej dzierżawie

Udzielenie zgody administratora dla całej dzierżawy jest operacją wrażliwą.  Uprawnienia będą udzielane w imieniu całej organizacji i mogą obejmować uprawnienia do próby wykonywania operacji o wysokim poziomie uprawnień. Na przykład zarządzanie rolami, pełny dostęp do wszystkich skrzynek pocztowych lub wszystkich witryn oraz pełna personifikacja użytkownika.

Przed udzieleniem zgody administratora dla całej dzierżawy musisz upewnić się, że aplikacja i wydawca aplikacji mają zaufanie do poziomu udzielania dostępu. Jeśli nie masz pewności, kto kontroluje aplikację i dlaczego żąda uprawnień, nie udzielaj *zgody.*

Na poniższej liście przedstawiono zalecenia, które należy wziąć pod uwagę podczas oceniania żądania udzielenia zgody administratora.

* **Poznaj [platformę uprawnień i wyrażania zgody](../develop/consent-framework.md) na platformie tożsamości firmy Microsoft.**

* **Poznaj różnicę między [uprawnieniami delegowanymi i uprawnieniami aplikacji.](../develop/v2-permissions-and-consent.md#permission-types)**

   Uprawnienia aplikacji umożliwiają aplikacji dostęp do danych dla całej organizacji bez interakcji z użytkownikiem. Uprawnienia delegowane umożliwiają aplikacji działanie w imieniu użytkownika, który w pewnym momencie został zalogowany do aplikacji.

* **Poznaj żądane uprawnienia.**

   Uprawnienia żądane przez aplikację są wyświetlane w [wierszu zgody](../develop/application-consent-experience.md). Rozwinięcie tytułu uprawnienia spowoduje wyświetlenie opisu uprawnienia. Opis uprawnień aplikacji zazwyczaj kończy się na "bez zalogowano użytkownika". Opis uprawnień delegowanych zazwyczaj kończy się na "w imieniu zalogowanego użytkownika". Uprawnienia dla interfejsu MICROSOFT GRAPH API [opisano](/graph/permissions-reference) w te Microsoft Graph Permissions Reference (Dokumentacja uprawnień usługi Microsoft Graph). Zapoznaj się z dokumentacją dotyczącą innych interfejsów API, aby zrozumieć uprawnienia, które uwidaczniają.

   Jeśli nie rozumiesz żądanego uprawnienia, *nie udzielaj zgody*.

* **Dowiedz się, która aplikacja żąda uprawnień i kto opublikował aplikację.**

   Należy zwrócić uwagę na złośliwe aplikacje, które próbują wyglądać jak inne aplikacje.

   Jeśli masz wątpliwości co do wiarygodności aplikacji lub jej wydawcy, *nie udzielaj zgody*. Zamiast tego należy poszukać dodatkowego potwierdzenia (na przykład bezpośrednio od wydawcy aplikacji).

* **Upewnij się, że żądane uprawnienia są zgodne z funkcjami, których oczekujesz od aplikacji.**

   Na przykład aplikacja oferująca zarządzanie witrynami programu SharePoint może wymagać delegowanego dostępu do odczytu wszystkich kolekcji witryn, ale nie musi mieć pełnego dostępu do wszystkich skrzynek pocztowych ani pełnych uprawnień personifikacji w katalogu.

   Jeśli podejrzewasz, że aplikacja żąda większej liczby uprawnień, niż jest to *potrzebne, nie udzielaj zgody*. Aby uzyskać więcej informacji, skontaktuj się z wydawcą aplikacji.

## <a name="granting-consent-as-an-administrator"></a>Udzielanie zgody jako administrator

### <a name="granting-tenant-wide-admin-consent"></a>Udzielanie zgody administratora dla całej dzierżawy
Zobacz [Udzielanie](grant-admin-consent.md) zgody administratora całej dzierżawy aplikacji, aby uzyskać instrukcje krok po kroku dotyczące udzielania zgody administratora całej dzierżawy z witryny Azure Portal, przy użyciu programu PowerShell usługi Azure AD lub z samego monitu o wyrażenie zgody.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Udzielanie zgody w imieniu określonego użytkownika
Zamiast udzielać zgody całej organizacji, administrator może również użyć interfejsu API usługi Microsoft Graph, aby [udzielić](/graph/use-the-api) zgody na uprawnienia delegowane w imieniu pojedynczego użytkownika. Aby uzyskać więcej informacji, zobacz Get access on behalf of a user (Uzyskiwanie dostępu [w imieniu użytkownika).](/graph/auth-v2-user)

## <a name="limiting-user-access-to-applications"></a>Ograniczanie dostępu użytkowników do aplikacji
Dostęp użytkowników do aplikacji nadal może być ograniczony nawet wtedy, gdy udzielono zgody administratora całej dzierżawy. Aby uzyskać więcej informacji na temat sposobu wymagania przypisania użytkownika do aplikacji, zobacz [metody przypisywania użytkowników i grup.](./assign-user-or-group-access-portal.md)

Aby uzyskać szersze omówienie, w tym opis sposobu obsługi dodatkowych złożonych scenariuszy, zobacz using Azure AD for application access management (Używanie usługi [Azure AD do zarządzania dostępem do aplikacji).](what-is-access-management.md)

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Wyłączanie wszystkich przyszłych operacji wyrażania zgody przez użytkownika dla dowolnej aplikacji
Wyłączenie zgody użytkownika dla całego katalogu uniemożliwia użytkownikom końcowych wyrażanie zgody na dowolną aplikację. Administratorzy nadal mogą wyrazić zgodę w imieniu użytkownika. Aby dowiedzieć się więcej na temat zgody aplikacji i powodów, dla których możesz jej nie chcieć wyrazić, zapoznaj się z tematem Understanding user and admin consent (Informacje o zgody użytkownika [i administratora).](../develop/howto-convert-app-to-be-multi-tenant.md)

Aby wyłączyć wszystkie przyszłe operacje wyrażania zgody przez użytkowników w całym katalogu, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.
3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.
4.  Wybierz **pozycję Użytkownicy i grupy** w menu nawigacji.
5.  Wybierz pozycję **Ustawienia użytkownika**.
6.  Wyłącz wszystkie przyszłe operacje wyrażania zgody przez ustawienie przełącznika Użytkownicy mogą zezwalać aplikacjom na dostęp do swoich danych na nie **i** kliknij  **przycisk** Zapisz.

## <a name="next-steps"></a>Następne kroki
* [Pięć kroków zabezpieczania infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Konfigurowanie przepływu pracy wyrażania zgody przez administratora](configure-admin-consent-workflow.md)
* [Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje](configure-user-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)
