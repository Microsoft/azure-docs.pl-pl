---
title: Azure Functions hosting dedykowany
description: Dowiedz się więcej na temat korzyści z używania Azure Functions na dedykowanym App Service plan hostingu.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ee32d4c4a7d33664da88b133a1f7011fc09f298
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591057"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Dedykowane plany hostingu dla Azure Functions

Ten artykuł zawiera informacje na temat hostingu aplikacji funkcji w planie App Service, w tym w App Service Environment (ASE). Inne opcje hostingu znajdują się w [artykule plan hostingu](functions-scale.md).

Plan App Service definiuje zestaw zasobów obliczeniowych dla aplikacji do uruchomienia. Te zasoby obliczeniowe są analogiczne do [_farmy serwerów_](https://wikipedia.org/wiki/Server_farm) w konwencjonalnym hostingu. Co najmniej jednej aplikacji funkcji można skonfigurować do uruchamiania w tych samych zasobach obliczeniowych (plan App Service), jak w przypadku innych aplikacji App Service, takich jak aplikacje sieci Web. Te plany obejmują podstawowe, standardowe, Premium i izolowane jednostki SKU. Aby uzyskać szczegółowe informacje na temat działania planu App Service, zobacz [szczegółowe Omówienie planów Azure App Service](../app-service/overview-hosting-plans.md).

Należy wziąć pod uwagę plan App Service w następujących sytuacjach:

* Istnieją już istniejące, nieużywane maszyny wirtualne, na których uruchomiono inne wystąpienia App Service.
* Chcesz udostępnić niestandardowy obraz, na którym będą uruchamiane funkcje.

## <a name="billing"></a>Rozliczenia

Płacisz za aplikacje funkcji w planie App Service, tak jak w przypadku innych zasobów App Service. Różni się to od [planu zużycia](consumption-plan.md) Azure Functions lub hostingu [planu Premium](functions-premium-plan.md) , które mają składniki kosztów oparte na zużyciu. Opłaty są naliczane tylko za plan, niezależnie od tego, ile aplikacji funkcji lub aplikacji sieci Web działa w planie. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem App Service](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="always-on"></a><a name="always-on"></a> Zawsze włączone

Jeśli uruchamiasz plan App Service, należy włączyć ustawienie **zawsze** włączone, aby aplikacja funkcji działała poprawnie. W planie App Service środowisko uruchomieniowe funkcji przechodzi w stan bezczynności po kilku minutach braku aktywności, więc tylko Wyzwalacze HTTP będą wznawiać działanie funkcji. Ustawienie **zawsze włączone** jest dostępne tylko w planie App Service. Zgodnie z planem zużycia platforma automatycznie aktywuje aplikacje funkcji.

Nawet przy włączonej opcji zawsze włączone przekroczenie limitu czasu wykonywania poszczególnych funkcji jest kontrolowane przez `functionTimeout` ustawienie w [host.jsw](functions-host-json.md#functiontimeout) pliku projektu.

## <a name="scaling"></a>Skalowanie

Korzystając z planu App Service, można ręcznie skalować w poziomie, dodając więcej wystąpień maszyn wirtualnych. Możesz również włączyć funkcję automatycznego skalowania, chociaż automatyczne skalowanie będzie wolniejsze niż elastyczne skalowanie planu Premium. Aby uzyskać więcej informacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Możesz również skalować w górę, wybierając inny plan App Service. Aby uzyskać więcej informacji, zobacz [skalowanie w górę aplikacji na platformie Azure](../app-service/manage-scale-up.md). 

> [!NOTE] 
> Podczas uruchamiania funkcji JavaScript (Node.js) w planie App Service należy wybrać plan, który ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zobacz [Wybieranie planów App Service z pojedynczym rdzeniem](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>Środowiska usługi App Service

Uruchamianie w [App Service Environment](../app-service/environment/intro.md) (ASE) pozwala w pełni izolować funkcje i korzystać z większej liczby wystąpień niż plan App Service. Aby rozpocząć, zobacz.

Jeśli chcesz po prostu uruchomić aplikację funkcji w sieci wirtualnej, możesz to zrobić przy użyciu [planu Premium](functions-premium-plan.md). Aby dowiedzieć się więcej, zobacz [ustanawianie Azure Functions dostępu do lokacji prywatnej](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Następne kroki

+ [Opcje hostingu Azure Functions](functions-scale.md)
+ [Plan usługi Azure App Service — omówienie](../app-service/overview-hosting-plans.md)