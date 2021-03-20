---
title: Migracja i integracja aplikacji w Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące zarządzania infrastrukturą Azure DevTest Labs w kontekście migracji i integracji aplikacji.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8d86728eefc46c74b49ac610e2207ce5e7ae6a9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289349"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Zarządzanie infrastrukturą Azure DevTest Labs — migracja i integracja aplikacji
Po ustanowieniu środowiska tworzenia i testowania laboratorium należy wziąć pod uwagę następujące pytania:

- Jak używać środowiska w zespole projektu?
- Jak upewnić się, że przestrzegasz wszelkich wymaganych zasad organizacyjnych i utrzymujesz elastyczność, aby dodać wartość do aplikacji?

## <a name="azure-marketplace-images-vs-custom-images"></a>Obrazy w portalu Azure Marketplace a obrazy niestandardowe

### <a name="question"></a>Pytanie
Kiedy należy używać obrazu portalu Azure Marketplace a własnego niestandardowego obrazu organizacyjnego?

### <a name="answer"></a>Odpowiedź
Portal Azure Marketplace powinien być używany domyślnie, chyba że istnieją konkretne problemy lub wymagania organizacyjne. Typowe przykłady to:

- Złożona konfiguracja oprogramowania, która wymaga dołączenia aplikacji jako części obrazu podstawowego.
- Instalacja i konfiguracja aplikacji może zająć wiele godzin, co nie jest wydajnym sposobem dodawania czasu obliczeń do obrazu portalu Azure Marketplace.
- Deweloperzy i testerzy wymagają szybkiego dostępu do maszyny wirtualnej i chcą zminimalizować czas instalacji nowej maszyny wirtualnej.
- Warunki zgodności lub przepisy prawne (na przykład zasady zabezpieczeń), które muszą być stosowane dla wszystkich maszyn.

Używanie obrazów niestandardowych nie powinno być uznawane za jasne. Wprowadzają one dodatkową złożoność, ponieważ teraz trzeba zarządzać plikami VHD dla tych podstawowych obrazów bazowych. Należy również regularnie uaktualniać te obrazy podstawowe przy użyciu aktualizacji oprogramowania. Aktualizacje te obejmują nowe aktualizacje systemu operacyjnego, a także wszelkie aktualizacje lub zmiany konfiguracji, które są potrzebne dla samego pakietu oprogramowania.

## <a name="formula-vs-custom-image"></a>Formuła a obraz niestandardowy

### <a name="question"></a>Pytanie
Kiedy należy używać formuły a obrazu niestandardowego?

### <a name="answer"></a>Odpowiedź
Typowym czynnikiem decydującym w tym scenariuszu jest koszt i ponowne użycie.

Jeśli masz scenariusz, w którym wielu użytkowników/laboratoriów wymagają obrazu z dużą ilością oprogramowania na obrazie podstawowym, możesz obniżyć koszty, tworząc obraz niestandardowy. Oznacza to, że obraz jest tworzony jeden raz. Skraca on czas instalacji maszyny wirtualnej i koszt ponoszony z powodu maszyny wirtualnej uruchomionej, gdy wystąpi konfiguracja.

Jednak dodatkowym czynnikiem, który należy zwrócić uwagę, jest częstotliwość wprowadzania zmian w pakiecie oprogramowania. Jeśli uruchamiasz codzienne kompilacje i chcesz, aby oprogramowanie było wymagane na maszynach wirtualnych użytkowników, rozważ użycie formuły zamiast obrazu niestandardowego.

## <a name="use-custom-organizational-images"></a>Korzystanie z niestandardowych obrazów organizacyjnych

### <a name="question"></a>Pytanie
Jak mogę skonfigurować łatwy do powtórzenia proces, aby przenieść niestandardowe obrazy organizacyjne do środowiska DevTest Labs?

