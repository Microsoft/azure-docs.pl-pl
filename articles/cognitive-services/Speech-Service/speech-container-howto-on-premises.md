---
title: Korzystanie z kontenerów usługi mowy z Kubernetes i Helm
titleSuffix: Azure Cognitive Services
description: Za pomocą Kubernetes i Helm do definiowania obrazów kontenerów zamiany mowy na tekst i zamiany tekstu na mowę utworzysz pakiet Kubernetes. Ten pakiet zostanie wdrożony w klastrze Kubernetes lokalnie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: aahi
ms.openlocfilehash: 78ac9ae4aa8611f50caa94c84d3e6c95e58fc91c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200737"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Korzystanie z kontenerów usługi mowy z Kubernetes i Helm

Jedną z opcji zarządzania kontenerami mowy w środowisku lokalnym jest użycie Kubernetes i Helm. Za pomocą Kubernetes i Helm do definiowania obrazów kontenerów zamiany mowy na tekst i zamiany tekstu na mowę utworzysz pakiet Kubernetes. Ten pakiet zostanie wdrożony w klastrze Kubernetes lokalnie. Na koniec dowiesz się, jak przetestować wdrożone usługi i różne opcje konfiguracji. Aby uzyskać więcej informacji na temat uruchamiania kontenerów platformy Docker bez aranżacji Kubernetes, zobacz [Install and run Speech Service Containers](speech-container-howto.md).

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne przed użyciem kontenerów mowy w środowisku lokalnym:

| Wymagane | Przeznaczenie |
|----------|---------|
| Konto platformy Azure | Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][free-azure-account]. |
| Dostęp Container Registry | Aby Kubernetes do ściągania obrazów platformy Docker do klastra, będzie potrzebny dostęp do rejestru kontenerów. |
| Interfejs wiersza polecenia Kubernetes | [Interfejs wiersza polecenia Kubernetes][kubernetes-cli] jest wymagany do zarządzania poświadczeniami udostępnionymi z rejestru kontenerów. Kubernetes jest również wymagany przed Helm, który jest menedżerem pakietów Kubernetes. |
| Interfejs wiersza polecenia Helm | Zainstaluj [interfejs wiersza polecenia Helm][helm-install], który służy do instalowania wykresu Helm (definicja pakietu kontenerów). |
|Zasób mowy |Aby można było korzystać z tych kontenerów, musisz mieć:<br><br>Zasób platformy Azure _mowy_ , aby uzyskać skojarzony klucz rozliczeń i identyfikator URI punktu końcowego rozliczenia. Obie wartości są dostępne na stronach przeglądów **mowy** Azure Portal i kluczy i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: klucz zasobu<br><br>**{ENDPOINT_URI}**: przykład identyfikatora URI punktu końcowego: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Zalecana konfiguracja komputera hosta

Zapoznaj się z informacjami na temat [komputera hosta kontenera usługi Speech][speech-container-host-computer] . Ten *Wykres Helm* automatycznie oblicza wymagania dotyczące procesora i pamięci w zależności od liczby dekoderów (współbieżnych żądań) określanych przez użytkownika. Ponadto dostosowuje się w zależności od tego, czy optymalizacje danych wejściowych audio/tekstowych są skonfigurowane jako `enabled` . Wykres Helm ma wartość domyślną, dwa współbieżne żądania i wyłączenie optymalizacji.

| Usługa | Procesor CPU/kontener | Pamięć/kontener |
|--|--|--|
| **Zamiana mowy na tekst** | jeden dekoder wymaga co najmniej 1 150 millicores. Jeśli `optimizedForAudioFile` jest włączona, wymagane są 1 950 millicores. (domyślnie: dwa dekodery) | Wymagane: 2 GB<br>Ograniczone: 4 GB |
| **Zamiana tekstu na mowę** | jedno współbieżne żądanie wymaga co najmniej 500 millicores. Jeśli `optimizeForTurboMode` jest włączona, wymagane są 1 000 millicores. (domyślnie: dwa współbieżne żądania) | Wymagane: 1 GB<br> Ograniczone: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Nawiązywanie połączenia z klastrem Kubernetes

Oczekuje się, że komputer hosta ma dostępny klaster Kubernetes. Zapoznaj się z tym samouczkiem dotyczącym [wdrażania klastra Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) , aby poznać koncepcję wdrażania klastra Kubernetes na komputerze hosta.

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurowanie wartości wykresu Helm na potrzeby wdrożenia

