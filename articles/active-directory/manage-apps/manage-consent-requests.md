---
title: Zarządzanie zgodą na aplikacje i ocenianie żądań zgody w Azure Active Directory
description: Dowiedz się, jak zarządzać żądaniami zgody, gdy wyrażasz zgodę użytkownika na wyłączenie lub ograniczenie, oraz jak oszacować żądanie administratora dla całej dzierżawy dla aplikacji w Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: c41543cdfff4e5ffaad614f6cb7a539d78a0bdae
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105565"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Zarządzanie zgodą na aplikacje i ocenianie żądań zgody

Firma Microsoft [zaleca](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) wyłączenie zgody użytkowników końcowych na aplikacje. Dzięki temu proces podejmowania decyzji jest scentralizowany przez zespół administratorów ds. zabezpieczeń i tożsamości.

Po wyłączeniu lub ograniczeniu zgody użytkownika końcowego należy pamiętać o kilku istotnych kwestiach, aby zapewnić bezpieczeństwo organizacji przy jednoczesnym wykorzystaniu aplikacji o krytycznym znaczeniu dla firmy. Te kroki są kluczowe, aby zminimalizować wpływ na zespół pomocy technicznej i administratorów IT w organizacji, jednocześnie uniemożliwiając korzystanie z niezarządzanych kont w aplikacjach innych firm.

