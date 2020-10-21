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
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 4b072257d49011819fe19d6e2901560df43b26dc
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275552"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Zarządzanie & zwiększenie limitów przydziału dla zasobów z Azure Machine Learning

Na platformie Azure są stosowane limity i przydziały, które uniemożliwiają przekroczenie budżetu z powodu oszustw oraz do przestrzegania ograniczeń pojemności platformy Azure. Te limity należy wziąć pod uwagę podczas skalowania obciążeń produkcyjnych. W tym artykule przedstawiono następujące informacje:

> [!div class="checklist"]
> + Domyślne limity zasobów platformy Azure powiązane z [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Wyświetlanie przydziałów i limitów.
> + Utwórz przydziały na poziomie obszaru roboczego.
> + Zwiększenie limitu przydziału żądań.
> + Prywatne przydziały punktów końcowych i DNS.

Wraz z zarządzaniem przydziałami można także dowiedzieć się, jak [planować & zarządzanie kosztami Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Uwagi specjalne

+ Przydział jest limitem kredytowym, a nie gwarancją wydajności. Jeśli masz zapotrzebowanie na dużą skalę, [skontaktuj się z pomocą techniczną platformy Azure w celu zwiększenia limitu przydziału](#request-quota-increases).

+ Przydział jest udostępniany wszystkim usługom w ramach subskrypcji, w tym Azure Machine Learning. Oblicz użycie dla wszystkich usług podczas oceniania pojemności.
    + Azure Machine Learning COMPUTE jest wyjątkiem i ma oddzielny przydział od podstawowego limitu przydziału. 

+ Domyślne limity różnią się w zależności od typu kategorii oferty, takich jak bezpłatna wersja próbna, płatność zgodnie z rzeczywistym użyciem oraz seria maszyn wirtualnych, takich jak Dv2, F, G i tak dalej.

## <a name="default-resource-quotas"></a>Domyślne przydziały zasobów

Ta sekcja zawiera informacje o domyślnych i maksymalnych limitach przydziału dla następujących zasobów:

+ Maszyny wirtualne
+ Środowisko obliczeniowe usługi Azure Machine Learning
+ Potoki Azure Machine Learning
+ Container Instances
+ Magazyn

> [!IMPORTANT]
> Limity mogą ulec zmianie. Najnowsza wartość znajduje się w [dokumencie](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) dotyczącym limitu przydziału na poziomie usług dla wszystkich platform Azure.

### <a name="virtual-machines"></a>Maszyny wirtualne
Dla każdej subskrypcji platformy Azure obowiązuje limit liczby maszyn wirtualnych w ramach wszystkich usług. Rdzenie maszyn wirtualnych mają regionalny, ogólny limit i limit regionalny dla każdej serii rozmiarów (Dv2, F itp.). Oba limity są wymuszane osobno.

Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. Ta subskrypcja będzie mogła wdrażać 30 maszyn wirtualnych lub 30 D1 maszyn wirtualnych lub kombinację tych dwóch, które nie przekraczają łącznie 30 rdzeni.

Limitów dla maszyn wirtualnych nie można zwiększyć powyżej wartości podanej w poniższej tabeli.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Środowisko obliczeniowe usługi Azure Machine Learning
[Azure Machine Learning COMPUTE](concept-compute-target.md#azure-machine-learning-compute-managed) ma domyślny limit przydziału dla liczby rdzeni i liczby unikatowych zasobów obliczeniowych dozwolonych na region w ramach subskrypcji. Ten limit przydziału jest oddzielony od limitu przydziału rdzeni maszyny wirtualnej z poprzedniej sekcji.

[Poproś o zwiększenie limitu przydziału](#request-quota-increases) , aby podnieść limity w tej sekcji do **maksymalnego limitu** pokazanego w tabeli.

Dostępne zasoby:
+ Liczba **rdzeni dedykowanych na region** ma domyślny limit 24-300 w zależności od typu oferty subskrypcji.  Liczbę rdzeni dedykowanych na subskrypcję można zwiększyć dla każdej rodziny maszyn wirtualnych. Wyspecjalizowane rodziny maszyn wirtualnych, takie jak NCv2, Seria NCV3 lub ND, zaczynają się domyślnie od zera.

+ **Rdzenie o niskim priorytecie na region** mają domyślny limit 100-3000 w zależności od typu oferty subskrypcji. Liczbę rdzeni o niskim priorytecie na subskrypcję można zwiększyć i jest to jedna wartość w różnych rodzinach maszyn wirtualnych.

+ **Klastry na region** mają domyślny limit 200. Są one współużytkowane przez klaster szkoleniowy i wystąpienie obliczeniowe (które jest traktowane jako klaster z pojedynczym węzłem w celu przekroczenia limitu przydziału).

W poniższej tabeli przedstawiono dodatkowe limity, których nie można przekroczyć.

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| Obszary robocze na grupę zasobów | 800 |
| Węzły w jednym Azure Machine Learning zasobów obliczeniowych (AmlCompute) | węzły 100 |
| Procesy MPI procesora GPU na węzeł | 1-4 |
| Pracownicy procesora GPU na węzeł | 1-4 |
| Okres istnienia zadania | 21 dni<sup>1</sup> |
| Okres istnienia zadania w węźle Low-Priority | 7 dni<sup>2</sup> |
| Serwery parametrów na węzeł | 1 |

<sup>1</sup> maksymalny okres istnienia odnosi się do czasu między rozpoczęciem i zakończeniem przebiegu. Ukończone przebiegi są utrwalane w nieskończoność. Dane dla przebiegów nieukończonych w maksymalnym okresie istnienia są niedostępne.
<sup>2</sup> zadania w węźle Low-Priority można zastąpić w dowolnym momencie, gdy istnieje ograniczenie pojemności. Zalecamy zaimplementowanie punktów kontrolnych w zadaniu.

### <a name="azure-machine-learning-pipelines"></a>Potoki Azure Machine Learning
[Potoki Azure Machine Learning](concept-ml-pipelines.md) mają następujące limity.

| **Zasób** | **Limit** |
| --- | --- |
| Etapy potoku | 30 000 |
| Obszary robocze na grupę zasobów | 800 |

### <a name="container-instances"></a>Wystąpienia kontenerów

Aby uzyskać więcej informacji, zobacz [limity Container Instances](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Magazyn
Dla kont usługi Azure Storage obowiązuje limit 250 kont magazynu na region na subskrypcję. Dotyczy to zarówno kont standardowych, jak i Premium Storage.

Aby zwiększyć limit, należy wysłać żądanie przez [Pomoc techniczną platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Zespół usługi Azure Storage zapoznaje się z Twoim przypadkiem i może zatwierdzić do 250 kont magazynu dla regionu.


## <a name="workspace-level-quota"></a>Przydział poziomu obszaru roboczego

Przydziały poziomu obszaru roboczego umożliwiają zarządzanie Azure Machine Learning alokacji docelowej obliczeń między wieloma [obszarami roboczymi](concept-workspace.md) w ramach tej samej subskrypcji.

Domyślnie wszystkie obszary robocze mają ten sam limit przydziału dla rodzin maszyn wirtualnych. Można jednak ustawić limit przydziału dla poszczególnych rodzin maszyn wirtualnych w obszarze roboczym w ramach subskrypcji. Pozwala to na udostępnianie pojemności i uniknięcie problemów z rywalizacją o zasoby:

1. Przejdź do dowolnego obszaru roboczego w ramach subskrypcji.
1. W lewym okienku wybierz pozycję **użycie + przydziały**.
1. Wybierz kartę **Konfigurowanie przydziałów** , aby wyświetlić przydziały.
1. Rozwiń rodzinę maszyn wirtualnych.
1. Ustaw limit przydziału dla każdego obszaru roboczego wymienionego w danej rodzinie maszyn wirtualnych.

Nie można ustawić wartości ujemnej ani wartości większej niż limit przydziału poziomu subskrypcji.

[![Limit przydziału poziomu obszaru roboczego Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Do ustawiania przydziału na poziomie obszaru roboczego wymagane są uprawnienia na poziomie subskrypcji.

## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów

Aby wyświetlić przydziały dla różnych zasobów platformy Azure, takich jak Virtual Machines, Storage i Network, użyj Azure Portal:

1. W okienku po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **subskrypcje** w kategorii Ogólne.

2. Z listy subskrypcji wybierz subskrypcję, której chcesz wyszukać.

3. Wybierz pozycję **użycie + przydziały** , aby wyświetlić bieżące limity przydziału i użycie. Użyj filtrów, aby wybrać dostawcę i lokalizacje. 

Przydziały obliczeń Azure Machine Learning w ramach subskrypcji są zarządzane oddzielnie od innych przydziałów platformy Azure. 

1. Przejdź do obszaru roboczego **Azure Machine Learning** w Azure Portal.

2. W okienku po lewej stronie w **sekcji Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **użycie + przydziały** , aby wyświetlić bieżące limity przydziału i użycie.

3. Wybierz subskrypcję, aby wyświetlić limity przydziałów. Pamiętaj, aby odfiltrować do regionu, który Cię interesuje.

4. Można przełączać się między widokiem poziomu subskrypcji i widokami poziomu obszaru roboczego.

## <a name="request-quota-increases"></a>Prośba o zwiększenie limitów przydziałów

Aby podnieść limit lub przydział powyżej domyślnego limitu, [Otwórz żądanie obsługi klienta w trybie online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) bez dodatkowych opłat.

Limitów nie można zwiększyć powyżej wartości maksymalnego limitu pokazanej w powyższych tabelach. W przypadku braku maksymalnego limitu nie można dostosować limitu dla zasobu.

Podczas żądania zwiększenia limitu przydziału wybierz usługę, z którą żądasz podniesienia limitu przydziału. Na przykład Azure Machine Learning, Container Instances, Storage itd. W przypadku Azure Machine Learning obliczeń można wybrać przycisk **limitu przydziału** podczas wyświetlania przydziału zgodnie z powyższymi krokami.

> [!NOTE]
> [Bezpłatne subskrypcje wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu przydziału. Jeśli masz [bezpłatną subskrypcję wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do subskrypcji [płatnej zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem. Aby uzyskać więcej informacji, zobacz [uaktualnianie bezpłatnej wersji próbnej platformy Azure do usługi płatność zgodnie z rzeczywistym](../billing/billing-upgrade-azure-subscription.md) użyciem i  [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Wzrost przydziału prywatnego punktu końcowego i prywatnego systemu DNS

Istnieją ograniczenia dotyczące liczby prywatnych punktów końcowych i prywatnych stref DNS, które można utworzyć w ramach subskrypcji.

Podczas Azure Machine Learning tworzenia zasobów w ramach subskrypcji (klienta) istnieje kilka scenariuszy, które tworzą zasoby należące do firmy Microsoft.

 W następujących scenariuszach może być konieczne zażądanie limitu przydziału w ramach subskrypcji firmy Microsoft:

* __Prywatny obszar roboczy z obsługą linku z kluczem zarządzanym przez klienta (CMK)__
* __Azure Container Registry obszaru roboczego za siecią wirtualną__
* __Dołączanie do obszaru roboczego klastra usługi Azure Kubernetes Service z włączonym linkiem prywatnym__.

Aby zażądać przydziału dla tych scenariuszy, wykonaj następujące czynności:

1. [Utwórz żądanie pomocy technicznej platformy Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) i wybierz następujące opcje z sekcji __podstawowe informacje__ :

    | Pole | Wybór |
    | ----- | ----- |
    | Typ problemu | Szczegóły techniczne |
    | Usługa | Moje usługi. Na liście rozwijanej wybierz pozycję __Machine Learning__ . |
    | Typ problemu | Konfiguracja i zabezpieczenia obszaru roboczego |
    | Podtyp problemu | Prywatne żądanie dotyczące przydziału punktów końcowych i Prywatna strefa DNS |

2. W sekcji __szczegóły__ Użyj pola __Opis__ , aby podać region platformy Azure, który ma być używany, i scenariusz, którego planujesz użyć. Jeśli trzeba będzie zażądać zwiększenia limitu przydziału dla wielu subskrypcji, należy również wyświetlić identyfikatory subskrypcji w tym polu.

3. Wybierz pozycję __Utwórz__ , aby utworzyć żądanie.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Zrzut ekranu przedstawiający prywatne żądanie zwiększenia limitu przydziału prywatnego punktu końcowego i prywatnego":::

## <a name="next-steps"></a>Następne kroki

+ [Planowanie & zarządzanie kosztami Azure Machine Learning](concept-plan-manage-cost.md)