Odwiedź [centrum Helm firmy Microsoft][ms-helm-hub] , aby uzyskać wszystkie dostępne publicznie wykresy Helm oferowane przez firmę Microsoft. W centrum programu Microsoft Helm znajdziesz **Wykres lokalny Cognitive Services mowy**. Jest to wykres, który zostanie zainstalowany **w środowisku Cognitive Services Speech** , ale musimy najpierw utworzyć `config-values.yaml` plik z jawnymi konfiguracjami. Zacznijmy od dodania repozytorium Microsoft do naszego wystąpienia Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Następnie skonfigurujemy nasze wartości wykresu Helm. Skopiuj i wklej następujący YAML do pliku o nazwie `config-values.yaml` . Aby uzyskać więcej informacji na temat dostosowywania **Cognitive Services mowy lokalnego wykresu Helm**, zobacz [Dostosowywanie wykresów Helm](#customize-helm-charts). Zastąp `# {ENDPOINT_URI}` `# {API_KEY}` Komentarze i komentarz własnymi wartościami.

```yaml
# These settings are deployment specific and users can provide customizations
# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Jeśli `billing` wartości i `apikey` nie zostaną podane, usługi wygasną po 15 minutach. Podobnie weryfikacja nie powiedzie się, ponieważ usługi nie będą dostępne.

### <a name="the-kubernetes-package-helm-chart"></a>Pakiet Kubernetes (wykres Helm)

*Wykres Helm* zawiera konfigurację, którą obrazy platformy Docker pobrać z `mcr.microsoft.com` rejestru kontenerów.

> [Wykres Helm][helm-charts] to zbiór plików, które opisują powiązany zestaw zasobów Kubernetes. Pojedynczy wykres może służyć do wdrażania bardzo prostego, takiego jak Memcached pod lub złożonego, takiego jak pełny stos aplikacji sieci Web z serwerami HTTP, bazami danych, pamięciami podręcznymi i tak dalej.

Dostarczone *wykresy Helm* umożliwiają ściąganie obrazów platformy Docker usługi mowy, zamiany tekstu na mowę i usług zamiany mowy na tekst z `mcr.microsoft.com` rejestru kontenerów.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalowanie wykresu Helm w klastrze Kubernetes

Aby zainstalować *Wykres Helm* , musimy wykonać [`helm install`][helm-install-cmd] polecenie, zastępując `<config-values.yaml>` z odpowiednią ścieżką i nazwą pliku. `microsoft/cognitive-services-speech-onpremise`Wykres Helm, do którego odwołuje się poniżej, jest dostępny w [centrum Microsoft Helm w tym miejscu][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Oto przykładowe dane wyjściowe, które mogą być widoczne w przypadku pomyślnego wykonania instalacji:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Wdrożenie Kubernetes może potrwać kilka minut. Aby upewnić się, że oba typy i usługi są poprawnie wdrożone i dostępne, wykonaj następujące polecenie:

```console
kubectl get all
```

Powinieneś oczekiwać, że zobaczysz coś podobnego do następującego:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Weryfikowanie wdrożenia Helm z testami Helm

Zainstalowane wykresy Helm definiują *testy Helm*, które stanowią wygodę do weryfikacji. Te testy weryfikują gotowość usługi. Aby zweryfikować usługi **zamiany mowy na tekst** i zamiany **tekstu na mowę** , zostanie wykonane polecenie [test Helm][helm-test] .

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Te testy zakończą się niepowodzeniem, jeśli stan POD nie jest `Running` lub jeśli wdrożenie nie znajduje się na liście pod `AVAILABLE` kolumną. Poczekaj, aż ukończenie tego procesu może potrwać od 10 minut.

Te testy będą wyprowadzać różne wyniki stanu:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Alternatywą dla wykonywania *testów Helm* może być zebranie *zewnętrznych adresów IP* i odpowiednich portów z `kubectl get all` polecenia. Korzystając z adresu IP i portu, Otwórz przeglądarkę internetową i przejdź do programu, aby `http://<external-ip>:<port>:/swagger/index.html` wyświetlić strony struktury API Swagger.

## <a name="customize-helm-charts"></a>Dostosuj wykresy Helm

Wykresy Helm są hierarchiczne. Jest to hierarchia umożliwiająca dziedziczenie wykresu, a także jest również koncepcją specyfiki, w której ustawienia, które są bardziej szczegółowymi regułami przesłonięć.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat instalowania aplikacji z programem Helm w usłudze Azure Kubernetes Service (AKS), [odwiedź to tutaj][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontenery usług Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md