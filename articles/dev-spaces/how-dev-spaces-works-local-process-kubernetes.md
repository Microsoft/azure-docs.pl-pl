---
title: Jak działa proces lokalny z usługą Kubernetes
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Opisuje procesy związane z używaniem procesu lokalnego z usługą Kubernetes w celu połączenia komputera deweloperskiego z klastrem Kubernetes
keywords: Proces lokalny z Kubernetes, Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: dd126fc55a86b1de115239a31e5adb7b1d264846
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974425"
---
# <a name="how-local-process-with-kubernetes-works"></a>Jak działa proces lokalny z usługą Kubernetes

Proces lokalny z programem Kubernetes umożliwia uruchamianie i debugowanie kodu na komputerze deweloperskim, przy jednoczesnym połączeniu z klastrem Kubernetes z pozostałą częścią aplikacji lub usług. Na przykład jeśli masz dużą architekturę mikrousług z wieloma zależnymi usługami i bazami danych, replikowanie tych zależności na komputerze deweloperskim może być trudne. Ponadto Kompilowanie i wdrażanie kodu w klastrze Kubernetes dla każdej zmiany kodu podczas programowania w pętli wewnętrznej może być powolne, czasochłonne i trudne do użycia z debugerem.

Proces lokalny z Kubernetes pozwala uniknąć konieczności kompilowania i wdrażania kodu w klastrze przez utworzenie połączenia bezpośrednio między komputerem deweloperskim i klastrem. Połączenie komputera deweloperskiego z klastrem podczas debugowania umożliwia szybkie testowanie i opracowywanie usługi w kontekście pełnej aplikacji bez konieczności tworzenia żadnej konfiguracji platformy Docker lub Kubernetes.

Proces lokalny z Kubernetes przekierowuje ruch między podłączonym klastrem Kubernetes i komputerem deweloperskim. To przekierowywanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze Kubernetes, aby komunikować się tak, jakby znajdowały się w tym samym klastrze Kubernetes. Proces lokalny z usługą Kubernetes umożliwia także replikować zmienne środowiskowe i zainstalowane woluminy dostępne dla zasobników w klastrze Kubernetes na komputerze deweloperskim. Zapewnianie dostępu do zmiennych środowiskowych i zainstalowanych woluminów na komputerze deweloperskim pozwala szybko korzystać z kodu bez konieczności ręcznego replikowania tych zależności.

## <a name="using-local-process-with-kubernetes"></a>Używanie procesu lokalnego z Kubernetes

Aby można było korzystać z procesu lokalnego z usługą Kubernetes, konieczne jest [Visual Studio Code][vs-code] z [Azure dev Spaces][azds-vs-code] i rozszerzeniami [usługi Azure Kubernetes][az-aks-vs-code] zainstalowanymi i uruchomionymi w systemie macOS lub Windows 10, a także z [zainstalowanym interfejsem wiersza polecenia Azure dev Spaces][azds-cli]. Można również użyć [programu Visual Studio 2019][visual-studio] działającego w systemie Windows 10 z zainstalowanymi obciążeniami *ASP.NET i Web Development* oraz *Azure Development* , a także z włączoną flagą funkcji *AzureDevSpacesTools. LocalKubernetesDebugging* Preview oraz [zainstalowaną Azure dev Spaces interfejs wiersza polecenia][azds-cli]. W przypadku korzystania z procesu lokalnego z usługą Kubernetes w celu nawiązania połączenia z klastrem Kubernetes można przekierować cały ruch do i z istniejącego obszaru klastra na komputer deweloperski.

> [!NOTE]
> W przypadku korzystania z procesu lokalnego z usługą Kubernetes zostanie wyświetlony monit o podanie nazwy usługi, która ma zostać przekierowana na komputer deweloperski. Ta opcja jest wygodnym sposobem identyfikacji pod kątem przekierowania. Wszystkie przekierowania między klastrem Kubernetes a komputerem deweloperskim dotyczą usługi pod.

Gdy proces lokalny z usługą Kubernetes nawiązuje połączenie z klastrem,:

