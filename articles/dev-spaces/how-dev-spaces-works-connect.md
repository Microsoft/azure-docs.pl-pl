---
title: Jak połączyć komputer deweloperski z klastrem AKS
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Opisuje procesy opisane przy użyciu Azure Dev Spaces do łączenia komputera deweloperskiego z klastrem usługi Azure Kubernetes Service
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241714"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Jak połączyć komputer deweloperski z klastrem AKS

Za pomocą Azure Dev Spaces można połączyć komputer deweloperski z klastrem AKS, co pozwala na uruchamianie i debugowanie kodu na komputerze deweloperskim, tak jakby był uruchomiony w klastrze. Azure Dev Spaces przekierowuje ruch między podłączonym klastrem AKS, uruchamiając go w klastrze, który działa jako agent zdalny do przekierowywania ruchu między komputerem deweloperskim i klastrem. To przekierowywanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze AKS, aby komunikować się tak, jakby znajdowały się w tym samym klastrze AKS. To połączenie umożliwia również uruchamianie i debugowanie kodu z lub bez kontenera na komputerze deweloperskim. Połączenie komputera deweloperskiego z klastrem ułatwia szybkie tworzenie aplikacji i wykonywanie testów kompleksowych.

## <a name="connecting-to-your-cluster"></a>Nawiązywanie połączenia z klastrem

Nawiąż połączenie z istniejącym klastrem AKS przy użyciu [Visual Studio Code][vs-code] z rozszerzeniem [Azure dev Spaces][azds-vs-code] zainstalowanym w systemie MacOS lub Windows 10. Po nawiązaniu połączenia możesz przekierować cały ruch do i z nowego lub istniejącego w klastrze do komputera deweloperskiego.

> [!NOTE]
> W przypadku używania Visual Studio Code do nawiązywania połączenia z klastrem rozszerzenie Azure Dev Spaces udostępnia opcję przekierowywania usługi do komputera deweloperskiego. Ta opcja jest wygodnym sposobem identyfikacji pod kątem przekierowania. Wszystkie przekierowania między klastrem AKS a komputerem deweloperskim dotyczą usługi pod.

Połączenie z klastrem nie wymaga, aby Azure Dev Spaces włączony w klastrze. Zamiast tego, gdy rozszerzenie Azure Dev Spaces nawiązuje połączenie z klastrem,:

* Zastępuje kontener w klastrze AKS z kontenerem zdalnego agenta, który przekierowuje ruch do komputera deweloperskiego. Podczas przekierowywania nowego pod Azure Dev Spaces w klastrze AKS jest tworzony nowy element pod, a agent zdalny.
* Uruchamia funkcję [polecenia kubectl port-forward][kubectl-port-forward] na komputerze deweloperskim, aby przekazywać ruch z komputera deweloperskiego do zdalnego agenta działającego w klastrze.
* Zbiera informacje o środowisku z klastra przy użyciu zdalnego agenta. Informacje o środowisku obejmują zmienne środowiskowe, widoczne usługi, instalacje woluminów i instalacje tajne.
* Konfiguruje środowisko w terminalu Visual Studio Code, dzięki czemu usługa na komputerze deweloperskim będzie mogła uzyskiwać dostęp do tych samych zmiennych, tak jakby były uruchomione w klastrze.  
* Aktualizuje plik hosts, aby mapować usługi w klastrze AKS na lokalne adresy IP na komputerze deweloperskim. Te wpisy plików hostów umożliwiają uruchamianie kodu na komputerze deweloperskim w celu wykonywania żądań do innych usług uruchomionych w klastrze. Aby zaktualizować plik hosts, Azure Dev Spaces będzie pytał o dostęp administratora na komputerze deweloperskim podczas nawiązywania połączenia z klastrem.

