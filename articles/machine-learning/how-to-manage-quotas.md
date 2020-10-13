---
title: Zarządzanie zasobami & przydziałów
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat przydziałów zasobów dla Azure Machine Learning i zażądaj więcej przydziału.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: 4e5634633aacb56e7c7744b3afdb3aec0fe2b70f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629594"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Zarządzanie & zwiększenie limitów przydziału dla zasobów z Azure Machine Learning


Ten artykuł zawiera szczegółowe informacje na temat wstępnie skonfigurowanych limitów zasobów platformy Azure dla subskrypcji [Azure Machine Learning](overview-what-is-azure-ml.md) i przydziałów, którymi można zarządzać. Te limity są stosowane w celu uniemożliwienia budżetowania zbytów z powodu oszustw oraz do przestrzegania ograniczeń pojemności platformy Azure. 

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów skojarzonych z Azure Machine Learning. Te limity mieszczą się w zakresie od limitu liczby [obszarów roboczych](concept-workspace.md) , które są ograniczone do rzeczywistego obliczenia bazowego, który jest używany do uczenia lub wnioskowania/oceny modelu. 

Podczas projektowania i skalowania zasobów Azure Machine Learning dla obciążeń produkcyjnych należy wziąć pod uwagę te limity. Na przykład jeśli klaster nie osiągnie docelowej liczby węzłów, być może osiągnięto limit liczby rdzeni obliczeniowych Azure Machine Learning dla subskrypcji. Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, Otwórz żądanie obsługi klienta online bez dodatkowych opłat. Limitów nie można zwiększyć powyżej wartości maksymalnego limitu pokazanej w poniższych tabelach ze względu na ograniczenia pojemności platformy Azure. Jeśli nie ma żadnej kolumny maksymalnego limitu, zasób nie ma dopuszczalnych limitów.


