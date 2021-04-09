---
title: Wprowadzenie do modelu zasobów usługi Azure Service Fabric
description: Dowiedz się więcej o modelu zasobów Service Fabric — uproszczone podejście do definiowania aplikacji Service Fabric siatki.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626913"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Wprowadzenie do modelu zasobów usługi Service Fabric

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Model zasobów Service Fabric opisuje proste podejście do definiowania zasobów, które składają się na aplikację Service Fabric siatką. Poszczególne zasoby można wdrożyć w dowolnym środowisku Service Fabric.  Model zasobów Service Fabric jest również zgodny z modelem Azure Resource Manager. Następujące typy zasobów są obecnie obsługiwane w tym modelu:

- Aplikacje i usługi
- Sieci
- Bramy
- Wpisy tajne i wpisy tajne/wartości
- Woluminy

Każdy zasób jest szczegółowo opisany w pliku zasobów, który jest prostym dokumentem YAML lub JSON opisującym aplikację siatkową i jest obsługiwany przez platformę Service Fabric.

## <a name="applications-and-services"></a>Aplikacje i usługi

Zasób aplikacji jest jednostką wdrożenia, przechowywaniem wersji i okresem istnienia aplikacji siatki. Składa się z jednego lub więcej zasobów usług, które reprezentują mikrousługi. Każdy zasób usługi z kolei składa się z jednego lub więcej pakietów kodu, które opisują wszystko, czego potrzebujesz do uruchomienia obrazu kontenera skojarzonego z pakietem kodu.

![Aplikacje i usługi][Image1]

Zasób usługi deklaruje następujące kwestie:

- Nazwa, wersja i rejestr kontenera
- Zasoby procesora i pamięci wymagane dla każdego kontenera
- Punkty końcowe sieci
- Odwołania do innych zasobów, takich jak sieci, woluminy i wpisy tajne 

Wszystkie pakiety kodu zdefiniowane w ramach zasobu usługi są wdrażane i uaktywniane razem jako Grupa. Zasób usługi zawiera również informacje o liczbie wystąpień usługi do uruchomienia, a także odwołuje się do innych zasobów (na przykład zasobów sieciowych), od których zależy.

Jeśli aplikacja siatkowa składa się z więcej niż jednej usługi, nie ma gwarancji, że są one uruchamiane w tym samym węźle. Ponadto podczas uaktualniania aplikacji Niepowodzenie uaktualnienia pojedynczej usługi spowoduje przywrócenie wszystkich usług z powrotem do ich poprzedniej wersji.

Jak sugerowali wcześniej, cykl życia każdego wystąpienia aplikacji może być zarządzany niezależnie. Na przykład jedno wystąpienie aplikacji można uaktualnić niezależnie od innych wystąpień aplikacji. Zazwyczaj liczba usług w aplikacji jest dość mała, ponieważ im więcej usług zostało umieszczonych w aplikacji, tym trudniejsze jest zarządzanie każdą usługą niezależnie.

## <a name="networks"></a>Sieci

Zasób sieciowy jest indywidualnie wdrażany zasób, niezależnie od zasobu aplikacji lub usługi, który może odwoływać się do niego jako zależność. Służy do tworzenia sieci dla aplikacji. Wiele usług z różnych aplikacji może być częścią tej samej sieci.  Aby uzyskać więcej informacji, zapoznaj się z tematem [Obsługa sieci w aplikacjach Service Fabric siatki](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Bieżąca wersja zapoznawcza obsługuje tylko mapowanie jeden do jednego między aplikacjami i sieciami

![Sieć i Brama][Image2]

## <a name="gateways"></a>Bramy
Zasób bramy łączy dwie sieci i kieruje ruchem.  Brama umożliwia usługom komunikowanie się z klientami zewnętrznymi i zapewnia ruch przychodzący do usług.  Bramy można również używać do łączenia aplikacji siatkowej z własną istniejącą siecią wirtualną. Aby uzyskać więcej informacji, zapoznaj się z tematem [Obsługa sieci w aplikacjach Service Fabric siatki](service-fabric-mesh-networks-and-gateways.md).

![Sieć i Brama][Image2]

## <a name="secrets"></a>Wpisy tajne

Zasoby tajne są wdrażane niezależnie od zasobów aplikacji lub usług, które mogą odwoływać się do niej w zależności od siebie. Służy do bezpiecznego dostarczania wpisów tajnych do aplikacji. Wiele usług z różnych aplikacji może odwoływać się do wartości tego samego klucza tajnego.

## <a name="volumes"></a>Woluminy

Kontenery często udostępniają dyski tymczasowe. Dyski tymczasowe są nieulotne, jednak dzięki temu otrzymujesz nowy dysk tymczasowy i utracisz informacje w przypadku awarii kontenera. Trudno jest również udostępniać informacje na dyskach tymczasowych innym kontenerom. Woluminy są katalogami, które są instalowane w ramach wystąpień kontenera, których można użyć do utrwalania stanu. Woluminy zapewniają magazyn plików ogólnego przeznaczenia i umożliwiają odczytywanie i zapisywanie plików przy użyciu standardowych interfejsów API plików we/wy dysku. Zasób woluminu jest deklaratywnym sposobem opisywania sposobu, w jaki katalog jest zainstalowany, oraz magazynu zapasowego dla niego (Azure Files wolumin lub Service Fabric niezawodny wolumin).  Aby uzyskać więcej informacji, przeczytaj artykuł [przechowywanie stanu](service-fabric-mesh-storing-state.md#volumes).

![Diagram przedstawia usługę przepływające do woluminu dysku, który przepływa do obu Service Fabric niezawodny wolumin, następnie do zreplikowanego dysku lokalnego oraz do Azure Files woluminu, do magazynu sieciowego.][Image3]

## <a name="programming-models"></a>Modele programowania
Zasób usługi wymaga tylko obrazu kontenera do uruchomienia, do którego odwołuje się pakiet kodu skojarzony z zasobem. Można uruchomić dowolny kod, który jest pisany w dowolnym języku, przy użyciu dowolnych struktur wewnątrz kontenera, bez konieczności znajomości i używania interfejsów API specyficznych dla Service Fabric siatki. 

Kod aplikacji pozostaje przenośny nawet poza Service Fabric siatką, a wdrożenia aplikacji pozostają spójne niezależnie od języka lub platformy używanego do implementowania usług. Niezależnie od tego, czy aplikacja jest ASP.NET Core, czy tylko zestaw procesów i skryptów, model wdrażania zasobów siatki Service Fabric pozostaje taki sam. 

## <a name="packaging-and-deployment"></a>Pakowanie i wdrażanie

Aplikacje siatki Service Fabric w oparciu o model zasobów są spakowane jako kontenery platformy Docker.  Service Fabric siatka to udostępnione środowisko i kontenery z wieloma dzierżawcami zapewniają wysoki poziom izolacji.  Te aplikacje są opisane przy użyciu formatu JSON lub formatu YAML (który następnie jest konwertowany na format JSON). W przypadku wdrażania aplikacji siatki na platformie Azure Service Fabric siatka kod JSON używany do opisywania aplikacji jest szablonem Azure Resource Manager. Zasoby są mapowane na zasoby platformy Azure.  W przypadku wdrażania aplikacji sieci w klastrze Service Fabric (autonomicznym lub hostowanym przez platformę Azure) kod JSON używany do opisywania aplikacji jest formatem podobnym do szablonu Azure Resource Manager.  Po wdrożeniu aplikacje siatkowe mogą być zarządzane za pośrednictwem interfejsów HTTP lub interfejsu wiersza polecenia platformy Azure. 


## <a name="next-steps"></a>Następne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
