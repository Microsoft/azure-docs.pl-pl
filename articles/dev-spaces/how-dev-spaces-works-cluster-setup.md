---
title: Jak skonfigurować klaster dla Azure Dev Spaces działa
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Opisuje sposób konfigurowania klastra usługi Azure Kubernetes dla Azure Dev Spaces Works
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972683"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Jak skonfigurować klaster dla Azure Dev Spaces działa

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces zapewnia wiele sposobów na szybkie Iterowanie i debugowanie aplikacji Kubernetes oraz współpracę z zespołem w klastrze usługi Azure Kubernetes Service (AKS). Jednym ze sposobów jest włączenie Azure Dev Spaces w klastrze AKS, dzięki czemu można [uruchamiać usługi bezpośrednio w klastrze][how-it-works-up] i korzystać z [dodatkowych możliwości sieciowych i routingu][how-it-works-routing]. W tym artykule opisano, co się dzieje podczas przygotowywania klastra i włączania Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Przygotowywanie klastra AKS

Aby przygotować klaster AKS na potrzeby obszarów programistycznych, należy sprawdzić, czy klaster AKS znajduje się w regionie [obsługiwanym przez Azure dev Spaces][supported-regions] i korzysta z Kubernetes 1.10.3 lub nowszego. Azure Dev Spaces można włączyć z poziomu interfejsu wiersza polecenia platformy Azure, uruchamiając `az aks use-dev-spaces` .

Po włączeniu Azure Dev Spaces w klastrze AKS zainstaluje on kontroler dla klastra. Kontroler znajduje się poza klastrem AKS. Umożliwia ona zachowanie komunikacji między narzędziami po stronie klienta a klastrem AKS. Po jego włączeniu można korzystać z kontrolera przy użyciu narzędzi po stronie klienta.

Kontroler wykonuje następujące akcje:

* Zarządza tworzeniem i wyborem obszaru dev.
* Instaluje wykres Helm aplikacji i tworzy obiekty Kubernetes.
* Kompiluje obraz kontenera aplikacji.
* Wdraża aplikację w usłudze AKS.
* Wykonuje przyrostowe kompilacje i ponowne uruchomienie po zmianie kodu źródłowego.
* Zarządza dziennikami i śladami HTTP.
* Przesyła strumieniowo stdout i stderr do narzędzi po stronie klienta.
* Konfiguruje Routing dla aplikacji w przestrzeni, a także między spacjami nadrzędnymi i podrzędnymi.

Kontroler jest oddzielnym zasobem platformy Azure poza klastrem i wykonuje następujące czynności w przypadku zasobów w klastrze:

* Tworzy lub wyznacza przestrzeń nazw Kubernetes, która ma być używana jako przestrzeń dev.
* Usuwa wszystkie Kubernetes przestrzeń nazw o nazwie *azds*, jeśli istnieje, i tworzy nową.
* Wdraża konfigurację elementu webhook Kubernetes.
* Wdraża serwer przyjęcia elementu webhook.

Używa tej samej nazwy głównej usługi, która jest używany przez klaster AKS do wykonywania wywołań usługi do innych składników Azure Dev Spaces.

![Azure Dev Spaces Przygotuj klaster](media/how-dev-spaces-works/prepare-cluster.svg)

Aby można było użyć Azure Dev Spaces, musi istnieć co najmniej jeden obszar deweloperski. Azure Dev Spaces używa Kubernetes przestrzenie nazw w klastrze AKS dla miejsc deweloperskich. Gdy kontroler jest instalowany, zostanie wyświetlony komunikat z prośbą o utworzenie nowej przestrzeni nazw Kubernetes lub wybranie istniejącej przestrzeni nazw, która ma być używana jako pierwsze miejsce dev. Domyślnie kontroler oferuje uaktualnienie istniejącej *domyślnej* przestrzeni nazw Kubernetes do pierwszego obszaru dev.

Gdy przestrzeń nazw jest oznaczona jako przestrzeń dev, kontroler dodaje etykietę *azds.IO/Space=true* do tej przestrzeni nazw, aby zidentyfikować ją jako przestrzeń dev. Początkowe miejsce tworzenia lub wyznaczania jest wybierane domyślnie po przygotowaniu klastra. Gdy jest zaznaczone miejsce, jest używane przez Azure Dev Spaces do tworzenia nowych obciążeń.

Korzystając z narzędzi po stronie klienta, można tworzyć nowe miejsca deweloperskie i usuwać istniejące spacje. Ze względu na ograniczenie w Kubernetes, nie można usunąć *domyślnego* obszaru dev. Kontroler usuwa również wszystkie istniejące przestrzenie nazw Kubernetes o nazwie *azds* , aby uniknąć konfliktów z `azds` poleceniem używanym przez narzędzia po stronie klienta.

