---
title: Rozwiązywanie problemów z usługą FabricInternalServerError lub ServiceAllocationFailure podczas wdrażania usługi w chmurze (klasycznej) w usłudze Azure | Microsoft Docs
description: W tym artykule pokazano, jak rozwiązać wyjątek FabricInternalServerError lub ServiceAllocationFailure podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0883178779179df2e531123b8a500c62d42530e4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877448"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Rozwiązywanie problemów z usługą FabricInternalServerError lub ServiceAllocationFailure podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure

W tym artykule rozwiążesz problemy z błędami alokacji, których kontroler sieci szkieletowej nie może przydzielić podczas wdrażania usługi Azure Cloud Service (klasycznej).

Podczas wdrażania wystąpień w usłudze w chmurze lub dodawania nowych wystąpień roli Sieć Web lub Proces roboczy Microsoft Azure zasoby obliczeniowe.

Czasami możesz otrzymywać błędy podczas tych operacji nawet przed osiągnięciem limitu subskrypcji platformy Azure.

> [!TIP]
> Informacje te mogą być również przydatne podczas planowania wdrożenia usług.

## <a name="symptom"></a>Objaw

W Azure Portal przejdź do usługi w chmurze (klasycznej) i na pasku bocznym wybierz pozycję *Dziennik operacji (klasyczny),* aby wyświetlić dzienniki.

![Obraz przedstawia blok Dziennik operacji (klasyczny).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Podczas inspekcji dzienników usługi w chmurze (klasycznej) zostanie wyświetlony następujący wyjątek:

|Wyjątek  |Komunikat o błędzie  |
|---------|---------|
FabricInternalServerError     |Operacja nie powiodła się z kodem błędu "InternalError" i errorMessage "Serwer napotkał błąd wewnętrzny. Ponów próbę wykonania żądania.".|
|ServiceAllocationFailure     |Operacja nie powiodła się z kodem błędu "InternalError" i errorMessage "Serwer napotkał błąd wewnętrzny. Ponów próbę wykonania żądania.".|

## <a name="cause"></a>Przyczyna

*FabricInternalServerError* i *ServiceAllocationFailure* to wyjątki, które mogą wystąpić, gdy kontroler sieci szkieletowej nie może przydzielić wystąpień w klastrze. Główna przyczyna różni się, jeśli usługa w chmurze jest **przypięta lub** nie jest **przypięta.**

- [**Nie przypięte:** Błędy po pierwszym wdrożeniu nowej usługi w chmurze](#not-pinned-to-a-cluster)
- [**Przypięte:** Błędy z istniejących usług w chmurze](#pinned-to-a-cluster)

> [!NOTE]
> Gdy pierwsze wystąpienie jest wdrażane w usłudze w chmurze (w środowisku przejściowym lub produkcyjnym), ta usługa w chmurze jest przypinana do klastra.
>
> Z czasem zasoby w tej puli zasobów mogą stać się w pełni wykorzystywane. Jeśli usługa w chmurze żąda alokacji dodatkowych zasobów, gdy w przypiętej puli zasobów jest niewystarczająca ilość zasobów, żądanie spowoduje błąd [alokacji.](cloud-services-allocation-failures.md)

## <a name="solution"></a>Rozwiązanie

Postępuj zgodnie ze wskazówkami dla błędów alokacji w następujących scenariuszach.

### <a name="not-pinned-to-a-cluster"></a>Nie przypięte do klastra

Podczas pierwszego wdrażania usługi w chmurze (klasycznej) klaster nie został jeszcze wybrany, więc usługa w chmurze nie jest *przypięta.* Na platformie Azure może wystąpić błąd wdrażania, ponieważ:

- Wybrano określony rozmiar, który nie jest dostępny w regionie.
- Kombinacja rozmiarów wymaganych dla różnych ról nie jest dostępna w regionie.

W przypadku wystąpienia błędu alokacji w tym scenariuszu zalecanym działaniem jest sprawdzenie dostępnych rozmiarów w regionie i zmiana wcześniej określonego rozmiaru.

1. Rozmiary dostępne w regionie można sprawdzić na stronie [produktów usługi w chmurze (klasycznej).](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services)

    > [!NOTE]
    > Na *stronie* Produkty nie będzie pokazywana dostępna pojemność. W przypadku każdej nowej alokacji platforma Azure powinna mieć możliwość wyboru optymalnego klastra w Twoim regionie w tym momencie.

1. Zaktualizuj plik definicji usługi dla usługi w chmurze (wersja klasyczna), aby określić [inny rozmiar produktu](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) niż region.

### <a name="pinned-to-a-cluster"></a>Przypięte do klastra

Istniejące usługi w chmurze *są przypinane* do klastra. Wszelkie dalsze wdrożenia usługi w chmurze (klasycznej) będą odbywać się w tym samym klastrze.

Jeśli w tym scenariuszu wystąpi błąd alokacji, zalecanym kursem działania jest ponowne uaktualnienie do nowej usługi w chmurze (klasycznej) (i zaktualizowanie *rekordu CNAME).*

> [!TIP]
> To rozwiązanie ma największą szansę na powodzenie, ponieważ pozwala platformie na wybór spośród wszystkich klastrów w tym regionie.

> [!NOTE]
> To rozwiązanie powinno nie wiązać się z przestojami.

1. Wdrażanie obciążenia w nowej usłudze w chmurze (klasycznej).
    - Dalsze instrukcje można znaleźć w przewodniku How [to create and deploy a Cloud service (classic) (Jak](cloud-services-how-to-create-deploy-portal.md) utworzyć i wdrożyć usługę w chmurze (klasyczną).

    > [!WARNING]
    > Jeśli nie chcesz utracić adresu IP skojarzonego z tym miejscem wdrożenia, możesz użyć rozwiązania [3](cloud-services-allocation-failures.md#solutions)— zachowaj adres IP .

1. Zaktualizuj rekord *CNAME* lub *A,* aby wskazać ruch do nowej usługi w chmurze (klasycznej).
    - Dalsze instrukcje można znaleźć w przewodniku Konfigurowanie niestandardowej nazwy domeny dla usługi [Azure Cloud Service (wersja](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) klasyczna).

1. Gdy ruch nie będzie przenoszyny do starej witryny, możesz usunąć starą usługę w chmurze (klasyczną).
    - Zobacz przewodnik [Usuwanie wdrożeń i usługi w chmurze (klasycznej),](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) aby uzyskać dalsze instrukcje.
    - Aby wyświetlić ruch sieciowy w usłudze w chmurze (klasycznej), zobacz [Wprowadzenie do monitorowania usługi w chmurze (klasycznej).](cloud-services-how-to-monitor.md)

Zobacz [Rozwiązywanie problemów z błędami alokacji usługi w chmurze (klasycznej) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) dalsze kroki korygowania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązań błędów alokacji i informacji w tle:

> [!div class="nextstepaction"]
> [Błędy alokacji — usługa w chmurze (klasyczna)](cloud-services-allocation-failures.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Swój problem możesz opublikować na tych forach lub opublikować w [ @AzureSupport serwisie Twitter.](https://twitter.com/AzureSupport) Możesz również przesłać wniosek o pomoc techniczną platformy Azure. Aby przesłać wniosek o pomoc techniczną, na stronie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) wybierz pozycję Uzyskaj pomoc *techniczną.*
