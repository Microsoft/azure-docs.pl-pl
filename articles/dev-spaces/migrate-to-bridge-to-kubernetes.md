---
title: Migrowanie do rozszerzenia Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Opisuje proces migracji z Azure Dev Spaces do Bridge to Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery, Bridge to Kubernetes
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499295"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrowanie do rozszerzenia Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces zostanie wycofany 31 października 2023 r. Klienci powinni przejść do korzystania Bridge to Kubernetes, czyli narzędzia dewelopera klienta.
>
> Celem tej Azure Dev Spaces było ułatwianie użytkownikom tworzenia aplikacji na kubernetes. Znaczącym kompromisem w podejściu Azure Dev Spaces było obciążenie użytkowników zrozumieniem konfiguracji platformy Docker i platformy Kubernetes, a także pojęć związanych z wdrażaniem rozwiązania Kubernetes. Z czasem stało się również jasne, że podejście Azure Dev Spaces nie zmniejszało szybkości tworzenia pętli wewnętrznej na kubernetes. Bridge to Kubernetes skutecznie zmniejsza szybkość tworzenia pętli wewnętrznej i pozwala uniknąć niepotrzebnego obciążenia użytkowników.
>
> Podstawowa misja pozostaje niezmieniona: Twórz najlepsze rozwiązania do tworzenia, testowania i debugowania kodu mikrousług w kontekście większej aplikacji.

Bridge to Kubernetes zapewnia lżejszą alternatywę dla wielu scenariuszy programistyki, które działają z Azure Dev Spaces. Bridge to Kubernetes jest środowisko tylko po stronie klienta przy [][vs]użyciu rozszerzeń w Visual Studio   i [Visual Studio Code.][vsc]  

Bridge to Kubernetes środowisko programowe, zezwalając ustanowionej aplikacji Kubernetes na uwzględnianie usługi działającej na lokalnej stacji roboczej dewelopera. W przeciwieństwie do usługi Dev Spaces usługa Bridge to Kubernetes zmniejsza złożone pętlę wewnętrzną przez krokowe tworzenie konfiguracji platformy Docker i kubernetes, dzięki czemu deweloperzy mogą skupić się wyłącznie na logice biznesowej kodu mikrousług.

Bridge to Kubernetes ułatwia iterowanie kodu uruchomionego na komputerze dewelopera, jednocześnie zużywając zależności i istniejącą konfigurację ze środowiska Kubernetes. Z kolei Azure Dev Spaces wdroży mikrousługę w środowisku Kubernetes, zanim będzie można zdalnie debugować usługę i iterować kod.

Ten artykuł zawiera porównanie Azure Dev Spaces i Bridge to Kubernetes oraz instrukcje dotyczące migracji z Azure Dev Spaces do Bridge to Kubernetes.

## <a name="development-approaches"></a>Podejścia programowe

