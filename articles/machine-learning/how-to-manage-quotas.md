---
title: Zarządzanie zasobami i przydziałami
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat przydziałów i limitów zasobów dla Azure Machine Learning i sposobu żądania zwiększenia limitu przydziału.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 58dacc9e65da6502d083446cb7202c222cb7e795
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034041"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Zarządzanie przydziałami i zwiększanie limitów zasobów przy użyciu Azure Machine Learning

Na platformie Azure są stosowane limity i przydziały, które uniemożliwiają przekroczenie budżetu z powodu oszustw i przestrzeganie ograniczeń pojemności platformy Azure. Te limity należy wziąć pod uwagę podczas skalowania obciążeń produkcyjnych. W tym artykule omówiono następujące informacje:

> [!div class="checklist"]
> + Domyślne limity zasobów platformy Azure powiązane z [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Tworzenie przydziałów na poziomie obszaru roboczego.
> + Wyświetlanie przydziałów i limitów.
> + Zwiększenie limitu przydziału.
> + Prywatne przydziały punktów końcowych i DNS.

Wraz z zarządzaniem przydziałami można dowiedzieć się, jak [planować i zarządzać kosztami Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Uwagi specjalne

+ Przydział jest limitem kredytowym, a nie gwarancją wydajności. Jeśli masz zapotrzebowanie na dużą skalę, [skontaktuj się z pomocą techniczną platformy Azure w celu zwiększenia limitu przydziału](#request-quota-increases).

+ Przydział jest udostępniany wszystkim usługom w ramach subskrypcji, w tym Azure Machine Learning. Oblicz użycie dla wszystkich usług, gdy oceniasz pojemność.
 
  Azure Machine Learning COMPUTE to wyjątek. Ma oddzielny przydział od podstawowego limitu przydziału. 

+ Domyślne limity różnią się w zależności od typu kategorii oferty, takich jak bezpłatna wersja próbna, płatność zgodnie z rzeczywistym użyciem i seria maszyn wirtualnych (na przykład Dv2, F i G).

## <a name="default-resource-quotas"></a>Domyślne przydziały zasobów

Ta sekcja zawiera informacje o domyślnych i maksymalnych limitach przydziału dla następujących zasobów:

+ Zasoby Azure Machine Learning
  + Azure Machine Learning obliczeń
  + Potoki Azure Machine Learning
+ Maszyny wirtualne
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> Limity mogą ulec zmianie. Aby uzyskać najnowsze informacje, zobacz [limity dotyczące subskrypcji i usług platformy Azure, przydziałów i ograniczeń](../azure-resource-manager/management/azure-subscription-service-limits.md) dla wszystkich platform Azure.

### <a name="azure-machine-learning-assets"></a>Zasoby Azure Machine Learning
Poniższe ograniczenia dotyczące zasobów dotyczą poszczególnych obszarów roboczych. 

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| Zestawy danych | 10 mln |
| Uruchamianie | 10 mln |
| Modele | 10 mln|
| Artifacts | 10 mln |

Ponadto maksymalny **czas działania** to 30 dni, a maksymalna liczba **rejestrowanych metryk na uruchomienie** to 1 000 000.

### <a name="azure-machine-learning-compute"></a>Środowisko obliczeniowe usługi Azure Machine Learning
[Azure Machine Learning COMPUTE](concept-compute-target.md#azure-machine-learning-compute-managed) ma domyślny limit przydziału dla liczby rdzeni (podzielony przez każdą rodzinę maszyn wirtualnych i łączne całkowite rdzenie), a także liczbę unikatowych zasobów obliczeniowych dozwolonych na region w ramach subskrypcji. Ten limit przydziału jest oddzielony od limitu przydziału rdzeni maszyny wirtualnej wymienionego w poprzedniej sekcji, gdy ma zastosowanie tylko do zarządzanych zasobów obliczeniowych Azure Machine Learning.

[Poproś o zwiększenie limitu przydziału](#request-quota-increases) , aby podnieść limity dla różnych przydziałów głównych rodziny maszyn wirtualnych, Łączna liczba przydziałów i zasobów podstawowych subskrypcji w tej sekcji.

Dostępne zasoby:
+ Liczba **rdzeni dedykowanych na region** ma domyślny limit od 24 do 300, w zależności od typu oferty subskrypcji. Dla każdej rodziny maszyn wirtualnych można zwiększyć liczbę dedykowanych rdzeni na subskrypcję. Wyspecjalizowane rodziny maszyn wirtualnych, takie jak NCv2, Seria NCV3 lub ND, zaczynają się domyślnie od zera.

+ **Rdzenie o niskim priorytecie na region** mają domyślny limit 100 do 3 000, w zależności od typu oferty subskrypcji. Liczbę rdzeni o niskim priorytecie na subskrypcję można zwiększyć i jest to jedna wartość w różnych rodzinach maszyn wirtualnych.

+ **Klastry na region** mają domyślny limit 200. Są one współużytkowane przez klaster szkoleniowy i wystąpienie obliczeniowe. (Wystąpienie obliczeniowe jest traktowane jako klaster z jednym węzłem na potrzeby przydziałów).

> [!TIP]
> Aby dowiedzieć się więcej o tym, której rodziny maszyn wirtualnych należy zażądać zwiększenia limitu przydziału, wypróbuj [rozmiary maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/sizes). Na przykład rodziny maszyn wirtualnych GPU zaczynają się od "N" w nazwie rodziny (np. Seria Seria NCV3)

W poniższej tabeli przedstawiono dodatkowe limity na platformie. Skontaktuj się z zespołem produktu Azure, **korzystając z biletu pomocy technicznej** , aby zażądać wyjątku.

| **Zasób lub Akcja** | **Limit maksymalny** |
| --- | --- |
| Obszary robocze na grupę zasobów | 800 |
| Węzły w jednej konfiguracji **klastra** Azure Machine Learning COMPUTE (AmlCompute) jako Pula bez obsługi komunikacji (tj. nie można uruchomić zadań MPI) | 100 węzłów, ale można skonfigurować do 65000 węzłów |
| Węzły w pojedynczym kroku przebiegu równoległego są **uruchamiane** w klastrze Azure Machine Learning COMPUTE (AmlCompute) | 100 węzłów, ale można skonfigurować do 65000 węzłów, Jeśli klaster jest skonfigurowany do skalowania na wyższy |
| Węzły w ramach jednej konfiguracji **klastra** Azure Machine Learning COMPUTE (AmlCompute) jako puli z włączoną obsługą komunikacji | 300 węzłów, ale można skonfigurować do 4000 węzłów |
| Węzły w jednej konfiguracji **klastra** Azure Machine Learning COMPUTE (AmlCompute) jako Pula z włączoną obsługą komunikacji w rodzinie maszyn wirtualnych z WŁĄCZONĄ funkcją RDMA | węzły 100 |
| Węzły w jednym MPI **działają** Azure Machine Learning w klastrze obliczeniowym (AmlCompute) | węzły 100, ale można je zwiększyć do 300 węzłów |
| Procesy MPI procesora GPU na węzeł | 1-4 |
| Pracownicy procesora GPU na węzeł | 1-4 |
| Okres istnienia zadania | 21 dni<sup>1</sup> |
| Okres istnienia zadania w węźle o niskim priorytecie | 7 dni<sup>2</sup> |
| Serwery parametrów na węzeł | 1 |

<sup>1</sup> maksymalny okres istnienia to czas od momentu uruchomienia przebiegu i po jego zakończeniu. Ukończone przebiegi są utrwalane w nieskończoność. Dane dla przebiegów nieukończonych w maksymalnym okresie istnienia są niedostępne.
<sup>2</sup> zadania w węźle o niskim priorytecie można zastąpić za każdym razem, gdy istnieje ograniczenie pojemności. Zalecamy wdrożenie punktów kontrolnych w zadaniu.

#### <a name="azure-machine-learning-pipelines"></a>Potoki Azure Machine Learning
[Potoki Azure Machine Learning](concept-ml-pipelines.md) mają następujące limity.

| **Zasób** | **Limit** |
| --- | --- |
| Etapy potoku | 30 000 |
| Obszary robocze na grupę zasobów | 800 |

### <a name="virtual-machines"></a>Maszyny wirtualne
Dla każdej subskrypcji platformy Azure obowiązuje limit liczby maszyn wirtualnych w ramach wszystkich usług. Rdzenie maszyn wirtualnych mają regionalne limity i limity regionalne dla poszczególnych rozmiarów. Oba limity są wymuszane osobno.

Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. Ta subskrypcja będzie mogła wdrażać 30 maszyn wirtualnych lub 30 D1 maszyn wirtualnych lub kombinację tych dwóch, które nie przekraczają łącznie 30 rdzeni.

Nie można wywoływać limitów dla maszyn wirtualnych powyżej wartości podanych w poniższej tabeli.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

Aby uzyskać więcej informacji, zobacz [limity Container Instances](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits).

### <a name="storage"></a>Magazyn
Usługa Azure Storage ma limit 250 kont magazynu na region na subskrypcję. Ten limit obejmuje konta magazynu w warstwie Standardowa i Premium.

Aby zwiększyć limit, należy wysłać żądanie przez [Pomoc techniczną platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Zespół usługi Azure Storage będzie przeglądać swój przypadek i może zatwierdzić do 250 kont magazynu dla regionu.


## <a name="workspace-level-quotas"></a>Przydziały na poziomie obszaru roboczego

Przydziały na poziomie obszaru roboczego umożliwiają zarządzanie Azure Machine Learning alokacji docelowej obliczeń między wieloma [obszarami roboczymi](concept-workspace.md) w ramach tej samej subskrypcji.

Domyślnie wszystkie obszary robocze mają ten sam limit przydziału dla rodzin maszyn wirtualnych. Można jednak ustawić limit przydziału dla poszczególnych rodzin maszyn wirtualnych w obszarze roboczym w ramach subskrypcji. Pozwala to na udostępnianie pojemności i uniknięcie problemów z rywalizacją o zasoby.

1. Przejdź do dowolnego obszaru roboczego w ramach subskrypcji.
1. W lewym okienku wybierz pozycję **użycie + przydziały**.
1. Wybierz kartę **Konfigurowanie przydziałów** , aby wyświetlić przydziały.
1. Rozwiń rodzinę maszyn wirtualnych.
1. Ustaw limit przydziału dla każdego obszaru roboczego wymienionego w danej rodzinie maszyn wirtualnych.

Nie można ustawić wartości ujemnej ani wartości większej niż limit przydziału na poziomie subskrypcji.

[![Zrzut ekranu przedstawiający Azure Machine Learning przydziału na poziomie obszaru roboczego.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Aby ustawić limit przydziału na poziomie obszaru roboczego, musisz mieć uprawnienia na poziomie subskrypcji.

## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów

Aby wyświetlić limit przydziału dla różnych zasobów platformy Azure, takich jak maszyny wirtualne, magazyn lub sieć, użyj Azure Portal:

1. W okienku po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **subskrypcje** w kategorii **Ogólne** .

2. Z listy subskrypcji wybierz subskrypcję, której chcesz wyszukać.

3. Wybierz pozycję **użycie + przydziały** , aby wyświetlić bieżące limity przydziału i użycie. Użyj filtrów, aby wybrać dostawcę i lokalizacje. 

Przydziały obliczeniowe Azure Machine Learning w ramach subskrypcji są zarządzane oddzielnie od innych przydziałów platformy Azure: 

1. Przejdź do obszaru roboczego **Azure Machine Learning** w Azure Portal.

2. W okienku po lewej stronie w sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **użycie + przydziały** , aby wyświetlić bieżące limity przydziału i użycie.

3. Wybierz subskrypcję, aby wyświetlić limity przydziałów. Odfiltruj do regionu, który Cię interesuje.

4. Można przełączać się między widokiem poziomu subskrypcji a widokiem poziomu obszaru roboczego.

## <a name="request-quota-increases"></a>Prośba o zwiększenie limitów przydziałów

Aby podnieść limit lub przydział powyżej domyślnego limitu, [Otwórz żądanie obsługi klienta w trybie online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) bez dodatkowych opłat.

Nie można podnieść limitów powyżej wartości maksymalnych przedstawionych w poprzednich tabelach. W przypadku braku maksymalnego limitu nie można dostosować limitu dla zasobu.

Gdy żądasz zwiększenia limitu przydziału, wybierz usługę, z której korzystasz. Na przykład wybierz Azure Machine Learning, Container Instances lub magazyn. W przypadku Azure Machine Learning obliczeń można wybrać przycisk **przydziału żądania** podczas wyświetlania przydziału w poprzednich krokach.

> [!NOTE]
> [Bezpłatne subskrypcje wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu przydziału. Jeśli masz bezpłatną subskrypcję wersji próbnej, możesz przeprowadzić uaktualnienie do subskrypcji [płatnej zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem. Aby uzyskać więcej informacji, zobacz [uaktualnianie bezpłatnej wersji próbnej platformy Azure do usługi płatność zgodnie z rzeczywistym](../cost-management-billing/manage/upgrade-azure-subscription.md) użyciem i [bezpłatne konto platformy Azure — często zadawane pytania](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Zwiększanie limitu przydziału prywatnego punktu końcowego i prywatnego systemu DNS

Istnieją limity liczby prywatnych punktów końcowych i prywatnych stref DNS, które można utworzyć w ramach subskrypcji.

Azure Machine Learning tworzy zasoby w ramach subskrypcji (klienta), ale niektóre scenariusze tworzą zasoby należące do firmy Microsoft.

 W następujących scenariuszach może być konieczne zażądanie limitu przydziału w ramach subskrypcji firmy Microsoft:

* Obszar roboczy z włączonym linkiem prywatnym platformy Azure z kluczem zarządzanym przez klienta (CMK)
* Usługa Azure Container Registry w przypadku obszaru roboczego poza Twoją siecią wirtualną
* Dołączanie klastra usługi Azure Kubernetes Service z włączoną usługą Private Link do obszaru roboczego

Aby zażądać przydziału dla tych scenariuszy, wykonaj następujące czynności:

1. [Utwórz żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) i wybierz następujące opcje w sekcji __podstawowe informacje__ :

    | Pole | Wybór |
    | ----- | ----- |
    | Typ problemu | **Szczegóły techniczne** |
    | Usługa | **Moje usługi**. Następnie na liście rozwijanej wybierz pozycję __Machine Learning__ . |
    | Typ problemu | **Konfiguracja i zabezpieczenia obszaru roboczego** |
    | Podtyp problemu | **Prywatne żądanie dotyczące przydziału punktów końcowych i Prywatna strefa DNS** |

2. W sekcji __szczegóły__ Użyj pola __Opis__ , aby podać region platformy Azure i scenariusz, który ma być używany. Jeśli musisz zażądać zwiększenia limitu przydziału dla wielu subskrypcji, Utwórz listę identyfikatorów subskrypcji w tym polu.

3. Wybierz pozycję __Utwórz__ , aby utworzyć żądanie.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Zrzut ekranu przedstawiający prywatne żądanie zwiększenia limitu przydziału prywatnego punktu końcowego i prywatnego.":::

## <a name="next-steps"></a>Następne kroki

+ [Planowanie i zarządzanie kosztami Azure Machine Learning](concept-plan-manage-cost.md)
