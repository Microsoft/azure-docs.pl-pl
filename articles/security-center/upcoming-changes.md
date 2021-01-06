---
title: Ważne zmiany dotyczące Azure Security Center
description: Nadchodzące zmiany w Azure Security Center, które mogą być potrzebne, i dla których może być konieczne zaplanowanie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: memildin
ms.openlocfilehash: 0656000a1d6449306e8afe538f846c55c79a31a2
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915290"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

- [Zasoby "nie dotyczy", które mają być zgłaszane jako "zgodne" w ocenach Azure Policy](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [35 zaleceń dotyczących wersji zapoznawczej dodano w celu zwiększenia zakresu testów zabezpieczeń platformy Azure](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>Zasoby "nie dotyczy", które mają być zgłaszane jako "zgodne" w ocenach Azure Policy

**Szacowana data zmiany:** Styczeń 2021

Obecnie zasoby, które są oceniane pod kątem rekomendacji i nie są **stosowane** , są wyświetlane w Azure Policy jako "niezgodne". Żadna akcja użytkownika nie może zmienić stanu na "zgodny". Od tej planowanej zmiany zostaną one zgłoszone jako "zgodne" w celu zwiększenia przejrzystości.

Jedyny wpływ będzie widoczny w Azure Policy, w którym zostanie zwiększona liczba zgodnych zasobów. Nie będzie to miało wpływu na Twój bezpieczny wynik w Azure Security Center.

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>35 zaleceń dotyczących wersji zapoznawczej w celu zwiększenia zakresu testów zabezpieczeń platformy Azure

**Szacowana data zmiany:** Styczeń 2021

Usługa Azure Security test to zestaw wytycznych dotyczących zabezpieczeń i zgodności opartych na platformie Azure, które są stosowane do najlepszych rozwiązań w zakresie bezpieczeństwa i zapewniających zgodność. [Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](../security/benchmarks/introduction.md).

Poniższe zalecenia dotyczące wersji zapoznawczej 35 zostaną dodane do Security Center, aby zwiększyć pokrycie tego testu porównawczego.

Zalecenia dotyczące wersji zapoznawczej nie powodują złej kondycji zasobu i nie są uwzględniane w obliczeniach bezpiecznego wyniku. Skoryguj je wszędzie tam, gdzie to możliwe, aby po zakończeniu okresu korzystania z wersji zapoznawczej doczyniły się do oceny. Dowiedz się więcej o tym, jak odpowiedzieć na te zalecenia w temacie [Koryguj zalecenia w Azure Security Center](security-center-remediate-recommendations.md).

| Kontrola zabezpieczeń                     | Nowe rekomendacje                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włącz szyfrowanie w spoczynku            | — Konta Azure Cosmos DB powinny używać kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w czasie spoczynku<br>-Azure Machine Learning obszary robocze powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)<br>— Należy włączyć ochronę danych klucza dla serwerów MySQL<br>— Należy włączyć ochronę danych klucza dla serwerów PostgreSQL<br>-Cognitive Services konta powinny włączać szyfrowanie danych za pomocą klucza zarządzanego przez klienta (CMK)<br>-Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)<br>-Wystąpienia zarządzane przez program SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w spoczynku<br>— Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych<br>— Konta magazynu powinny używać klucza zarządzanego przez klienta (CMK) do szyfrowania                                                                                                                                                              |
| Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń    | -Subskrypcje powinny mieć adres e-mail kontaktu pod kątem problemów z zabezpieczeniami<br> — Funkcja autoaprowizacji agenta Log Analytics powinna być włączona w ramach subskrypcji<br> -Należy włączyć powiadomienia e-mail o alertach o wysokiej ważności<br> -Wyślij powiadomienie e-mail do właściciela subskrypcji w celu uzyskania alertów o wysokiej ważności.<br> -Magazyny kluczy powinny mieć włączoną ochronę przed przeczyszczeniem<br> -Magazyny kluczy powinny mieć włączone usuwanie nietrwałe |
| Zarządzanie dostępem i uprawnieniami        | -Aplikacje funkcji powinny mieć włączone "certyfikaty klienta (przychodzące certyfikaty klienta)" |
| Ochrona aplikacji przed atakami DDoS | — Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway<br> -Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure Front drzwiczk Service |
| Ogranicz nieautoryzowany dostęp do sieci | -Zapora powinna być włączona na Key Vault<br> -Prywatny punkt końcowy powinien być skonfigurowany dla Key Vault<br> -Konfiguracja aplikacji powinna używać prywatnego linku<br> -Pamięć podręczna platformy Azure dla Redis powinna znajdować się w sieci wirtualnej<br> -Azure Event Grid domeny powinny używać prywatnego linku<br> -Azure Event Grid tematy powinny używać prywatnego linku<br> -Azure Machine Learning obszary robocze powinny używać prywatnego linku<br> — Usługa Azure sygnalizująca powinna używać prywatnego linku<br> — Chmura Wiosenna platformy Azure powinna używać iniekcji sieci<br> -Rejestr kontenerów nie powinien zezwalać na nieograniczony dostęp do sieci<br> -Rejestry kontenerów powinny używać prywatnego linku<br> -Publiczny dostęp do sieci powinien być wyłączony dla serwerów MariaDB<br> -Publiczny dostęp do sieci powinien być wyłączony dla serwerów MySQL<br> -Publiczny dostęp do sieci powinien być wyłączony dla serwerów PostgreSQL<br> — Konto magazynu powinno korzystać z prywatnego połączenia z linkiem<br> — Konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej<br> — Szablony konstruktora obrazów maszyn wirtualnych powinny używać prywatnego linku|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Powiązane linki:

- [Dowiedz się więcej o teście zabezpieczeń Azure](../security/benchmarks/introduction.md)
- [Dowiedz się więcej o Azure Database for MariaDB](../mariadb/overview.md)
- [Dowiedz się więcej o Azure Database for MySQL](../mysql/overview.md)
- [Dowiedz się więcej o Azure Database for PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z najnowszymi zmianami w produkcie, zobacz [co nowego w programie Azure Security Center?](release-notes.md).