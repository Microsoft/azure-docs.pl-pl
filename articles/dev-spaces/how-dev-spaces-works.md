---
title: Jak działa usługa Azure Dev Spaces
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Opisuje procesy, które Azure Dev Spaces
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963707"
---
# <a name="how-azure-dev-spaces-works"></a>Jak działa usługa Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Tworzenie aplikacji Kubernetes może być trudne. Wymagane są pliki konfiguracyjne Docker i Kubernetes. Należy ustalić sposób lokalnego testowania aplikacji i korzystania z innych usług zależnych. Być może musisz obsługiwać tworzenie i testowanie wielu usług jednocześnie i z zespołem deweloperów.

Azure Dev Spaces zapewnia wiele sposobów na szybkie Iterowanie i debugowanie aplikacji Kubernetes oraz współpracę z zespołem. W tym artykule opisano, jakie Azure Dev Spaces można wykonać i jak działa.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Szybkie Iterowanie i debugowanie aplikacji Kubernetes

Azure Dev Spaces zmniejsza nakład pracy, aby opracowywać, testować i iterować aplikację Kubernetes w kontekście klastra AKS. To zmniejszenie nakładu pracy pozwala deweloperom skupić się na logice biznesowej aplikacji i nie konfigurować usług do uruchamiania w Kubernetes.

### <a name="bridge-to-kubernetes"></a>Mostek na platformę Kubernetes

Za pomocą programu Bridge to Kubernetes można połączyć komputer deweloperski z klastrem Kubernetes, co pozwala na uruchamianie i debugowanie kodu na komputerze deweloperskim, tak jakby był uruchomiony w klastrze. Mostek do Kubernetes przekierowuje ruch między podłączonym klastrem, uruchamiając go w klastrze, który działa jako agent zdalny do przekierowywania ruchu między komputerem deweloperskim i klastrem. To przekierowanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze, aby komunikować się tak, jakby znajdowały się w tym samym klastrze. Aby uzyskać więcej informacji na temat łączenia komputera deweloperskiego z klastrem Kubernetes, zobacz [jak działa mostek do Kubernetes][how-it-works-bridge-to-kubernetes].

### <a name="run-your-code-in-aks"></a>Uruchamianie kodu w AKS

Oprócz przekierowywania ruchu między komputerem deweloperskim i klastrem AKS, za pomocą Azure Dev Spaces można skonfigurować i szybko uruchomić kod bezpośrednio w AKS. Dzięki programowi Visual Studio, Visual Studio Code lub interfejsie wiersza polecenia Azure Dev Spaces usługa Azure dev Spaces przekaże kod do klastra, a następnie skompiluje go i uruchomi. Usługa Azure dev Spaces umożliwia również inteligentne synchronizowanie zmian kodu i ponowne uruchomienie usługi w celu odzwierciedlenia zmian w razie potrzeby. Podczas uruchamiania kodu dzienniki kompilacji i ślady HTTP są przesyłane strumieniowo z powrotem do klienta, dzięki czemu można monitorować postęp i diagnozować problemy. Możesz również użyć Azure Dev Spaces, aby dołączyć debuger w programie Visual Studio i Visual Studio Code do usług Java, Node.js i .NET Core. Aby uzyskać więcej informacji, zobacz [jak przygotowywanie projektu dla Azure dev Spaces działa][how-it-works-prep], [jak uruchomić swój kod przy użyciu Azure dev Spaces Works][how-it-works-up]oraz [jak zdalne debugowanie kodu za pomocą Azure dev Spaces działa][how-it-works-remote-debugging].

## <a name="team-development"></a>Programowanie zespołowe

Azure Dev Spaces ułatwia zespołom wydajną pracę w aplikacji w tym samym klastrze AKS bez zakłócania pracy.

### <a name="intelligent-routing-between-dev-spaces"></a>Inteligentne kierowanie między miejscami do deweloperów

Dzięki Azure Dev Spaces zespół może współdzielić jeden klaster AKS z uruchomioną aplikacją natywną w chmurze i utworzyć odizolowane miejsca programistyczne, w których zespół może opracowywać, testować i debugować bez zakłócania innych miejsc dev. Wersja linii bazowej aplikacji jest uruchamiana w głównym miejscu deweloperskim. Członkowie zespołu tworzą niezależne miejsca do użytku deweloperskiego na podstawie przestrzeni głównej na potrzeby tworzenia, testowania i debugowania zmian w aplikacji. Dzięki możliwościom routingu w miejscach deweloperskich, podrzędne miejsca dev umożliwiają kierowanie żądań między usługami uruchomionymi w podrzędnym obszarze dev i nadrzędnym miejscu dev. Ten Routing pozwala deweloperom uruchamiać własne wersje usługi przy ponownym używaniu usług zależnych od obszaru nadrzędnego. Każdy obszar podrzędny ma własny unikatowy adres URL, który może być współużytkowany przez inne osoby do współpracy. Aby uzyskać więcej informacji na temat sposobu działania routingu w Azure Dev Spaces, zobacz [jak routing współdziała z Azure dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Testowanie na żywo otwarte żądanie ściągnięcia

Możesz również użyć akcji usługi GitHub z Azure Dev Spaces, aby przetestować zmiany w aplikacji w żądaniu ściągnięcia bezpośrednio w klastrze przed scaleniem. Azure Dev Spaces może automatycznie wdrożyć wersję recenzowania aplikacji w klastrze, umożliwiając autorowi oraz innym członkom zespołu przeglądanie zmian w kontekście całej aplikacji. Korzystając z możliwości routingu Azure Dev Spaces, ta wersja przeglądu aplikacji zostanie również wdrożona w klastrze bez wpływu na inne miejsca deweloperskie. Wszystkie te możliwości mogą bezpiecznie zatwierdzać i scalać żądania ściągnięcia. Aby zapoznać się z przykładem akcji usługi GitHub i Azure Dev Spaces, zobacz [Akcje github & usłudze Azure Kubernetes][pr-flow].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć łączenie lokalnego komputera deweloperskiego z klastrem AKS, zobacz [Łączenie komputera deweloperskiego z klastrem AKS][connect].


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development