### <a name="answer"></a>Odpowiedź
Obejrzyj [ten film wideo w wzorcu fabryki obrazu](./devtest-lab-faq.md#blog-post). Ten scenariusz jest zaawansowanym scenariuszem, a udostępniane skrypty są tylko przykładowymi skryptami. Jeśli wymagane są jakiekolwiek zmiany, należy zarządzać i obsługiwać skrypty używane w danym środowisku.

Za pomocą DevTest Labs do tworzenia niestandardowego potoku obrazu w Azure Pipelines:

- [Wprowadzenie: przygotowywanie maszyn wirtualnych w ciągu kilku minut przez skonfigurowanie fabryki obrazu w Azure DevTest Labs](./devtest-lab-faq.md#blog-post)
- [Fabryka obrazów — część 2! Skonfiguruj Azure Pipelines i laboratorium fabryki do tworzenia maszyn wirtualnych](./devtest-lab-faq.md#blog-post)
- [Fabryka obrazów — część 3: zapisywanie obrazów niestandardowych i dystrybucja do wielu laboratoriów](./devtest-lab-faq.md#blog-post)
- [Wideo: niestandardowa fabryka obrazów z Azure DevTest Labs](./devtest-lab-faq.md#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>Wzorce konfigurowania konfiguracji sieci

### <a name="question"></a>Pytanie
Jak mogę upewnić się, że maszyny wirtualne deweloperskie i testowe nie mogą uzyskać dostępu do publicznej sieci Internet? Czy istnieją zalecane wzorce konfigurowania konfiguracji sieci?

### <a name="answer"></a>Odpowiedź
Tak. Istnieją dwa aspekty, które należy wziąć pod uwagę — ruch przychodzący i wychodzący.

**Ruch przychodzący** — Jeśli maszyna wirtualna nie ma publicznego adresu IP, nie można jej skontaktować z Internetem. Typowym podejściem jest upewnienie się, że ustawiono zasady na poziomie subskrypcji, dzięki czemu żaden użytkownik nie może utworzyć publicznego adresu IP.

**Ruch wychodzący** — Jeśli chcesz uniemożliwić maszynom wirtualnym bezpośrednie przechodzenie do publicznej sieci Internet i wymusić ruch przez zaporę firmową, możesz kierować ruchem lokalnym za pośrednictwem usługi Express Route lub VPN przy użyciu wymuszonego routingu.

> [!NOTE]
> Jeśli masz serwer proxy, który blokuje ruch bez ustawień serwera proxy, nie zapomnij dodać wyjątków do konta magazynu artefaktów laboratorium.

W przypadku maszyn wirtualnych lub podsieci można także użyć grup zabezpieczeń sieci. Ten krok powoduje dodanie dodatkowej warstwy ochrony w celu zezwalania na ruch i blokowania go.

## <a name="new-vs-existing-virtual-network"></a>Nowa a istniejąca sieć wirtualna

### <a name="question"></a>Pytanie
Kiedy należy utworzyć nową sieć wirtualną dla środowiska DevTest Labs a przy użyciu istniejącej sieci wirtualnej?

### <a name="answer"></a>Odpowiedź
Jeśli maszyny wirtualne muszą współdziałać z istniejącą infrastrukturą, należy rozważyć użycie istniejącej sieci wirtualnej w środowisku DevTest Labs. Ponadto, jeśli używasz ExpressRoute, możesz zminimalizować ilość sieci wirtualnych/podsieci, aby nie dzielić przestrzeni adresowej IP, która jest przypisana do użycia w subskrypcjach. Należy również rozważyć użycie wzorca wirtualnej sieci równorzędnej (model gwiazdy). Takie podejście umożliwia komunikację między sieciami wirtualnymi i między subskrypcjami w danym regionie, ale Komunikacja równorzędna między regionami to wbudowana funkcja sieci platformy Azure.

W przeciwnym razie każde środowisko DevTest Labs może mieć własną sieć wirtualną. Należy jednak pamiętać, że liczba sieci wirtualnych na subskrypcję jest [ograniczona](../azure-resource-manager/management/azure-subscription-service-limits.md) . Wartość domyślna to 50, chociaż ten limit można podwyższyć do 100.

## <a name="shared-public-or-private-ip"></a>Udostępniony, publiczny lub prywatny adres IP

### <a name="question"></a>Pytanie
Kiedy należy używać udostępnionego adresu IP a publicznego adresu IP a prywatny adres IP?

### <a name="answer"></a>Odpowiedź
W przypadku korzystania z sieci VPN typu lokacja-lokacja lub usługi Express Route należy rozważyć użycie prywatnych adresów IP, aby umożliwić dostęp do tych maszyn za pośrednictwem sieci wewnętrznej i uzyskać dostęp za pośrednictwem publicznego Internetu.

> [!NOTE]
> Właściciele laboratorium mogą zmienić te zasady podsieci, aby upewnić się, że nikt nie tworzy publicznych adresów IP dla swoich maszyn wirtualnych. Właściciel subskrypcji powinien utworzyć zasady subskrypcji uniemożliwiające tworzenie publicznych adresów IP.

W przypadku korzystania ze współużytkowanych publicznych adresów IP maszyny wirtualne w laboratorium współużytkują publiczny adres. Takie podejście może być przydatne, gdy konieczne jest uniknięcie naruszenia limitów publicznych adresów IP dla danej subskrypcji.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limity liczby maszyn wirtualnych na użytkownika lub laboratorium

### <a name="question"></a>Pytanie
Czy istnieje reguła pod względem liczby maszyn wirtualnych, które należy ustawić na użytkownika lub na laboratorium?

### <a name="answer"></a>Odpowiedź
Biorąc pod uwagę liczbę maszyn wirtualnych na użytkownika lub na laboratorium, istnieją trzy główne zagadnienia:

- **Całkowity koszt** , jaki zespół może spędzać na zasobach w laboratorium. Można łatwo przydziałać wiele maszyn. Aby kontrolować koszty, jeden mechanizm polega na ograniczeniu liczby maszyn wirtualnych na użytkownika i/lub na laboratorium
- Łączna liczba maszyn wirtualnych w laboratorium ma wpływ na dostępne [przydziały poziomu subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md) . Jeden z górnych limitów to 800 grup zasobów na subskrypcję. DevTest Labs obecnie tworzy nową grupę zasobów dla każdej maszyny wirtualnej (o ile nie są używane udostępnione publiczne adresy IP). Jeśli w ramach subskrypcji jest 10 laboratoriów, laboratoria mogą dopasować około 79 maszyn wirtualnych w każdym laboratorium (800 górny limit — 10 grup zasobów dla samych 10 laboratoriów) = 79 maszyny wirtualne na laboratorium.
- Jeśli laboratorium jest połączone z siecią lokalną za pośrednictwem usługi Express Route (na przykład), **dostępne są zdefiniowane przestrzenie adresów IP** dla sieci wirtualnej/podsieci. Aby upewnić się, że maszyny wirtualne w laboratorium nie mogą zostać utworzone (błąd: nie można pobrać adresu IP), właściciele laboratorium mogą określić maksymalną liczbę maszyn wirtualnych na laboratorium z opcją dostępna przestrzeń adresów IP.

## <a name="use-resource-manager-templates"></a>Używanie szablonów usługi Resource Manager

### <a name="question"></a>Pytanie
Jak mogę używać szablonów Menedżer zasobów w środowisku DevTest Labs?

### <a name="answer"></a>Odpowiedź
Szablony Menedżer zasobów można wdrożyć w środowisku DevTest Labs, wykonując kroki opisane w [funkcji środowiska w artykule DevTest Labs](devtest-lab-test-env.md) . Zasadniczo możesz sprawdzić szablony Menedżer zasobów w repozytorium git (Azure Repos lub GitHub) i dodać [prywatne repozytorium dla szablonów](devtest-lab-test-env.md) do laboratorium.

Ten scenariusz może nie być przydatny, jeśli używasz laboratoriów DevTest Labs do hostowania maszyn deweloperskich, ale mogą być przydatne, jeśli tworzysz środowisko tymczasowe, które jest reprezentatywne dla produkcji.

Warto również zauważyć, że liczba maszyn wirtualnych na laboratorium lub dla poszczególnych użytkowników ogranicza liczbę maszyn utworzonych w sposób macierzysty w laboratorium, a nie w środowiskach (Menedżer zasobów szablonów).

## <a name="next-steps"></a>Następne kroki
Zobacz [Używanie środowisk w DevTest Labs](devtest-lab-test-env.md).
