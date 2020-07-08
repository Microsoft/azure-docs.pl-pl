---
title: Używanie procesu lokalnego w usłudze Kubernetes z programem Visual Studio Code (wersja zapoznawcza)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Dowiedz się, jak używać procesu lokalnego z usługą Kubernetes do łączenia komputera deweloperskiego z klastrem Kubernetes przy użyciu Azure Dev Spaces
keywords: Proces lokalny z Kubernetes, Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316740"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Używanie procesu lokalnego w usłudze Kubernetes z programem Visual Studio Code (wersja zapoznawcza)

Proces lokalny z programem Kubernetes umożliwia uruchamianie i debugowanie kodu na komputerze deweloperskim, przy jednoczesnym połączeniu z klastrem Kubernetes z pozostałą częścią aplikacji lub usług. Na przykład jeśli masz dużą architekturę mikrousług z wieloma zależnymi usługami i bazami danych, replikowanie tych zależności na komputerze deweloperskim może być trudne. Ponadto Kompilowanie i wdrażanie kodu w klastrze Kubernetes dla każdej zmiany kodu podczas programowania w pętli wewnętrznej może być powolne, czasochłonne i trudne do użycia z debugerem.

Proces lokalny z Kubernetes pozwala uniknąć konieczności kompilowania i wdrażania kodu w klastrze przez utworzenie połączenia bezpośrednio między komputerem deweloperskim i klastrem. Połączenie komputera deweloperskiego z klastrem podczas debugowania umożliwia szybkie testowanie i opracowywanie usługi w kontekście pełnej aplikacji bez konieczności tworzenia żadnej konfiguracji platformy Docker lub Kubernetes.

Proces lokalny z Kubernetes przekierowuje ruch między podłączonym klastrem Kubernetes i komputerem deweloperskim. To przekierowywanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze Kubernetes, aby komunikować się tak, jakby znajdowały się w tym samym klastrze Kubernetes. Proces lokalny z usługą Kubernetes umożliwia także replikować zmienne środowiskowe i zainstalowane woluminy dostępne dla zasobników w klastrze Kubernetes na komputerze deweloperskim. Zapewnianie dostępu do zmiennych środowiskowych i zainstalowanych woluminów na komputerze deweloperskim pozwala szybko korzystać z kodu bez konieczności ręcznego replikowania tych zależności.

W tym przewodniku dowiesz się, jak używać procesu lokalnego z usługą Kubernetes, aby przekierowywać ruch między klastrem Kubernetes i kodem uruchomionym na komputerze deweloperskim. Ten przewodnik zawiera również skrypt służący do wdrażania dużej przykładowej aplikacji z wieloma mikrousługami w klastrze Kubernetes.

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][preview-terms]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten przewodnik korzysta z [przykładowej aplikacji do udostępniania Azure dev Spaces roweru][bike-sharing-github] do zademonstrowania połączenia komputera deweloperskiego z klastrem Kubernetes. Jeśli masz już uruchomioną aplikację w klastrze Kubernetes, możesz wykonać poniższe kroki i użyć nazw własnych usług.

### <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli].
* [Visual Studio Code][vs-code] uruchomiony w systemie MacOS lub Windows 10.
* Rozszerzenie [Azure dev Spaces][azds-vs-code] w wersji 2.0.220200601 lub nowszej zainstalowane w Visual Studio Code.
* [Zainstalowano interfejs wiersza polecenia Azure dev Spaces][azds-cli].

## <a name="create-a-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Utwórz klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Instalowanie przykładowej aplikacji

Zainstaluj przykładową aplikację w klastrze przy użyciu dostarczonego skryptu. Ten skrypt można uruchomić na komputerze deweloperskim lub przy użyciu [Azure Cloud Shell][azure-cloud-shell].

> [!IMPORTANT]
> Aby uruchomić skrypt, musisz mieć dostęp *właściciela* lub *współautora* do klastra.

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Przejdź do przykładowej aplikacji, w której działa klaster, otwierając swój publiczny adres URL, który jest wyświetlany w danych wyjściowych skryptu instalacji.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

