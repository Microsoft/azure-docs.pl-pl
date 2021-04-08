---
title: Model aplikacji Service Fabric platformy Azure
description: Jak modelować i opisywać aplikacje i usługi na platformie Azure Service Fabric przy użyciu plików manifestu aplikacji i usługi.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85963737"
---
# <a name="model-an-application-in-service-fabric"></a>Modelowanie aplikacji w Service Fabric
Ten artykuł zawiera omówienie modelu aplikacji Service Fabric platformy Azure oraz sposób definiowania aplikacji i usługi za pośrednictwem plików manifestu.

## <a name="understand-the-application-model"></a>Omówienie modelu aplikacji
Aplikacja jest kolekcją usług składowych, które wykonują określoną funkcję lub funkcje. Usługa wykonuje kompletną i autonomiczną funkcję, która może być uruchamiana i uruchamiana niezależnie od innych usług.  Usługa składa się z kodu, konfiguracji i danych. W przypadku każdej usługi kod składa się z wykonywalnych plików binarnych, konfiguracja składa się z ustawień usługi, które mogą być ładowane w czasie wykonywania, a dane składają się z dowolnych danych statycznych, które mają być używane przez usługę. Każdy składnik w tym hierarchicznym modelu aplikacji może być w wersji i uaktualniony niezależnie.

![Model aplikacji Service Fabric][appmodel-diagram]

Typ aplikacji jest kategoryzacją aplikacji i składa się z pakietu typów usług. Typ usługi jest kategoryzacją usługi. Kategoryzacja może mieć różne ustawienia i konfiguracje, ale podstawowe funkcje pozostają takie same. Wystąpienia usługi są różnymi wariantami konfiguracji usług tego samego typu usługi.  

Klasy (lub "typy") aplikacji i usług są opisane za poorednictwem plików XML (manifestów aplikacji i manifestów usługi).  Manifesty opisują aplikacje i usługi oraz szablony, dla których można utworzyć wystąpienie aplikacji z magazynu obrazów klastra.  Manifesty są szczegółowo omówione w [manifestach aplikacji i usług](service-fabric-application-and-service-manifests.md). Definicja schematu dla ServiceManifest.xml i pliku ApplicationManifest.xml jest instalowana z zestawem SDK Service Fabric i narzędziami do *folderu C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Schemat XML jest udokumentowany w [dokumentacji schematu ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

Kod dla różnych wystąpień aplikacji działa jako osobne procesy nawet w przypadku hostowania tego samego węzła Service Fabric. Ponadto cykl życia każdego wystąpienia aplikacji może być zarządzany (na przykład uaktualniony) niezależnie od siebie. Na poniższym diagramie przedstawiono, jak typy aplikacji składają się z typów usług, które z kolei składają się na kod, konfigurację i pakiety danych. Aby uprościć diagram, wyświetlane są tylko pakiety Code/config/Data dla programu `ServiceType4` , chociaż każdy typ usługi będzie zawierać niektóre lub wszystkie typy pakietów.

![Service Fabric typów aplikacji i typów usług][cluster-imagestore-apptypes]

W klastrze może istnieć co najmniej jedno wystąpienie typu usługi. Na przykład wystąpienia usługi stanowej lub repliki mają wysoką niezawodność dzięki replikacji stanu między replikami znajdującymi się w różnych węzłach w klastrze. Replikacja zasadniczo zapewnia nadmiarowość do dostępności usługi, nawet jeśli jeden węzeł w klastrze ulegnie awarii. Podzielona na [partycje usługa](service-fabric-concepts-partitioning.md) dodatkowo dzieli stan (i wzorce dostępu do tego stanu) między węzłami w klastrze.

Na poniższym diagramie przedstawiono relację między aplikacjami i wystąpieniami usług, partycjami i replikami.

![Partycje i repliki w ramach usługi][cluster-application-instances]

> [!TIP]
> Układ aplikacji w klastrze można wyświetlić za pomocą narzędzia Service Fabric Explorer dostępnego pod adresem http:// &lt; yourclusteraddress &gt; : 19080/Explorer. Aby uzyskać więcej informacji, zobacz [wizualizowanie klastra przy użyciu Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
- Dowiedz się więcej o [stanie](service-fabric-concepts-state.md)usług, [partycjonowaniu](service-fabric-concepts-partitioning.md)i [dostępności](service-fabric-availability-services.md).
- Przeczytaj o sposobie definiowania aplikacji i usług w [manifestach aplikacji i usług](service-fabric-application-and-service-manifests.md).
- [Modele hostingu aplikacji](service-fabric-hosting-model.md) opisują relację między replikami (lub wystąpieniami) wdrożonej usługi i procesu hosta usługi.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