Wraz z zarządzaniem przydziałami można także dowiedzieć się, jak [planować & zarządzanie kosztami Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Uwagi specjalne

+ Przydział jest limitem kredytowym, a nie gwarancją wydajności. Jeśli masz zapotrzebowanie na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure. Możesz również [zwiększyć limity przydziału](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

+ Przydział jest współużytkowany przez wszystkie usługi w Twoich subskrypcjach, w tym Azure Machine Learning. Jedyny wyjątek to Azure Machine Learning COMPUTE, który ma oddzielny przydział od podstawowego limitu przydziału. Należy pamiętać o obliczeniu użycia przydziału dla wszystkich usług, gdy oceniasz potrzeby związane z pojemnością.

+ Domyślne limity różnią się w zależności od typu kategorii oferty, takich jak bezpłatna wersja próbna, płatność zgodnie z rzeczywistym użyciem i seria maszyn wirtualnych, takie jak Dv2, F, G i tak dalej.

## <a name="default-resource-quotas"></a>Domyślne przydziały zasobów

Poniżej przedstawiono podział limitów przydziału według różnych typów zasobów w ramach subskrypcji platformy Azure.

> [!IMPORTANT]
> Limity mogą ulec zmianie. Najnowsza wartość znajduje się w [dokumencie](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) dotyczącym limitu przydziału na poziomie usług dla wszystkich platform Azure.

### <a name="virtual-machines"></a>Maszyny wirtualne
Dla każdej subskrypcji platformy Azure obowiązuje limit liczby maszyn wirtualnych w ramach usług lub autonomicznej. Rdzenie maszyn wirtualnych mają limit regionalny dla regionu i regionalny dla serii rozmiaru (Dv2, F itp.), które są osobno wymuszane. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. W przypadku tej subskrypcji dozwolone będzie wdrożenie 30 maszyn wirtualnych A1 lub 30 maszyn wirtualnych D1 albo kombinacji obu rodzajów maszyn wirtualnych, pod warunkiem, że liczba rdzeni nie przekroczy 30 (na przykład 10 maszyn wirtualnych A1 i 20 maszyn wirtualnych D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Środowisko obliczeniowe usługi Azure Machine Learning
W przypadku [Azure Machine Learning obliczeń](concept-compute-target.md#azure-machine-learning-compute-managed)istnieje domyślny limit przydziału dla liczby rdzeni i liczby unikatowych zasobów obliczeniowych dozwolonych na region w ramach subskrypcji. Ten limit przydziału jest oddzielony od powyżej limitu przydziału rdzeni maszyny wirtualnej, a podstawowe limity nie są współużytkowane przez dwa typy zasobów, ponieważ AmlCompute jest zarządzaną usługą, która wdraża zasoby w modelu hostowanym w imieniu.

Dostępne zasoby:
+ Dedykowane rdzenie na region mają domyślny limit 24-300 w zależności od typu oferty subskrypcji z wyższymi wartościami domyślnymi dla typów ofert EA i CSP.  Liczbę rdzeni dedykowanych na subskrypcję można zwiększyć i różnią się w zależności od rodziny maszyn wirtualnych. Niektóre wyspecjalizowane rodziny maszyn wirtualnych, takie jak NCv2, Seria NCV3 lub ND, zaczynają się domyślnie od zera. Skontaktuj się z pomocą techniczną platformy Azure, wywołując żądanie limitu przydziału w celu omówienia opcji wzrostu.

+ Rdzenie o niskim priorytecie na region mają domyślny limit 100-3000 w zależności od typu oferty subskrypcji z wyższymi wartościami domyślnymi dla typów ofert EA i CSP. Liczbę rdzeni o niskim priorytecie na subskrypcję można zwiększyć i jest to jedna wartość w różnych rodzinach maszyn wirtualnych. Skontaktuj się z pomocą techniczną platformy Azure, aby omówić opcje wzrostu.

+ Klastry na region mają domyślny limit 200. Są one współużytkowane przez klaster szkoleniowy i wystąpienie obliczeniowe (które jest traktowane jako klaster z pojedynczym węzłem w celu przekroczenia limitu przydziału). Skontaktuj się z pomocą techniczną platformy Azure, jeśli chcesz zażądać zwiększenia poza tym limitem.

+ Istnieją inne ścisłe limity, których nie można przekroczyć po trafieniu.

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| Maksymalna liczba obszarów roboczych na grupę zasobów | 800 |
| Maksymalna liczba węzłów w jednym Azure Machine Learning zasobów obliczeniowych (AmlCompute) | węzły 100 |
| Maksymalna liczba procesów MPI procesora GPU na węzeł | 1-4 |
| Maksymalna liczba procesów roboczych procesora GPU na węzeł | 1-4 |
| Maksymalny okres istnienia zadania | 21 dni<sup>1</sup> |
| Maksymalny okres istnienia zadania w węźle Low-Priority | 7 dni<sup>2</sup> |
| Maksymalna liczba serwerów parametrów na węzeł | 1 |

<sup>1</sup> maksymalny okres istnienia odnosi się do czasu uruchomienia uruchomienia i po jego zakończeniu. Ukończone przebiegi są utrwalane w nieskończoność; dane dla przebiegów nieukończonych w maksymalnym okresie istnienia są niedostępne.
<sup>2</sup> zadania w węźle Low-Priority można zastąpić w dowolnym momencie, gdy istnieje ograniczenie pojemności. Zalecamy zaimplementowanie tworzenia punktów kontrolnych w zadaniu.

### <a name="azure-machine-learning-pipelines"></a>Potoki Azure Machine Learning
W przypadku [potoków Azure Machine Learning](concept-ml-pipelines.md)obowiązuje limit przydziału liczby kroków w potoku oraz liczby uruchomień opublikowanych potoków dla regionu w ramach subskrypcji.
- Maksymalna liczba kroków dozwolona w potoku to 30 000
- Maksymalna liczba przebiegów opartych na harmonogramie i ściągania obiektów BLOB dla harmonogramów opublikowanych potoków na subskrypcję na miesiąc wynosi 100 000

### <a name="container-instances"></a>Wystąpienia kontenerów

Istnieje również ograniczenie liczby wystąpień kontenerów, które można uruchomić w danym okresie (co godzinę) lub w całej subskrypcji.
Limity można znaleźć w temacie [Container Instances limitów](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Magazyn
Istnieje limit liczby kont magazynu na region, jak również w danej subskrypcji. Domyślny limit to 250 i obejmuje konta standardowe i Premium Storage. Jeśli potrzebujesz więcej niż 250 kont magazynu w danym regionie, Przekształć żądanie przez [Pomoc techniczną platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Zespół usługi Azure Storage sprawdzi swój przypadek biznesowy i może zatwierdzić do 250 kont magazynu dla danego regionu.


## <a name="workspace-level-quota"></a>Przydział poziomu obszaru roboczego

Aby lepiej zarządzać przydziałami zasobów dla Azure Machine Learning obliczeniowych obiektów docelowych (Amlcompute) między różnymi [obszarami roboczymi](concept-workspace.md), wprowadziliśmy funkcję umożliwiającą dystrybuowanie przydziałów na poziomie subskrypcji (przez rodzinę maszyn wirtualnych) i konfigurowanie ich na poziomie obszaru roboczego. Domyślnym zachowaniem jest to, że wszystkie obszary robocze mają ten sam limit przydziału dla każdej rodziny maszyn wirtualnych. Jednak w miarę zwiększania się liczby obszarów roboczych i obciążeń o różnym priorytecie zaczynają współużytkować te same zasoby, użytkownicy chcą mieć możliwość lepszego udostępniania pojemności i uniknięcia problemów z rywalizacją o zasoby. Azure Machine Learning udostępnia rozwiązanie z zarządzaną ofertą obliczeniową przez umożliwienie użytkownikom ustawiania maksymalnego limitu przydziału dla określonej rodziny maszyn wirtualnych w każdym obszarze roboczym. Jest to analogiczne do dystrybucji pojemności między obszarami roboczymi, a użytkownicy mogą wybrać opcję przydzielenia w celu uzyskania maksymalnego użycia. 

Aby ustawić przydziały na poziomie obszaru roboczego, przejdź do dowolnego obszaru roboczego w ramach subskrypcji, a następnie kliknij pozycję **użycie + przydziały** w okienku po lewej stronie. Następnie wybierz kartę **Konfigurowanie przydziałów** , aby wyświetlić przydziały, rozwiń każdą rodzinę maszyn wirtualnych i ustawić limit przydziału dla każdego obszaru roboczego wymienionego w ramach tej rodziny maszyn wirtualnych. Należy pamiętać, że nie można ustawić wartości ujemnej ani wartości większej niż limit przydziału poziomu subskrypcji. Ponadto w miarę obserwowania wszystkie obszary robocze są przypisywane do całego przydziału subskrypcji, aby umożliwić pełne wykorzystanie przydzielonego limitu przydziału.

[![Limit przydziału poziomu obszaru roboczego Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Do ustawiania przydziału na poziomie obszaru roboczego wymagane są uprawnienia na poziomie subskrypcji. Jest to wymuszane w taki sposób, aby indywidualni właściciele obszaru roboczego nie edytowali ani nie zwiększają swoich przydziałów i nie encroaching się do zasobów ustawionych dla innego obszaru roboczego. W ten sposób administrator subskrypcji najlepiej przypisuje i dystrybuuje te przydziały w obszarach roboczych.



## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów

Przydziały obliczeń Azure Machine Learning w ramach subskrypcji są zarządzane oddzielnie od innych limitów zasobów platformy Azure. Aby wyświetlić ten limit przydziału, należy przejść do szczegółów usług Machine Learning.  

1. W okienku po lewej stronie wybierz pozycję **usługa Machine Learning** a następnie wybierz dowolny obszar roboczy z wyświetlonej listy.

2. W następnym bloku w **sekcji Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **użycie + przydziały** , aby wyświetlić bieżące limity przydziału i użycie.

3. Wybierz subskrypcję, aby wyświetlić limity przydziałów. Pamiętaj, aby odfiltrować do regionu, który Cię interesuje.

4. Teraz można przełączać się między widokiem poziomu subskrypcji a widokiem poziomu obszaru roboczego:
    + **Widok subskrypcji:** Pozwala to na wyświetlenie użycia przydziału rdzeni przez rodzinę maszyn wirtualnych, rozwinięcie go według obszaru roboczego i dalsze rozszerzenie go o rzeczywiste nazwy klastra. Ten widok jest optymalny, aby szybko zapoznać się ze szczegółami dotyczącymi użycia podstawowego dla danej rodziny maszyn wirtualnych, aby zobaczyć podziały przez obszary robocze i inne klastry dla każdego z tych obszarów roboczych. Ogólna Konwencja w tym widoku to (użycie/przydział), gdzie użycie jest bieżącą liczbą skalowalnych rdzeni, a limit przydziału to logiczna Maksymalna liczba rdzeni, do których można skalować zasób. Dla każdego **obszaru roboczego**przydział będzie przydziałem na poziomie obszaru roboczego (jak wyjaśniono powyżej), co oznacza maksymalną liczbę rdzeni, które można skalować do określonej rodziny maszyn wirtualnych. W przypadku **klastra** w podobny sposób przydziały są w rzeczywistości rdzeniami odpowiadającymi maksymalnej liczbie węzłów, które klaster może skalować do określonych przez właściwość max_nodes.
    
    + **Widok obszaru roboczego:** Pozwala to na wyświetlenie użycia zasobów podstawowych według obszaru roboczego, rozszerzenie go przez rodzinę maszyn wirtualnych i dalsze rozszerzenie go o rzeczywiste nazwy klastra. Ten widok jest optymalny, aby szybko zapoznać się ze szczegółami dotyczącymi podstawowego użycia określonego obszaru roboczego, aby zobaczyć podział przez rodziny maszyn wirtualnych i inne klastry dla każdej z tych rodzin.

Wyświetlanie przydziału dla różnych innych zasobów platformy Azure, takich jak Virtual Machines, Storage i Network, jest proste przez Azure Portal.

1. W okienku po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **subskrypcje** w kategorii Ogólne.

2. Z listy subskrypcji wybierz subskrypcję, której chcesz wyszukać.

3. Wybierz pozycję **użycie + przydziały** , aby wyświetlić bieżące limity przydziału i użycie. Użyj filtrów, aby wybrać dostawcę i lokalizacje. 

## <a name="request-quota-increases"></a>Prośba o zwiększenie limitów przydziałów

Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, [Otwórz żądanie obsługi klienta online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) bez dodatkowych opłat.

Limitów nie można zwiększyć powyżej wartości maksymalnego limitu pokazanej w tabelach. W przypadku braku maksymalnego limitu zasób nie ma dopuszczalnych limitów. [Zobacz instrukcje krok po kroku dotyczące zwiększania limitu przydziału](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

Podczas żądania zwiększenia limitu przydziału należy wybrać usługę żądaną do podniesienia przydziału, która może być usługami, takimi jak przydział usługi Machine Learning, wystąpienia kontenerów lub przydział magazynu. Oprócz Azure Machine Learning obliczeń, można kliknąć przycisk **limitu przydziału** podczas wyświetlania przydziału, wykonując czynności opisane powyżej.

> [!NOTE]
> [Bezpłatne subskrypcje wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu przydziału. Jeśli masz [bezpłatną subskrypcję wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do subskrypcji [płatnej zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem. Aby uzyskać więcej informacji, zobacz [uaktualnianie bezpłatnej wersji próbnej platformy Azure do usługi płatność zgodnie z rzeczywistym](../billing/billing-upgrade-azure-subscription.md) użyciem i  [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Wzrost przydziału prywatnego punktu końcowego i prywatnego systemu DNS

Istnieją ograniczenia dotyczące liczby prywatnych punktów końcowych i prywatnych stref DNS, które można utworzyć w ramach subskrypcji. Podczas Azure Machine Learning tworzenia zasobów w ramach subskrypcji (klienta) istnieje kilka scenariuszy, które tworzą zasoby należące do firmy Microsoft. W następujących scenariuszach może być konieczne zażądanie limitu przydziału w ramach subskrypcji firmy Microsoft:

* __Prywatny obszar roboczy z obsługą linku z kluczem zarządzanym przez klienta (CMK)__
* __Azure Container Registry obszaru roboczego za siecią wirtualną__
* __Dołączanie do obszaru roboczego klastra usługi Azure Kubernetes Service z włączonym linkiem prywatnym__.

Aby zażądać przydziału dla tych scenariuszy, wykonaj następujące czynności:

1. [Utwórz żądanie pomocy technicznej platformy Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) i wybierz następujące opcje z sekcji __podstawowe informacje__ :

    | Pole | Wybór |
    | ----- | ----- |
    | Typ problemu | Szczegóły techniczne |
    | Usługa | Moje usługi. Na liście rozwijanej wybierz pozycję __Machine Learning__ . |
    | Typ problemu | Konfiguracja obszaru roboczego, zestaw SDK i interfejs wiersza polecenia |
    | Podtyp problemu | Problem z aprowizacją obszaru roboczego lub zarządzaniem nim |

2. W sekcji __szczegóły__ Użyj pola __Opis__ , aby podać region platformy Azure, który ma być używany, i scenariusz, którego planujesz użyć. Jeśli trzeba będzie zażądać zwiększenia limitu przydziału dla wielu subskrypcji, należy również wyświetlić identyfikatory subskrypcji w tym polu.

3. Utwórz żądanie przy użyciu __Create__ .

## <a name="next-steps"></a>Następne kroki

+ [Planowanie & zarządzanie kosztami Azure Machine Learning](concept-plan-manage-cost.md)