## <a name="process-changes-and-education"></a>Przetwarzaj zmiany i edukacja

 1. Rozważ włączenie [przepływu pracy zgody administratora](configure-admin-consent-workflow.md) , aby umożliwić użytkownikom żądanie zatwierdzenia przez administratora bezpośrednio z poziomu ekranu wyrażania zgody.

 2. Upewnij się, że wszyscy administratorzy znają [uprawnienia i strukturę wyrażania zgody](../develop/consent-framework.md), jak działa [monit o zgodę](../develop/application-consent-experience.md) oraz jak [oszacować żądanie dotyczące zgody administratora dzierżawy](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Przejrzyj istniejące procesy w organizacji, aby umożliwić użytkownikom zażądanie zatwierdzenia przez administratora aplikacji, a jeśli to konieczne, wprowadź aktualizacje. Jeśli procesy zostały zmienione:
    * Aktualizowanie odpowiedniej dokumentacji, monitorowania, automatyzacji i tak dalej.
    * Przekazywać zmiany procesu wszystkim odpowiednim użytkownikom, deweloperom, zespołom pomocy technicznej i administratorom IT.

## <a name="auditing-and-monitoring"></a>Inspekcja i monitorowanie

1. [Przeprowadź inspekcję aplikacji i Udziel uprawnień](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) w organizacji, aby upewnić się, że żadne nieuzasadnione lub podejrzane aplikacje nie uzyskały wcześniej dostępu do danych.

2. Zapoznaj się z informacjami dotyczącymi [wykrywania i korygowania nielegalnych dotacji w pakiecie Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) w celu uzyskania dodatkowych najlepszych rozwiązań i zabezpieczenia przed podejrzanymi aplikacjami żądającymi zgody OAuth.

3. Jeśli Twoja organizacja ma odpowiednią licencję:

    * Skorzystaj [z dodatkowych funkcji inspekcji aplikacji OAuth w Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Użyj [skoroszytów Azure monitor do monitorowania uprawnień i](../reports-monitoring/howto-use-azure-monitor-workbooks.md) działań związanych z zgodą. Skoroszyt *Informacje o zgodzie* zawiera widok aplikacji według liczby nieudanych żądań zgody. Może to być przydatne w przypadku określania priorytetów aplikacji dla administratorów do przeglądania i decydowania, czy należy przyznać im zgodę administratora.

### <a name="additional-considerations-for-reducing-friction"></a>Dodatkowe zagadnienia dotyczące zmniejszenia liczby problemów

Aby zminimalizować wpływ na zaufane aplikacje o krytycznym znaczeniu dla firmy, które są już używane, weź pod uwagę proaktywnie udzielanie zgody administratora na aplikacje, które mają dużą liczbę przyznanych zgody użytkownika:

1. Utwórz spis aplikacji, które zostały już dodane do organizacji i o wysokim poziomie użycia, na podstawie dzienników logowania lub działań związanych z udzieleniem zgody. [Skrypt](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) programu PowerShell może służyć do szybkiego i łatwego odnajdywania aplikacji z dużą liczbą przyznanych zgody użytkownika.

2. Oceń najważniejsze aplikacje, które nie udzieliły jeszcze zgody administratora.

   > [!IMPORTANT]
   > Należy dokładnie oszacować aplikację przed udzieleniem zgody administratora całej dzierżawy, nawet jeśli wielu użytkowników w organizacji już wyraziło zgodę na siebie.

3. Dla każdej zatwierdzonej aplikacji Udziel zgody administratora na całej dzierżawy przy użyciu jednej z metod opisanych poniżej.

4. Dla każdej zatwierdzonej aplikacji należy rozważyć [ograniczenie dostępu użytkowników](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Ocenianie żądania zgody administratora całej dzierżawy

Udzielanie zgody administratora całej dzierżawy jest operacją poufną.  Uprawnienia będą przyznawane w imieniu całej organizacji i mogą obejmować uprawnienia do podejmowania operacji o wysokim poziomie uprawnień. Na przykład zarządzanie rolami, pełny dostęp do wszystkich skrzynek pocztowych lub wszystkich witryn oraz pełna personifikacja użytkownika.

Przed przyznaniem zgody administratora całej dzierżawy należy zadbać o to, aby ufać aplikacji i wydawcy aplikacji, na poziomie przyznawanego dostępu. Jeśli nie masz pewności, kto kontroluje aplikację i Dlaczego aplikacja żąda uprawnień, nie *udzielaj zgody*.

Poniższa lista zawiera pewne zalecenia, które należy wziąć pod uwagę podczas oceny wniosku o przyznanie zgody administratora.

* **Zapoznaj się z [uprawnieniami i strukturą wyrażania zgody](../develop/consent-framework.md) na platformie tożsamości firmy Microsoft.**

* **Zapoznaj się z różnicą między [delegowanymi uprawnieniami i uprawnieniami aplikacji](../develop/v2-permissions-and-consent.md#permission-types).**

   Uprawnienia aplikacji umożliwiają aplikacji dostęp do danych w całej organizacji bez żadnej interakcji z użytkownikiem. Delegowane uprawnienia pozwalają aplikacji działać w imieniu użytkownika, który w pewnym momencie został zalogowany do aplikacji.

* **Zapoznaj się z żądanymi uprawnieniami.**

   Uprawnienia wymagane przez aplikację są wymienione w [monicie o zgodę](../develop/application-consent-experience.md). Powiększanie tytułu uprawnienia spowoduje wyświetlenie opisu uprawnienia. Opis uprawnień aplikacji zwykle kończy się na "bez zalogowanego użytkownika". Opis delegowanych uprawnień zwykle kończy się na "w imieniu zalogowanego użytkownika". Uprawnienia do interfejsu API Microsoft Graph są opisane w dokumentacji [uprawnień Microsoft Graph](/graph/permissions-reference) — zapoznaj się z dokumentacją dotyczącą innych interfejsów API, aby zrozumieć, jakie uprawnienia są ujawniane.

   Jeśli nie rozumiesz żądanego uprawnienia, nie *udzielaj zgody*.

* **Informacje o tym, które aplikacje żądają uprawnień i kto opublikował aplikację.**

   Uważaj na złośliwe aplikacje, które próbują wyglądać podobnie do innych aplikacji.

   Jeśli nie masz wątpliwości autentyczności aplikacji lub jej wydawcy, nie *udzielaj zgody*. Zamiast tego należy zwrócić dodatkowe potwierdzenie (na przykład bezpośrednio od wydawcy aplikacji).

* **Upewnij się, że żądane uprawnienia są wyrównane z funkcjami, których oczekujesz od aplikacji.**

   Na przykład aplikacja oferująca zarządzanie witryną programu SharePoint może wymagać delegowanego dostępu do odczytu wszystkich kolekcji witryn, ale niekoniecznie musi mieć pełny dostęp do wszystkich skrzynek pocztowych lub pełne uprawnienia personifikacji w katalogu.

   Jeśli podejrzewasz, że aplikacja żąda więcej uprawnień niż jest to potrzebne, nie *udzielaj zgody*. Aby uzyskać więcej informacji, skontaktuj się z wydawcą aplikacji.

## <a name="granting-consent-as-an-administrator"></a>Udzielanie zgody jako administrator

### <a name="granting-tenant-wide-admin-consent"></a>Udzielanie zgody administratora całej dzierżawy
Aby uzyskać instrukcje krok po kroku dotyczące udzielania zgody administratora na poziomie dzierżawy z Azure Portal przy użyciu programu Azure AD PowerShell lub z poziomu monitu o zgodę, zobacz [przyznawanie zgody administratora w całej dzierżawie](grant-admin-consent.md) .

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Udzielanie zgody w imieniu określonego użytkownika
Zamiast udzielania zgody na całą organizację, administrator może także użyć [interfejsu API Microsoft Graph](/graph/use-the-api) , aby przyznać zgodę na delegowane uprawnienia w imieniu pojedynczego użytkownika. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu w imieniu użytkownika](/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Ograniczanie dostępu użytkowników do aplikacji
Dostęp użytkowników do aplikacji można nadal ograniczyć nawet w przypadku udzielenia zgody administratora całej dzierżawy. Aby uzyskać więcej informacji na temat wymagania przypisania użytkownika do aplikacji, zobacz [metody przypisywania użytkowników i grup](./assign-user-or-group-access-portal.md).

Aby uzyskać więcej szerszego omówienia, w tym sposób obsługi dodatkowych złożonych scenariuszy, zobacz [Korzystanie z usługi Azure AD do zarządzania dostępem do aplikacji](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Wyłącz wszystkie przyszłe operacje wyrażania zgody użytkownika na dowolną aplikację
Wyłączenie zgody użytkownika na cały katalog uniemożliwi użytkownikom końcowym przesyłanie ich do żadnej aplikacji. Administratorzy mogą w dalszym ciągu wyrazić zgodę w imieniu użytkownika. Aby dowiedzieć się więcej o zgodzie aplikacji oraz o tym, dlaczego warto lub nie chcieć wyrazić zgodę, przeczytaj artykuł [Informacje o zgodzie użytkownika i administratora](../develop/howto-convert-app-to-be-multi-tenant.md).

Aby wyłączyć wszystkie przyszłe operacje wyrażania zgody użytkowników w całym katalogu, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Ustawienia użytkownika**.
6.  Wyłącz wszystkie przyszłe operacje wyrażania zgody użytkownika przez ustawienie opcji **Użytkownicy mogą zezwalać aplikacjom na dostęp do ich** przełączników danych **, a następnie** kliknij przycisk **Zapisz** .

## <a name="next-steps"></a>Następne kroki
* [Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
* [Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje](configure-user-consent.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)