W powyższym przykładzie publiczny adres URL to `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Nawiązywanie połączenia z klastrem i debugowanie usługi

Na komputerze deweloperskim Pobierz i skonfiguruj interfejs wiersza polecenia Kubernetes, aby połączyć się z klastrem Kubernetes przy użyciu polecenia [AZ AKS Get-Credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Otwórz przykład *dev-Spaces/Samples/BikeSharingApp/Bikes* z [aplikacji Azure dev Spaces bike Sharing][bike-sharing-github] w Visual Studio Code. Otwórz rozszerzenie usługi Azure Kubernetes i wybierz przestrzeń nazw *dev* w klastrze *MyAKS* .

![Wybierz przestrzeń nazw](../media/local-process-kubernetes-vs-code/select-namespace.png)

Użyj `npm install` polecenia, aby zainstalować zależności aplikacji.

```console
npm install
```

Wybierz ikonę *debugowania* po lewej stronie i wybierz pozycję *proces lokalny z Kubernetes (wersja zapoznawcza)* u góry.

![Wybieranie procesu lokalnego z Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Kliknij przycisk Start obok pozycji *proces lokalny z Kubernetes (wersja zapoznawcza)*. Przy pierwszym uruchomieniu konfiguracji uruchamiania zostanie wyświetlony monit o skonfigurowanie usługi, która ma zostać zamieniona, port do przeprowadzenia z komputera deweloperskiego oraz zadanie uruchamiania do użycia.

Wybierz usługę *Bikes* .

![Wybieranie usługi](../media/local-process-kubernetes-vs-code/choose-service.png)

Cały ruch w klastrze Kubernetes jest przekierowywany dla usługi *Bikes* do wersji aplikacji działającej na komputerze deweloperskim. Proces lokalny z usługą Kubernetes kieruje również cały ruch wychodzący z aplikacji z powrotem do klastra Kubernetes.

> [!IMPORTANT]
> Można przekierować tylko usługi, które mają pojedynczy pod.

Po wybraniu usługi zostanie wyświetlony monit o wprowadzenie portu TCP dla aplikacji lokalnej. Na potrzeby tego przykładu wprowadź *3000*.

![Połącz wybierz port](../media/local-process-kubernetes-vs-code/choose-port.png)

Wybierz pozycję *Uruchom za pośrednictwem npm* jako zadanie uruchamiania.

![Połącz wybierz zadanie uruchamiania](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> Zostanie wyświetlony monit o zezwolenie *KubernetesDNSManager* na uruchomienie podniesienia uprawnień i zmodyfikowanie pliku Hosts.

Komputer deweloperski jest połączony, gdy pasek stanu zmieni kolor na pomarańczowy, a rozszerzenie dev Spaces pokazuje, że masz połączenie.

![Komputer deweloperski jest podłączony](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> W przypadku uruchomienia subesquent nie zostanie wyświetlony monit o podanie nazwy usługi, portu lub zadania uruchamiania. Te wartości są zapisywane w *. programu vscode/tasks.json*.

Po nawiązaniu połączenia z komputerem deweloperskim ruch zaczyna się przekierować do komputera deweloperskiego w przypadku zastępowanej usługi.

## <a name="set-a-break-point"></a>Ustaw punkt przerwania

Otwórz [server.js][server-js-breakpoint] i kliknij w dowolnym miejscu w wierszu 233, aby umieścić w nim kursor. Ustaw punkt przerwania, naciskając klawisz *F9* lub klikając polecenie *Uruchom* , a następnie *Przełącz punkt przerwania*.

Przejdź do przykładowej aplikacji, otwierając publiczny adres URL. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik, a następnie wybierz rower do wynajęcia. Zauważ, że obraz dla roweru nie zostanie załadowany. Wróć do Visual Studio Code i obserwuj wiersz 233. Ustawiony punkt przerwania został wstrzymany usługi w wierszu 233. Aby wznowić działanie usługi, naciśnij klawisz *F5* lub kliknij przycisk *Run (Uruchom* ), a następnie *Kontynuuj*. Wróć do przeglądarki i sprawdź, czy jest wyświetlany obraz symbolu zastępczego dla roweru.

Usuń punkt przerwania, umieszczając kursor w wierszu 233 `server.js` i naciskając klawisz *F9*.

### <a name="update-your-application"></a>Aktualizowanie aplikacji

Edytuj `server.js` , aby usunąć wiersze 234 i 235:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Sekcja powinna teraz wyglądać następująco:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Zapisz zmiany, a następnie kliknij pozycję *Uruchom* , a następnie *Uruchom ponownie debugowanie*. Po ponownym nawiązaniu połączenia Odśwież przeglądarkę i sprawdź, czy nie widzisz już obrazu zastępczego dla roweru.

Kliknij przycisk *Uruchom* , a następnie *Zatrzymaj debugowanie* , aby zatrzymać debuger.

> [!NOTE]
> Domyślnie Zatrzymywanie zadania debugowania powoduje także odłączenie komputera deweloperskiego od klastra Kubernetes. Możesz zmienić to zachowanie, wyszukując *proces lokalny za pomocą Kubernetes: Rozłącz po debugowaniu* w ustawieniach Visual Studio Code i usuwając zaznaczenie obok pola *Rozłącz automatycznie po zakończeniu debugowania.* Po zaktualizowaniu tego ustawienia komputer programistyczny pozostanie połączony, gdy zatrzymasz i zaczniesz debugowanie. Aby odłączyć komputer deweloperski od klastra, kliknij rozszerzenie Azure Dev Spaces na pasku stanu, a następnie wybierz *Odłącz bieżącą sesję*.
>
> Jeśli Visual Studio Code nagle kończy połączenie z klastrem lub kończy działanie, przekierowanie usługi może nie zostać przywrócone do oryginalnego stanu przed nawiązaniem połączenia z procesem lokalnym z Kubernetes. Aby rozwiązać ten problem, zobacz [Przewodnik rozwiązywania problemów][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Korzystanie z funkcji rejestrowania i diagnostyki

Dane wyjściowe rejestrowania są zapisywane do okna *dev Spaces* , gdy komputer deweloperski jest połączony z klastrem Kubernetes.

![Dane wyjściowe](../media/local-process-kubernetes-vs-code/output.png)

Kliknij pasek stanu Azure Dev Spaces a następnie wybierz pozycję *Pokaż informacje diagnostyczne dotyczące połączenia*. To polecenie drukuje bieżące zmienne środowiskowe i cały system DNS w danych wyjściowych rejestrowania.

![Dane wyjściowe z diagnostyką](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Ponadto można znaleźć dzienniki diagnostyczne w `Azure Dev Spaces` katalogu w [katalogu *tymczasowym* komputera deweloperskiego][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>Usuwanie przykładowej aplikacji z klastra

Użyj dostarczonego skryptu, aby usunąć przykładową aplikację z klastra.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak za pomocą akcji Azure Dev Spaces i GitHub przetestować zmiany z żądania ściągnięcia bezpośrednio w AKS przed scaleniem żądania ściągnięcia z gałęzią główną repozytorium.

> [!div class="nextstepaction"]
> [Akcje GitHub & usłudze Azure Kubernetes Service][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download