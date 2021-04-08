---
title: Uaktualnij i Skaluj wystąpienie usługi Azure API Management | Microsoft Docs
description: W tym temacie opisano sposób uaktualniania i skalowania wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: bd36434bbfe435a53567c46728610627f99f987f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127791"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Uaktualnianie i skalowanie wystąpienia usługi Azure API Management  

Klienci mogą skalować wystąpienie API Management platformy Azure, dodając i usuwając jednostki. **Jednostka** składa się z dedykowanych zasobów platformy Azure i ma pewną pojemność obciążenia wyrażoną jako liczba wywołań interfejsu API miesięcznie. Ta liczba nie reprezentuje limitu wywołań, ale raczej maksymalną wartość przepływności, aby umożliwić planowanie nieograniczonej pojemności. Rzeczywista przepływność i opóźnienia różnią się w zależności od czynników, takich jak liczba i szybkość jednoczesnych połączeń, rodzaj i liczba skonfigurowanych zasad, rozmiar żądań i odpowiedzi oraz opóźnienie wewnętrznej bazy danych.

Pojemność i cena każdej jednostki są zależne od **warstwy** , w której znajduje się jednostka. Można wybrać jedną z czterech warstw: **Developer**, **Basic**, **Standard** i **Premium**. Jeśli trzeba zwiększyć pojemność usługi w ramach warstwy, należy dodać jednostkę. Jeśli warstwa aktualnie wybrana w wystąpieniu API Management nie zezwala na dodawanie większej liczby jednostek, należy przeprowadzić uaktualnienie do warstwy wyższego poziomu.

Cena każdej jednostki i dostępnych funkcji (na przykład wdrożenie z wielu regionów) zależy od warstwy wybranej dla wystąpienia API Management. W artykule [szczegóły cennika](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) opisano cenę jednostkową i funkcje, które można uzyskać w poszczególnych warstwach. 

>[!NOTE]
>W artykule [szczegóły cennika](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przedstawiono przybliżoną liczbę pojemności jednostek w każdej warstwie. Aby uzyskać dokładniejsze numery, należy zapoznać się z realistycznym scenariuszem dla interfejsów API. Zobacz [pojemność artykułu wystąpienia usługi Azure API Management](api-management-capacity.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Mieć wystąpienie API Management. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

+ Zrozumienie koncepcji [pojemności wystąpienia usługi Azure API Management](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Uaktualnianie i skalowanie  

Można wybrać jedną z czterech warstw: **Developer**, **Basic**,  **Standard** i **Premium**. Aby oszacować usługę, należy użyć warstwy **dewelopera** . nie powinna być używana w środowisku produkcyjnym. Warstwa **dewelopera** nie ma umowy SLA i nie można skalować tej warstwy (Dodaj/Usuń jednostki). 

**Podstawowa**, **standardowa** i **Premium** to warstwy produkcyjne, które mają umowę SLA i mogą być skalowane. Warstwa **podstawowa** to najtańsza warstwa z umową SLA i można ją skalować do dwóch jednostek. Warstwa **standardowa** może być skalowana do maksymalnie czterech jednostek. Możesz dodać dowolną liczbę jednostek do warstwy **Premium** .

Warstwa **Premium** umożliwia dystrybucję pojedynczego wystąpienia usługi Azure API Management w dowolnej liczbie żądanych regionów świadczenia usługi Azure. Podczas początkowego tworzenia usługi API Management platformy Azure wystąpienie zawiera tylko jedną jednostkę i znajduje się w jednym regionie świadczenia usługi Azure. Region początkowy jest wyznaczono jako region **podstawowy** . Dodatkowe regiony można łatwo dodawać. Podczas dodawania regionu należy określić liczbę jednostek, które mają zostać przydzielone. Na przykład można mieć jedną jednostkę w regionie **podstawowym** i pięć jednostek w innym regionie. Możesz dostosować liczbę jednostek do ruchu w poszczególnych regionach. Aby uzyskać więcej informacji, zobacz [jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure](api-management-howto-deploy-multi-region.md).

Możesz uaktualnić i obniżyć wersję do i z dowolnej warstwy. Uaktualnianie lub obniżanie poziomu może usunąć niektóre funkcje — na przykład wdrożenie sieci wirtualnych lub z wieloregionem, gdy jest to wersja standardowa lub podstawowa z warstwy Premium.

> [!NOTE]
> Zastosowanie procesu uaktualniania lub skalowania może potrwać od 15 do 45 minut. Po zakończeniu otrzymasz powiadomienie.

> [!NOTE]
> Usługa API Management w warstwie **zużycia** jest skalowana automatycznie na podstawie ruchu.

## <a name="scale-your-api-management-service"></a>Skalowanie usługi API Management

![Usługa skalowanie API Management w Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. Przejdź do usługi API Management w [Azure Portal](https://portal.azure.com/).
2. Wybierz opcję **lokalizacje** z menu.
3. Kliknij wiersz z lokalizacją, która ma zostać przeskalowana.
4. Określ nową liczbę **jednostek** — Użyj suwaka lub wpisz liczbę.
5. Kliknij pozycję **Zastosuj**.

## <a name="change-your-api-management-service-tier"></a>Zmień warstwę usługi API Management

1. Przejdź do usługi API Management w [Azure Portal](https://portal.azure.com/).
2. Kliknij **warstwę cenową** w menu.
3. Wybierz żądaną warstwę usług z listy rozwijanej. Użyj suwaka, aby określić skalę usługi API Management po zmianie.
4. Kliknij pozycję **Zapisz**.

## <a name="downtime-during-scaling-up-and-down"></a>Przestoje podczas skalowania w górę i w dół
W przypadku skalowania z lub do warstwy dewelopera wygaśnie Przestój. W przeciwnym razie nie ma przestojów. 

## <a name="compute-isolation"></a>Izolacja obliczeniowa
Jeśli wymagania dotyczące zabezpieczeń obejmują [izolację obliczeniową](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), można użyć warstwy cenowej **izolowanej** . Ta warstwa zapewnia, że zasoby obliczeniowe wystąpienia usługi API Management zużywają cały Host fizyczny i zapewniają wymagany poziom izolacji wymaganej do pomocy technicznej, na przykład działy zabezpieczeń na poziomie 5 (IL5) działu USA. Aby uzyskać dostęp do warstwy izolowanej, [Utwórz bilet pomocy technicznej](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Następne kroki

- [Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure](api-management-howto-deploy-multi-region.md)
- [Jak automatycznie skalować wystąpienie usługi Azure API Management](api-management-howto-autoscale.md)
- [Planowanie i zarządzanie kosztami API Management](plan-manage-costs.md)
- [Limity API Management](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)