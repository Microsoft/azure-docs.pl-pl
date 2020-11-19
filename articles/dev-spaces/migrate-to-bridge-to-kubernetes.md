---
title: Migrowanie do rozszerzenia Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Opisuje proces migracji z Azure Dev Spaces do mostka do Kubernetes
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers to Kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888525"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrowanie do rozszerzenia Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces zostanie wycofana 31 października 2023. Klienci powinni przejść do korzystania z usługi Bridge do Kubernetes, narzędzia dla deweloperów klienta.
>
> Celem Azure Dev Spacesu jest to, że użytkownicy będą mogli rozwijać Kubernetes. Istotnym kompromisem w podejściu Azure Dev Spaces było nałożenie dodatkowego obciążenia dla użytkowników w celu zrozumienia konfiguracji platformy Docker i Kubernetes, a także koncepcji wdrożenia Kubernetes. Z upływem czasu nastąpiło również jasne, że podejście Azure Dev Spaces nie skutecznie obniżyć szybkości tworzenia pętli wewnętrznej w Kubernetes. Mostek do Kubernetes skutecznie zmniejsza szybkość tworzenia pętli wewnętrznej i unika niepotrzebnych obciążeń dla użytkowników.
>
> Główna misja pozostaje niezmieniona: Utwórz najlepsze środowisko do tworzenia, testowania i debugowania kodu mikrousług w kontekście większej aplikacji.

Mostek do Kubernetes zapewnia jaśniejszą wagę alternatywy dla wielu scenariuszy programistycznych, które współpracują z Azure Dev Spaces. Mostek do Kubernetes to środowisko tylko po stronie klienta przy użyciu rozszerzeń w programie [Visual Studio][vs]   i [Visual Studio Code][vsc].  

Usługa Bridge do Kubernetes ułatwia programowanie dzięki umożliwieniu ustanowionej aplikacji Kubernetes w celu uwzględnienia usługi uruchomionej na lokalnej stacji roboczej deweloperskiej. W przeciwieństwie do funkcji Spaces, funkcja Bridge do Kubernetes zmniejsza złożoność pętli wewnętrznej przez podwyższenie poziomu potrzeby tworzenia konfiguracji platformy Docker i Kubernetes, dzięki czemu deweloperzy mogą skupić się wyłącznie na logice biznesowej ich kodu mikrousług.

Mostek do Kubernetes pomaga w iteracji kodu uruchomionego na komputerze deweloperskim, przy jednoczesnym zużyciu zależności i istniejącej konfiguracji ze środowiska Kubernetes. W przeciwieństwie do programu, Azure Dev Spaces wdrożyć mikrousługę w środowisku Kubernetes, zanim będzie możliwe zdalne debugowanie usługi i wykonanie iteracji w kodzie.

Ten artykuł zawiera porównanie między Azure Dev Spaces i mostkiem a Kubernetes oraz instrukcjami migracji z Azure Dev Spaces do programu Bridge do Kubernetes.

## <a name="development-approaches"></a>Podejścia programistyczne

