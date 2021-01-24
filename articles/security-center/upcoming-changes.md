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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 1b034c0f1c62eecf8139ed908a5a242060f3e886
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746564"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

- [Zalecenia dotyczące ochrony obciążeń Kubernetes są wkrótce udostępniane w celu uzyskania ogólnej dostępności (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Ulepszenia zalecenia klasyfikacji danych SQL](#enhancements-to-sql-data-classification-recommendation)
- [35 zaleceń dotyczących wersji zapoznawczej dodano w celu zwiększenia zakresu testów zabezpieczeń platformy Azure](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Zalecenia dotyczące ochrony obciążeń Kubernetes są wkrótce udostępniane w celu uzyskania ogólnej dostępności (GA)

**Szacowana data zmiany:** Styczeń 2021

Zalecenia dotyczące ochrony obciążeń Kubernetes opisane w artykule [Ochrona obciążeń Kubernetes](kubernetes-workload-protections.md) są obecnie dostępne w wersji zapoznawczej. Mimo że zalecenie jest w wersji zapoznawczej, nie renderuje zasobów w złej kondycji i nie jest uwzględniane w obliczeniach bezpiecznego wyniku.

Te rekomendacje wkrótce zostaną wydane w celu uzyskania ogólnej dostępności, a więc *zostaną* uwzględnione w obliczaniu wyniku. Jeśli jeszcze ich nie skorygowano, może to spowodować niewielki wpływ na bezpieczny wynik.

Skoryguj je wszędzie tam, gdzie to możliwe (Dowiedz się, w jaki sposób [skorygować zalecenia w Azure Security Center](security-center-remediate-recommendations.md)).

Zalecenia dotyczące ochrony obciążeń Kubernetes są następujące:

- Dodatek Azure Policy dla Kubernetes powinien być zainstalowany i włączony w klastrach
- Należy wymusić limity procesora CPU i pamięci kontenera
- Należy unikać używania kontenerów uprzywilejowanych
- Niezmienny (tylko do odczytu) główny system plików powinien zostać wymuszony dla kontenerów
- Należy unikać kontenera z eskalacją uprawnień
- Należy unikać uruchamiania kontenerów jako użytkownik główny
- Należy unikać obsługi kontenerów chronionych przestrzeni nazw hosta
- W przypadku kontenerów powinny być wymuszane najmniej uprzywilejowane funkcje systemu Linux.
- Użycie instalacji woluminów HostPath na początku powinno być ograniczone do znanej listy
- Kontenery powinny nasłuchiwać tylko na dozwolonych portach
- Usługi powinny nasłuchiwać tylko na dozwolonych portach
- Użycie sieci i portów hosta powinno być ograniczone
- Zastępowanie lub wyłączanie profilowania AppArmor profil powinien być ograniczony
- Obrazy kontenerów powinny być wdrażane tylko z zaufanych rejestrów             

Dowiedz się więcej na temat tych zaleceń w obszarze [Ochrona obciążeń Kubernetes](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe 

**Szacowana data zmiany:** Luty 2021

Następujące dwa zalecenia zaplanowano jako przestarzałe w lutym 2021:

- **Aby zastosować aktualizacje systemu, należy ponownie uruchomić maszyny**. Może to skutkować niewielkim wpływem na bezpieczny wynik.
- **Na maszynach należy zainstalować agenta monitorowania**. To zalecenie odnosi się tylko do maszyn lokalnych, a część jej logiki zostanie przetransferowana do innego zalecenia, **log Analytics na maszynach należy rozwiązać problemy z kondycją agenta**. Może to skutkować niewielkim wpływem na bezpieczny wynik.

Zalecamy sprawdzenie konfiguracji eksportu ciągłego i automatyzacji przepływu pracy, aby sprawdzić, czy te zalecenia są zawarte w nich. Ponadto należy odpowiednio zaktualizować wszystkie pulpity nawigacyjne lub inne narzędzia do monitorowania, które mogą z nich korzystać.

Więcej informacji na temat tych zaleceń znajduje się na [stronie informacje o zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Ulepszenia zalecenia klasyfikacji danych SQL

**Szacowana data zmiany:** Q2 2021

Aktualna wersja zaleceń **poufnych danych w bazach danych SQL powinna być sklasyfikowana** w obszarze **Zastosuj klasyfikację danych** , która zostanie wycofana i zastąpiona nową wersją, która jest lepiej wyrównana z strategią klasyfikacji danych firmy Microsoft. W efekcie:

- Zalecenie nie będzie już miało wpływu na swój Bezpieczny wynik
- Kontrola zabezpieczeń ("Zastosuj klasyfikację danych") nie będzie już miała wpływu na swój Bezpieczny wynik
- Identyfikator zalecenia również zostanie zmieniony (obecnie b0df6f56-862d-4730-8597-38c0fd4ebd59)


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