![Podejścia programowe](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces platformie Kubernetes deweloperzy pracują z kodem uruchomionym bezpośrednio w klastrze usługi AKS, unikając konieczności tworzenia środowiska lokalnego, które nie przypominało wdrożonego środowiska. Takie podejście poprawiło niektóre aspekty edukacji, ale wprowadziło również wymaganie wstępne dotyczące uczenia się i utrzymywania dodatkowych pojęć, takich jak platformy Docker, Kubernetes i Helm, zanim będzie można rozpocząć korzystanie z Azure Dev Spaces.

Bridge to Kubernetes umożliwia deweloperom pracę bezpośrednio na swoich komputerach deweloperskich podczas interakcji z resztą klastra. Takie podejście wykorzystuje znajomość i szybkość uruchamiania kodu bezpośrednio na komputerach dewelopera przy jednoczesnym udostępnianiu zależności i środowiska udostępnianego przez klaster. Takie podejście wykorzystuje również wierność i skalowanie, które pochodzą z działania na kubernetes.

## <a name="feature-comparison"></a>Porównanie funkcji

Azure Dev Spaces i Bridge to Kubernetes podobne funkcje, różnią się również w kilku obszarach:

| Wymaganie  | Azure Dev Spaces  | Mostek na platformę Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | W 15 regionach świadczenia usługi Azure | Dowolny region usługi AKS    |
| **Bezpieczeństwo** |
| Wymagany dostęp do zabezpieczeń w klastrze  | Współautor klastra usługi AKS  | Kubernetes RBAC — aktualizacja wdrożenia   |
| Wymagany dostęp do zabezpieczeń na komputerze dewelopera  | Nie dotyczy  | Administrator lokalny/sudo   |
| **Użyteczność** |
| Niezależne od artefaktów platformy Kubernetes i Platformy Docker  | Nie  | Tak   |
| Automatyczne wycofywanie zmian, po debugowaniu  | Nie  | Tak   |
| **Obsługiwane narzędzia klienta** |
| Działa z Visual Studio 2019 r.  | Tak  | Tak   |
| Współpracuje z Visual Studio Code  | Tak  | Tak   |
| Działa z interfejsem wiersza polecenia  | Tak  | Nie   |
| **Zgodność systemu operacyjnego** |
| Działa na Windows 10  | Tak  | Tak  |
| Działa w systemie Linux  | Tak  | Tak  |
| Działa w systemie macOS  | Tak  | Tak  |
| **Możliwości** |
| Izolacja deweloperów lub tworzenie zespołu  | Tak  | Tak  |
| Selektywne zastępowanie zmiennych środowiskowych  | Nie  | Tak  |
| Tworzenie pliku Dockerfile i wykresu Helm  | Tak  | Nie  |
| Trwałe wdrażanie kodu na kubernetes  | Tak  | Nie  |
| Debugowanie zdalne w zasobniku kubernetes  | Tak  | Nie  |
| Debugowanie lokalne połączone z kubernetes  | Nie  | Tak  |
| Debugowanie wielu usług w tym samym czasie na tej samej stacji roboczej  | Tak  | Tak  |

## <a name="kubernetes-inner-loop-development"></a>Tworzenie pętli wewnętrznej kubernetes

Największą różnicą między Azure Dev Spaces a Bridge to Kubernetes jest miejsce, w którym działa kod. Azure Dev Spaces pomaga tworzyć i debugować kod mikrousługi, ale wymaga uruchomienia tego kodu w klastrze. Bridge to Kubernetes umożliwia opracowywanie i debugowanie kodu mikrousługi bezpośrednio na komputerze dewelopera, nadal w kontekście większej aplikacji działającej na kubernetes. Bridge to Kubernetes rozszerza obwód klastra Kubernetes i umożliwia lokalnym procesom dziedziczenie konfiguracji z usługi Kubernetes.

![Opracowywanie pętli wewnętrznej](media/migrate-to-btk/btk-graphic-non-isolated.gif)

W przypadku Bridge to Kubernetes jest nawiązane połączenie sieciowe między komputerem dewelopera a klastrem.W okresie istnienia tego połączenia do klastra jest dodawany serwer proxy, a nie wdrożenie kubernetes, które przekierowuje żądania do usługi na komputer dewelopera. Po rozłączeniu wdrożenie aplikacji zostanie przywrócone do korzystania z oryginalnej wersji wdrożenia uruchomionego w klastrze. To podejście różni się od Azure Dev Spaces, w którym kod jest synchronizowany z klastrem, sbudowaną, a następnie uruchamianą. Azure Dev Spaces również nie wycofuje kodu.

Bridge to Kubernetes możliwość pracy z aplikacjami uruchomionymi na kubernetes niezależnie od ich metody wdrażania. Jeśli używasz funkcji ci/cd do kompilowania i uruchamiania aplikacji, niezależnie od tego, czy używasz ustalonych narzędzi, czy skryptów niestandardowych, możesz nadal używać Bridge to Kubernetes do tworzenia i debugowania kodu.

> [!TIP]
> Rozszerzenie [Microsoft Kubernetes umożliwia][kubernetes-extension] szybkie opracowywanie manifestów platformy Kubernetes za pomocą funkcji Intellisense i pomaga tworzyć szkielety wykresów programu Helm.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Przejście do Bridge to Kubernetes z Azure Dev Spaces

1. Jeśli używasz programu Visual Studio, zaktualizuj swoje Visual Studio IDE do wersji 16.7 lub większej i zainstaluj rozszerzenie Bridge to Kubernetes z Visual Studio Marketplace [.][vs-marketplace] Jeśli używasz usługi Visual Studio Code, zainstaluj [rozszerzenie Bridge to Kubernetes][vsc-marketplace].
1. Wyłącz kontroler Azure Dev Spaces przy użyciu interfejsu Azure Portal lub [Azure Dev Spaces CLI][azds-delete].
1. Użyj [Azure Cloud Shell](https://shell.azure.com). Lub w systemie Mac, Linux lub Windows z zainstalowaną powłoką Bash otwórz wiersz powłoki Bash. Upewnij się, że w środowisku wiersza polecenia są dostępne następujące narzędzia: interfejs wiersza polecenia platformy Azure, docker, kubectl, curl, tar i gunzip.
1. Utwórz rejestr kontenerów lub użyj istniejącego rejestru. Rejestr kontenerów można utworzyć na platformie Azure przy użyciu usługi [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) lub Docker Hub [.](https://hub.docker.com/) W przypadku Azure Cloud Shell do hostowania obrazów platformy Docker Azure Container Registry tylko te, które są dostępne.
1. Uruchom skrypt migracji, aby przekonwertować zasoby Azure Dev Spaces na Bridge to Kubernetes zasobów. Skrypt tworzy nowy obraz zgodny z Bridge to Kubernetes, przesyła go do wyznaczonego rejestru, a następnie aktualizuje klaster przy użyciu programu [Helm.](https://helm.sh) Należy podać grupę zasobów, nazwę klastra usługi AKS i rejestr kontenerów. Istnieją inne opcje wiersza polecenia, jak pokazano poniżej:

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

1. Ręcznie przemigruj wszelkie dostosowania, takie jak ustawienia zmiennych środowiskowych, w *pliku azds.yaml* do pliku *values.yml* projektu.
1. (opcjonalnie) Usuń `azds.yaml` plik z projektu.
1. Skonfiguruj Bridge to Kubernetes dla wdrożonej aplikacji. Aby uzyskać więcej informacji na temat używania Bridge to Kubernetes w programie Visual Studio, zobacz Use Bridge to Kubernetes in Visual Studio (Używanie Bridge to Kubernetes [w programie Visual Studio][use-btk-vs]). Aby uzyskać VS Code, zobacz [Use Bridge to Kubernetes in VS Code][use-btk-vsc](Używanie Bridge to Kubernetes w VS Code ).
1. Rozpocznij debugowanie przy użyciu nowo utworzonego Bridge to Kubernetes debugowania/uruchamiania.
1. W razie potrzeby możesz ponownie uruchomić skrypt w celu ponownego uruchomienia w klastrze.

## <a name="team-development-in-a-shared-cluster"></a>Tworzenie zespołu w klastrze udostępnionym

Routingu specyficznego dla deweloperów można również używać z Bridge to Kubernetes. Scenariusz Azure Dev Spaces zespołowego używa wielu przestrzeni nazw Kubernetes w celu odizolowania usługi od reszty aplikacji przy użyciu koncepcji przestrzeni nazw nadrzędnej i podrzędnej. Bridge to Kubernetes oferuje tę samą możliwość, ale o lepszej wydajności i w ramach tej samej przestrzeni nazw aplikacji.

Zarówno Bridge to Kubernetes, jak i Azure Dev Spaces wymagają, aby nagłówki HTTP są obecne i propagowane w całej aplikacji. Jeśli aplikacja została już skonfigurowana do obsługi propagacji nagłówka dla Azure Dev Spaces, nagłówek musi zostać zaktualizowany. Aby przejść do Bridge to Kubernetes z Azure Dev Spaces, zaktualizuj skonfigurowany nagłówek z *azds-route-as na* *kubernetes-route-as.*

## <a name="evaluate-bridge-to-kubernetes"></a>Oceń Bridge to Kubernetes

Jeśli chcesz poeksperymentować z Bridge to Kubernetes przed wyłączeniem Azure Dev Spaces w klastrze, najprostszym sposobem jest użycie nowego klastra. Jeśli spróbujesz równocześnie Azure Dev Spaces i Bridge to Kubernetes w tym samym klastrze, podczas korzystania z funkcji routingu w obu tych klastrach zostaną rozwiązane problemy.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Ocenianie Visual Studio przy użyciu Bridge to Kubernetes

1. Zaktualizuj swoje Visual Studio IDE do wersji 16.7 lub większej i zainstaluj rozszerzenie Bridge to Kubernetes z [Visual Studio Marketplace][vs-marketplace].
1. Utwórz nowy klaster usługi AKS i wd wdrażaj aplikację. Możesz również użyć [przykładowej aplikacji][btk-sample-app].
1. Skonfiguruj Bridge to Kubernetes we wdrożonej aplikacji. Aby uzyskać więcej informacji na temat używania Bridge to Kubernetes w programie Visual Studio, [zobacz Używanie Bridge to Kubernetes][use-btk-vs].
1. Rozpocznij debugowanie w Visual Studio przy użyciu nowo utworzonego Bridge to Kubernetes profilu debugowania.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Ocenianie Visual Studio Code za pomocą Bridge to Kubernetes

1. Zainstaluj [rozszerzenie Bridge to Kubernetes .][vsc-marketplace]
1. Utwórz nowy klaster usługi AKS i wd wdrażaj aplikację. Możesz również użyć [przykładowej aplikacji][btk-sample-app].
1. Skonfiguruj Bridge to Kubernetes we wdrożonej aplikacji. Aby uzyskać więcej informacji na temat używania Bridge to Kubernetes w programie Visual Studio Code, zobacz [Używanie Bridge to Kubernetes][use-btk-vsc].
1. Rozpocznij debugowanie w Visual Studio przy użyciu nowo utworzonego Bridge to Kubernetes profilu uruchamiania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak Bridge to Kubernetes działa.

> [!div class="nextstepaction"]
> [Jak działa Mostek na platformę Kubernetes][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