* Zostanie wyświetlony komunikat z prośbą o skonfigurowanie usługi do zamiany na klaster, port na komputerze deweloperskim, który będzie używany w kodzie, oraz zadanie uruchamiania dla kodu jako akcję jednorazową.
* Zastępuje kontener w obszarze na klastrze z kontenerem zdalnego agenta, który przekierowuje ruch do komputera deweloperskiego.
* Uruchamia funkcję [polecenia kubectl port-forward][kubectl-port-forward] na komputerze deweloperskim, aby przekazywać ruch z komputera deweloperskiego do zdalnego agenta działającego w klastrze.
* Zbiera informacje o środowisku z klastra przy użyciu zdalnego agenta. Informacje o środowisku obejmują zmienne środowiskowe, widoczne usługi, instalacje woluminów i instalacje tajne.
* Konfiguruje środowisko w programie Visual Studio lub Visual Studio Code, aby usługa na komputerze deweloperskim mogła uzyskiwać dostęp do tych samych zmiennych, tak jakby były uruchomione w klastrze.  
* Aktualizuje plik hosts w celu mapowania usług w klastrze na lokalne adresy IP na komputerze deweloperskim. Te wpisy plików hostów umożliwiają uruchamianie kodu na komputerze deweloperskim w celu wykonywania żądań do innych usług uruchomionych w klastrze. Aby można było zaktualizować plik hosts, proces lokalny z usługą Kubernetes będzie pytał o dostęp administratora na komputerze deweloperskim podczas nawiązywania połączenia z klastrem.
* Uruchamia uruchamianie i debugowanie kodu na komputerze deweloperskim. W razie potrzeby proces lokalny z usługą Kubernetes będzie zwalniać wymagane porty na komputerze deweloperskim przez zatrzymywanie usług lub procesów, które aktualnie korzystają z tych portów.

Po nawiązaniu połączenia z klastrem można uruchomić i debugować kod natywnie na komputerze bez kontenerach, a kod może bezpośrednio współdziałać z resztą klastra. Każdy ruch sieciowy odbierany przez agenta zdalnego jest przekierowywany do portu lokalnego określonego podczas połączenia, dzięki czemu kod natywnie uruchomiony może zaakceptować i przetworzyć ten ruch. Zmienne środowiskowe, woluminy i wpisy tajne z klastra są udostępniane w kodzie uruchomionym na komputerze deweloperskim. Ponadto ze względu na to, że wpisy pliku Hosts i przekazywanie portów zostały dodane do komputera dewelopera przez proces lokalny z Kubernetes, kod może wysyłać ruch sieciowy do usług uruchomionych w klastrze przy użyciu nazw usług z klastra, a ruch jest przesyłany do usług uruchomionych w klastrze. Ruch jest kierowany między komputerem deweloperskim i klastrem przez cały czas, gdy masz połączenie.

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

W przypadku korzystania z procesu lokalnego z Kubernetes w celu nawiązania połączenia z klastrem dzienniki diagnostyczne z klastra są rejestrowane w [katalogu tymczasowym][azds-tmp-dir]komputera deweloperskiego. Za pomocą Visual Studio Code można także użyć polecenia *Pokaż informacje diagnostyczne* , aby wydrukować bieżące zmienne środowiskowe i wpisy DNS z klastra.

## <a name="limitations"></a>Ograniczenia

Proces lokalny z Kubernetes ma następujące ograniczenia:

* Proces lokalny z usługą Kubernetes przekierowuje ruch dla jednej usługi do komputera deweloperskiego. Nie można użyć procesu lokalnego z usługą Kubernetes w celu przekierowania wielu usług w tym samym czasie.
* Aby można było połączyć się z tą usługą, usługa musi być objęta usługą. Nie można nawiązać połączenia z usługą z wieloma zasobnikami, takimi jak usługa z replikami.
* W przypadku procesu lokalnego z usługą Kubernetes w celu pomyślnego nawiązania połączenia na komputerze może znajdować się tylko jeden kontener uruchomiony w tym pod. Proces lokalny z usługą Kubernetes nie może połączyć się z usługami za pomocą zasobników z dodatkowymi kontenerami, takimi jak kontenery przyczepek z systemem.
* Proces lokalny z Kubernetes wymaga podniesionych uprawnień do uruchamiania na komputerze deweloperskim, aby można było edytować plik Hosts.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z procesu lokalnego z usługą Kubernetes w celu nawiązania połączenia z lokalnym komputerem deweloperskim z klastrem, zobacz [Używanie procesu lokalnego z usługą Kubernetes with Visual Studio Code][local-process-kubernetes-vs-code] i [Używanie procesu lokalnego z programem Kubernetes za pomocą programu Visual Studio][local-process-kubernetes-vs].

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download