Jeśli w klastrze są włączone Azure Dev Spaces, można również użyć opcji [przekierowania ruchu oferowanej przez Azure dev Spaces][how-it-works-routing]. Przekierowanie ruchu oferowane przez Azure Dev Spaces umożliwia nawiązanie połączenia z kopią usługi działającej w podrzędnym obszarze dev. Korzystanie z podrzędnego miejsca dev pozwala uniknąć zakłócania pracy innych osób w nadrzędnym obszarze dev, ponieważ przekierowujesz tylko ruch ukierunkowany na wystąpienie obszaru podrzędnego usługi, pozostawiając wystąpienie obszaru nadrzędnego niezmodyfikowanej usługi.

Po nawiązaniu połączenia z klastrem ruch jest kierowany do komputera deweloperskiego niezależnie od tego, czy usługa jest uruchomiona na komputerze deweloperskim.

## <a name="running-code-on-your-development-computer"></a>Uruchamianie kodu na komputerze deweloperskim

Po nawiązaniu połączenia z klastrem AKS można uruchomić dowolny kod natywnie na komputerze bez kontenerach. Każdy ruch sieciowy odbierany przez agenta zdalnego jest przekierowywany do portu lokalnego określonego podczas połączenia, dzięki czemu kod natywnie uruchomiony może zaakceptować i przetworzyć ten ruch. Zmienne środowiskowe, woluminy i wpisy tajne z klastra są udostępniane w kodzie uruchomionym na komputerze deweloperskim. Ponadto ze względu na to, że w przypadku wpisów w pliku Hosts i przekazywania portów dodanych do komputera dewelopera Azure Dev Spaces kod może wysyłać ruch sieciowy do usług uruchomionych w klastrze przy użyciu nazw usług z klastra, a ruch jest przesyłany do usług uruchomionych w klastrze.

Ponieważ Twój kod jest uruchomiony na komputerze deweloperskim, możesz skorzystać z dowolnego narzędzia używanego zwykle do programowania, aby uruchomić kod i debugować go. Ruch jest kierowany między komputerem deweloperskim i klastrem przez cały czas, gdy masz połączenie. To trwałe połączenie umożliwia uruchamianie, zatrzymywanie i ponowne uruchamianie kodu, dopóki nie będzie konieczne ponowne nawiązanie połączenia.

Ponadto Azure Dev Spaces zapewnia sposób replikowania zmiennych środowiskowych i zainstalowanych plików dostępnych dla zasobników w klastrze AKS na komputerze deweloperskim za pomocą pliku *azds-Local. env* . Możesz również użyć tego pliku, aby utworzyć nowe zmienne środowiskowe i instalacje woluminów.

## <a name="additional-configuration-with-azds-localenv"></a>Dodatkowa konfiguracja z azds-Local. env

Plik *azds-Local. env* umożliwia replikowanie zmiennych środowiskowych i zainstalowanych plików dostępnych dla Twojego zasobnika w klastrze AKS. W pliku *azds-Local. env* można określić następujące akcje:

* Pobierz wolumin i Ustaw ścieżkę do tego woluminu jako zmienną środowiskową.
* Pobierz pojedynczy plik lub zestaw plików z woluminu i zainstaluj go na komputerze deweloperskim.
* Udostępnienie usługi niezależnie od klastra, z którym nawiązano połączenie.

Oto przykład pliku *azds-Local. env* :

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Domyślny plik *azds-Local. env* nie jest tworzony automatycznie, dlatego należy ręcznie utworzyć plik w katalogu głównym projektu.

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

Po nawiązaniu połączenia z klastrem AKS dzienniki diagnostyczne z klastra są rejestrowane w [katalogu tymczasowym][azds-tmp-dir]komputera deweloperskiego. Za pomocą Visual Studio Code można także użyć polecenia *Pokaż informacje diagnostyczne* , aby wydrukować bieżące zmienne środowiskowe i wpisy DNS z klastra AKS.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć łączenie lokalnego komputera deweloperskiego z klastrem AKS, zobacz [Łączenie komputera deweloperskiego z klastrem AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
