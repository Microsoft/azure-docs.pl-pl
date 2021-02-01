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
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: 83c1d8ac316703d9d22d0716c86c5731c3db7133
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226475"
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

Bieżąca wersja zaleceń **poufnych danych w bazach danych SQL powinna być sklasyfikowana** w obszarze **Zastosuj klasyfikację zabezpieczeń danych** , która będzie lepiej wyrównana z strategią klasyfikacji danych firmy Microsoft. W efekcie:

- Zalecenie nie będzie już miało wpływu na swój Bezpieczny wynik
- Kontrola zabezpieczeń ("Zastosuj klasyfikację danych") nie będzie już miała wpływu na swój Bezpieczny wynik
- Identyfikator zalecenia również zostanie zmieniony (obecnie b0df6f56-862d-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z najnowszymi zmianami w produkcie, zobacz [co nowego w programie Azure Security Center?](release-notes.md).