Serwer przyjęcia elementu webhook Kubernetes służy do iniekcji z trzech kontenerów podczas wdrażania Instrumentacji: kontenera devspaces-proxy, kontenera devspaces-proxy-init i kontenera devspaces-Build. **Wszystkie trzy z tych kontenerów działają z dostępem do katalogu głównego w klastrze AKS.** Używają one również tej samej nazwy głównej usługi, która jest używana przez klaster AKS do wykonywania wywołań usługi do innych składników Azure Dev Spaces.

![Azure Dev Spaces Kubernetes serwer przyjęcia elementu webhook](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Kontener devspaces-proxy jest kontenerem przyczepki, który obsługuje cały ruch TCP do i z kontenera aplikacji i ułatwia Routing. Kontener devspaces-proxy kieruje komunikaty HTTP, jeśli są używane określone spacje. Może na przykład ułatwić kierowanie komunikatów HTTP między aplikacjami w przestrzeniach nadrzędnych i podrzędnych. Cały ruch inny niż HTTP przechodzi przez devspaces-proxy unmodifiedd. Kontener devspaces-proxy rejestruje także wszystkie przychodzące i wychodzące komunikaty HTTP i wysyła je do narzędzi po stronie klienta jako śladów. Te ślady mogą być następnie przeglądane przez dewelopera w celu sprawdzenia zachowania aplikacji.

Kontener devspaces-proxy-init jest [kontenerem init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) , który dodaje dodatkowe reguły routingu na podstawie hierarchii Space do kontenera aplikacji. Dodaje reguły routingu, aktualizując plik */etc/resolv.conf* kontenera aplikacji i konfigurację dołączenie iptables przed rozpoczęciem. Aktualizacje */etc/resolv.conf* umożliwiają rozpoznawanie nazw DNS usług w przestrzeniach nadrzędnych. Aktualizacje konfiguracji dołączenie iptables zapewniają, że cały ruch TCP do i z kontenera aplikacji jest kierowany za pośrednictwem devspaces-proxy. Wszystkie aktualizacje z devspaces-proxy-init są wykonywane oprócz reguł, które Kubernetes dodaje.

Kontener devspaces-Build jest kontenerem init i ma zainstalowany kod źródłowy projektu i gniazdo Docker. Kod źródłowy projektu i dostęp do platformy Docker umożliwiają skompilowanie kontenera aplikacji bezpośrednio przez.

> [!NOTE]
> Azure Dev Spaces używa tego samego węzła do kompilowania kontenera aplikacji i uruchamiania go. W związku z tym Azure Dev Spaces nie potrzebuje zewnętrznego rejestru kontenerów do kompilowania i uruchamiania aplikacji.

Serwer przyjmowania elementu webhook Kubernetes nasłuchuje dla każdego nowego pod, który został utworzony w klastrze AKS. Jeśli ten element pod zostanie wdrożony w dowolnym obszarze nazw z etykietą *azds.IO/Space=true* , zostanie on dodany do tych dodatkowych kontenerów. Kontener devspaces-Build jest wstrzykiwany tylko wtedy, gdy kontener aplikacji jest uruchamiany przy użyciu narzędzi po stronie klienta.

Po przygotowaniu klastra AKS można użyć narzędzi po stronie klienta do przygotowania i uruchomienia kodu w obszarze dev.

## <a name="client-side-tooling"></a>Narzędzia po stronie klienta

Narzędzia po stronie klienta umożliwiają użytkownikowi:
* Wygeneruj wykres pliku dockerfile, Helm, a Azure Dev Spaces plik konfiguracyjny dla aplikacji.
* Utwórz nadrzędne i podrzędne miejsca deweloperskie.
* Poinformuj kontroler, aby skompilować i uruchomić aplikację.

Gdy aplikacja jest uruchomiona, narzędzia po stronie klienta:
* Odbiera i wyświetla stdout i stderr z aplikacji działającej w AKS.
* Używa [portu do przodu](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) w celu zezwalania na dostęp do aplikacji sieci Web za pomocą protokołu http: \/ /localhost.
* Dołącza debuger do uruchomionej aplikacji w AKS.
* Synchronizuje kod źródłowy do obszaru deweloperskiego, gdy zostanie wykryta zmiana dla kompilacji przyrostowych, co pozwala na szybką iterację.
* Umożliwia połączenie komputera dewelopera bezpośrednio z klastrem AKS.

Narzędzia po stronie klienta można użyć z wiersza polecenia w ramach `azds` polecenia. Możesz również użyć narzędzi po stronie klienta w programie:

* Visual Studio Code przy użyciu [rozszerzenia Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Program Visual Studio z obciążeniem programowania na platformie Azure.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o używaniu narzędzi po stronie klienta do przygotowywania i uruchamiania kodu w obszarze deweloperskim, zobacz [przygotowywanie projektu dla Azure dev Spaces działa][how-it-works-prep].


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service