![Podejścia programistyczne](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces poKubernetes się, że deweloperzy pracują z kodem uruchomionym bezpośrednio w klastrze AKS, unikając potrzeby środowiska lokalnego, które nie jest podobne do wdrożonego środowiska. Takie podejście poprawiło pewne aspekty rozwoju, ale wprowadza również wstępnie wymaganie uczenia i utrzymywania dodatkowych koncepcji, takich jak Docker, Kubernetes i Helm, zanim zaczniesz korzystać z Azure Dev Spaces.

Mostek do Kubernetes umożliwia deweloperom pracę bezpośrednio na swoich komputerach deweloperskich, jednocześnie współpracując z pozostałą częścią klastra. Takie podejście wykorzystuje znajomość i szybkość uruchamiania kodu bezpośrednio na swoich komputerach deweloperskich, jednocześnie udostępniając zależności i środowisko udostępniane przez ich klaster. Takie podejście wykorzystuje również zalety i skalowanie, które pochodzą z uruchamiania w Kubernetes.

## <a name="feature-comparison"></a>Porównanie funkcji

Azure Dev Spaces i mostek do Kubernetes mają podobne funkcje, ale również różnią się w kilku obszarach:

| Wymaganie  | Azure Dev Spaces  | Mostek na platformę Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | W 15 regionach świadczenia usługi Azure | Dowolny region usługi AKS    |
| **Zabezpieczenia** |
| Wymagany jest dostęp do zabezpieczeń w klastrze  | Współautor klastra AKS  | Kubernetes RBAC — aktualizacja wdrożenia   |
| Wymagany na komputerze deweloperskim dostęp do zabezpieczeń  | Nie dotyczy  | Administrator lokalny/sudo   |
| **Wykorzystania** |
| Niezależna od Kubernetes i artefaktów platformy Docker  | Nie  | Tak   |
| Automatyczne wycofywanie zmian, po debugowaniu  | Nie  | Tak   |
| **Obsługiwane narzędzia klienta** |
| Współpracuje z programem Visual Studio 2019  | Tak  | Tak   |
| Działa z Visual Studio Code  | Tak  | Tak   |
| Praca z interfejsem wiersza polecenia  | Tak  | Nie   |
| **Zgodność systemu operacyjnego** |
| Działa w systemie Windows 10  | Tak  | Tak  |
| Działa w systemie Linux  | Tak  | Tak  |
| Działa na macOS  | Tak  | Tak  |
| **Możliwości** |
| Izolacja deweloperów lub programowanie zespołowe  | Tak  | Tak  |
| Selektywne zastępowanie zmiennych środowiskowych  | Nie  | Tak  |
| Tworzenie wykresu pliku dockerfile i Helm  | Tak  | Nie  |
| Trwałe Wdrażanie kodu do Kubernetes  | Tak  | Nie  |
| Zdalne debugowanie w Kubernetes pod  | Tak  | Nie  |
| Debugowanie lokalne połączone z Kubernetes  | Nie  | Tak  |
| Debugowanie wielu usług w tym samym czasie na tej samej stacji roboczej  | Tak  | Tak  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes — programowanie w pętli wewnętrznej

Największą różnicą między Azure Dev Spaces i Bridge Kubernetes jest miejsce, w którym wykonywany jest kod. Azure Dev Spaces ułatwia opracowywanie i debugowanie kodu mikrousług, ale wymaga uruchomienia tego kodu w klastrze. Usługa Bridge do Kubernetes umożliwia tworzenie i debugowanie kodu mikrousług bezpośrednio na komputerze deweloperskim, a mimo to w kontekście większej aplikacji działającej w Kubernetes. Mostek do Kubernetes rozszerza obwód klastra Kubernetes i umożliwia procesom lokalnym dziedziczenie konfiguracji z Kubernetes.

![Programowanie w pętli wewnętrznej](media/migrate-to-btk/btk-graphic-non-isolated.gif)

W przypadku korzystania z mostka Kubernetes połączenie sieciowe między komputerem deweloperskim a klastrem jest nawiązywane.W przypadku okresu istnienia tego połączenia do klastra zostanie dodany serwer proxy zamiast wdrożenia Kubernetes, który przekierowuje żądania do usługi na komputer deweloperski. Po rozłączeniu wdrożenie aplikacji powróci do korzystania z oryginalnej wersji wdrożenia uruchomionej w klastrze. Takie podejście różni się od sposobu działania Azure Dev Spaces, w którym kod jest synchronizowany z klastrem, skompilowany, a następnie uruchamiany. Azure Dev Spaces również nie wycofuje kodu.

Mostek do Kubernetes zapewnia elastyczność pracy z aplikacjami działającymi w Kubernetes niezależnie od ich metody wdrażania. Jeśli używasz ciągłej integracji/ciągłego tworzenia i uruchamiania aplikacji, niezależnie od tego, czy używasz określonych narzędzi czy skryptów niestandardowych, możesz nadal używać programu Bridge do Kubernetes do tworzenia i debugowania kodu.

> [!TIP]
>  [Rozszerzenie Microsoft Kubernetes][kubernetes-extension] umożliwia szybkie tworzenie manifestów Kubernetes za pomocą technologii IntelliSense i pomaga w tworzeniu szkieletów Helm wykresów.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Przejście do mostka do Kubernetes z Azure Dev Spaces

1. Jeśli używasz programu Visual Studio, zaktualizuj środowisko IDE programu Visual Studio do wersji 16,7 lub nowszej i zainstaluj mostek do rozszerzenia Kubernetes z [Visual Studio Marketplace][vs-marketplace]. Jeśli używasz Visual Studio Code, zainstaluj [mostek do rozszerzenia Kubernetes][vsc-marketplace].
1. Wyłącz kontroler Azure Dev Spaces przy użyciu Azure Portal lub [interfejsu wiersza polecenia Azure dev Spaces][azds-delete].
1. Użyj [Azure Cloud Shell](https://shell.azure.com). Lub na komputerach Mac, Linux lub Windows z zainstalowanym programem bash Otwórz wiersz polecenia powłoki bash. Upewnij się, że następujące narzędzia są dostępne w środowisku wiersza polecenia: Azure CLI, Docker, polecenia kubectl, zwinięcie, tar i gunzip.
1. Utwórz rejestr kontenerów lub Użyj istniejącego. Rejestr kontenerów można utworzyć na platformie Azure przy użyciu [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) lub przy użyciu narzędzia [Docker Hub](https://hub.docker.com/). W przypadku korzystania z Azure Cloud Shell tylko Azure Container Registry jest dostępny do hostowania obrazów platformy Docker.
1. Uruchom skrypt migracji w celu przekonwertowania Azure Dev Spaces zasobów na mostek do zasobów Kubernetes. Skrypt kompiluje nowy obraz zgodny z mostkiem Kubernetes, przekazuje go do wyoznaczonego rejestru, a następnie używa [Helm](https://helm.sh) do aktualizowania klastra przy użyciu obrazu. Należy podać grupę zasobów, nazwę klastra AKS oraz rejestr kontenerów. Dostępne są inne opcje wiersza polecenia, jak pokazano poniżej:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   Skrypt obsługuje następujące flagi:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Ręcznie Przeprowadź migrację dowolnych dostosowań, takich jak ustawienia zmiennych środowiskowych, w *azds. YAML* do pliku *wartości. yml* projektu.
1. obowiązkowe Usuń `azds.yaml` plik z projektu.
1. Skonfiguruj mostek do Kubernetes dla wdrożonej aplikacji. Aby uzyskać więcej informacji o korzystaniu z usługi Bridge do Kubernetes w programie Visual Studio, zobacz [use Bridge to Kubernetes in Visual Studio][use-btk-vs]. Aby uzyskać VS Code, zobacz [używanie mostka do Kubernetes w vs Code][use-btk-vsc].
1. Rozpocznij debugowanie przy użyciu nowo utworzonego mostka do Kubernetesego profilu debugowania/uruchamiania.
1. Skrypt można uruchomić ponownie w razie potrzeby w celu ponownego wdrożenia w klastrze.

## <a name="team-development-in-a-shared-cluster"></a>Programowanie zespołowe w klastrze udostępnionym

Można również użyć routingu specyficznego dla deweloperów z mostkiem do Kubernetes. Scenariusz opracowywania zespołu Azure Dev Spaces używa wielu przestrzeni nazw Kubernetes do izolowania usługi od reszty aplikacji przy użyciu koncepcji nadrzędnych i podrzędnych przestrzeni nazw. Mostek do Kubernetes oferuje tę samą funkcję, ale z ulepszonymi charakterystykami wydajności i w obrębie tej samej przestrzeni nazw aplikacji.

Oba mostki do Kubernetes i Azure Dev Spaces wymagają, aby nagłówki HTTP były obecne i propagowane w całej aplikacji. Jeśli aplikacja została już skonfigurowana do obsługi propagacji nagłówków dla Azure Dev Spaces, należy zaktualizować nagłówek. Aby przejść do programu Bridge do Kubernetes z Azure Dev Spaces, zaktualizuj skonfigurowany nagłówek z *azds-Route-as* do *Kubernetes-Route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Oceń mostek do Kubernetes

Jeśli chcesz eksperymentować z usługą Kubernetes przed wyłączeniem Azure Dev Spaces w klastrze, najprostszym sposobem jest użycie nowego klastra. Jeśli spróbujesz używać Azure Dev Spaces i mostka do Kubernetes w tym samym czasie w tym samym klastrze, wystąpią problemy podczas korzystania z funkcji routingu na obu.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Użyj programu Visual Studio, aby oszacować mostek do Kubernetes

1. Zaktualizuj środowisko IDE programu Visual Studio do wersji 16,7 lub nowszej i zainstaluj mostek do rozszerzenia Kubernetes z [Visual Studio Marketplace][vs-marketplace].
1. Utwórz nowy klaster AKS i Wdróż aplikację. Możesz również użyć [przykładowej aplikacji][btk-sample-app].
1. Skonfiguruj mostek do Kubernetes dla wdrożonej aplikacji. Aby uzyskać więcej informacji o korzystaniu z usługi Bridge do Kubernetes w programie Visual Studio, zobacz [use Bridge to Kubernetes][use-btk-vs].
1. Rozpocznij debugowanie w programie Visual Studio przy użyciu nowo utworzonego mostka do Kubernetes debugowania profilu.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Użyj Visual Studio Code, aby oszacować mostek do Kubernetes

1. Zainstaluj [mostek do rozszerzenia Kubernetes][vsc-marketplace].
1. Utwórz nowy klaster AKS i Wdróż aplikację. Możesz również użyć [przykładowej aplikacji][btk-sample-app].
1. Skonfiguruj mostek do Kubernetes dla wdrożonej aplikacji. Aby uzyskać więcej informacji na temat używania mostka do Kubernetes w Visual Studio Code, zobacz [use Bridge to Kubernetes][use-btk-vsc].
1. Rozpocznij debugowanie w programie Visual Studio przy użyciu nowo utworzonego mostka do Kubernetes uruchamiania profilu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa mostek Kubernetes.

> [!div class="nextstepaction"]
> [Jak działa Mostek na platformę Kubernetes][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
