---
title: Kontenery poufne w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się więcej o obsłudze niezmodyfikowanego kontenera w kontenerach poufnych.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: bf92e7f28d6a5804886d093671f4b7e33878f1ec
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652681"
---
# <a name="confidential-containers"></a>Kontenery poufne

## <a name="overview"></a>Omówienie

Umożliwienie deweloperom przenoszenia **istniejącej aplikacji platformy Docker (nowej lub istniejącej)** i bezpiecznego uruchamiania jej w usłudze Azure Kubernetes Service (AKS) za pomocą obsługiwanych węzłów obliczeniowych.

Kontenery poufne pomagają chronić:

- integralność danych 
- poufność danych
- integralność kodu
- Ochrona kodu kontenera przy użyciu szyfrowania
- gwarancje oparte na sprzęcie
- Zezwalaj na uruchamianie istniejących aplikacji
- Tworzenie katalogu głównego sprzętu zaufania
- Usuń administratora hosta, Kubernetes administrator i funkcję hypervisor z granicy zaufania

Sprzętowe środowisko wykonawcze (TEE) jest ważnym składnikiem służącym do zapewnienia mocnych gwarancji przez pomiary sprzętu i oprogramowania z zaufanych składników obliczeniowych (TCB). Weryfikacje tych pomiarów pomagają w sprawdzeniu oczekiwanych obliczeń i sprawdzaniu, czy są one modyfikowane przez aplikacje kontenerów.

Kontenery poufne obsługują aplikacje niestandardowe opracowane przy użyciu **języka Python, Java, Node js itp. lub spakowanych aplikacji kontenera, takich jak Nginx, Redis Cache, memcache** i tak dalej, aby można było uruchamiać niemodyfikowane na AKS z węzłami do przetwarzania poufnego.

Kontenery poufne są najszybszą ścieżką do poufności kontenera i będą wymagały ponownego pakowania istniejących aplikacji kontenera platformy Docker i nie będą wymagać zmian w kodzie aplikacji. Kontenery poufne to aplikacje kontenerów platformy Docker spakowane do uruchomienia w TEE. Niektóre poufne funkcje obsługi kontenera oferują również szyfrowanie kontenerów, które mogą pomóc w ochronie kodu kontenera podczas przechowywania i transportu, a także na hoście. Szyfrowanie kontenerów pozwala dodatkowo i chronić kod/model spakowany w kontenerze przy użyciu klucza odszyfrowywania dołączonego do TEE.

Poniżej znajduje się proces poufności kontenerów od projektowania do wdrożenia ![ poufnego kontenera, jak przetworzyć.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Poufne materiały do obsługi kontenerów
Aby można było uruchomić istniejący kontener platformy Docker niemodyfikowany, wymaga oprogramowania SGX, dzięki czemu wywołania aplikacji mogą używać specjalnego zestawu instrukcji procesora CPU, który jest dostępny w celu obniżenia obszaru dołączania powierzchni i nie muszą być zależne od systemu operacyjnego gościa. Po zapakowaniu z oprogramowaniem SGX Runtime kontenery są automatycznie uruchamiane w chronionej enclaves, co powoduje usunięcie systemu operacyjnego gościa, systemu operacyjnego hosta lub funkcji hypervisor z granicy zaufania. To izolowane wykonanie w węźle (maszynie wirtualnej) z szyfrowaniem danych w pamięci, które jest obsługiwane przez sprzęt, zmniejsza ogólne obszary ataku powierzchni i zmniejsza luki w zabezpieczeniach systemu operacyjnego lub warstw funkcji hypervisor.

Kontenery poufne są w pełni obsługiwane w AKS i włączane przez partnerów platformy Azure i projekty oprogramowania Open-Source (OSS). Deweloperzy mogą wybrać dostawców oprogramowania w oparciu o funkcje, integrację z usługami i narzędziami platformy Azure.

## <a name="partner-enablers"></a>Partnerzy partnerów
> [!NOTE]
> Poniższe rozwiązania są oferowane przez partnerów platformy Azure i mogą powodować naliczanie opłat licencyjnych. Sprawdź niezależne warunki oprogramowania partnerskiego. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) udostępnia oprogramowanie platformy SGX, które umożliwia uruchamianie niezmodyfikowanych kontenerów na AKS. Dowiedz się więcej na temat funkcji i zapoznaj się z przykładowymi aplikacjami [tutaj](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Rozpocznij pracę z przykładową Redis Cache i aplikacją niestandardową języka Python [tutaj](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Proces Anjuna](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) oferuje deweloperom możliwość korzystania z portalu i interfejsu wiersza polecenia w celu przełączenia ich aplikacji do kontenerów i przekonwertować ich do SGX możliwości obsługi kontenerów, bez konieczności modyfikowania ani ponownego kompilowania aplikacji. Fortanix zapewnia elastyczność uruchamiania i zarządzania najszerszym zestawem aplikacji, w tym istniejącymi aplikacjami, nowymi aplikacjami natywnymi enklawy i wstępnie spakowanymi aplikacjami. Aby utworzyć kontenery poufne, użytkownicy mogą zacząć pracę od interfejsu użytkownika lub [interfejsów API REST](https://www.fortanix.com/api/em/) programu [Computing Manager](https://em.fortanix.com/) , postępując zgodnie z przewodnikiem [Szybki Start](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) usługi Azure Kubernetes.

![Proces wdrażania Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

Usługa [Scone](https://scontain.com/index.html?lang=en) obsługuje zasady zabezpieczeń, które mogą generować certyfikaty, klucze i wpisy tajne, i gwarantują, że są one widoczne tylko dla zaświadczania usług aplikacji. W ten sposób usługi aplikacji automatycznie zaświadczą się nawzajem za pośrednictwem protokołu TLS — bez konieczności modyfikowania aplikacji ani protokołu TLS. Wyjaśniono to z pomocą prostej aplikacji kolby: https://sconedocs.github.io/flask_demo/  

SCONE może konwertować istniejące większość plików binarnych na aplikacje, które działają wewnątrz enclaves, bez konieczności zmiany aplikacji lub ponownego kompilowania aplikacji. SCONE chroni również języki interpretowane, takie jak Python, **szyfrując** zarówno pliki danych, jak i pliki kodu w języku Python. Za pomocą zasad zabezpieczeń SCONE, jeden może chronić zaszyfrowane pliki przed nieautoryzowanymi dostępem, modyfikacjami i wycofywaniem. Jak "sconify" istniejąca aplikacja języka Python została omówiona w [tym miejscu](https://sconedocs.github.io/sconify_image/)

![Przepływ Scontain](./media/confidential-containers/scone-workflow.png)

Wdrożenia Scone na poufnych węzłach obliczeniowych z AKS są w pełni obsługiwane i zintegrowane z innymi usługami platformy Azure. Zacznij korzystać z przykładowej aplikacji tutaj https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>Serviceenables 
> [!NOTE]
> Poniższe rozwiązania są oferowane w ramach projektów Open-Source i nie są bezpośrednio powiązane z usługami Azure CONFIDENTIAL (ACC) lub Microsoft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) to lekki system operacyjny gościa, zaprojektowany do uruchamiania pojedynczej aplikacji systemu Linux z minimalnymi wymaganiami hosta. Graphene mogą uruchamiać aplikacje w izolowanym środowisku z korzyściami porównywalnymi do uruchamiania kompletnego systemu operacyjnego i mają dobre wsparcie dla konwertowania istniejącej aplikacji kontenera Docker do kontenerów z osłoną Graphene (SGR).

Rozpocznij pracę z przykładową aplikacją i wdrożeniem w witrynie AKS [tutaj](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) to bezpieczny dla pamięci system operacyjny biblioteki wieloprocesowej (LibOS) dla platformy Intel SGX. Umożliwia uruchamianie starszych aplikacji na SGX z niewielkimi zmianami w kodzie źródłowym. Occlum w sposób przezroczysty chroni poufność obciążeń użytkowników, jednocześnie umożliwiając łatwe podniesienie i przechodzenie do istniejących aplikacji platformy Docker.

Occlum obsługuje wdrożenia AKS. Postępuj zgodnie z instrukcjami dotyczącymi wdrażania, korzystając z różnych przykładowych [aplikacji.](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demonstracja kontenerów poufnych
Zapoznaj się z demonstracją poufnych kontenerów. Przykład jest dostępny [tutaj](https://docs.microsoft.com/azure/architecture/example-scenario/confidential/healthcare-inference). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Zacznij korzystać z programu Touch

Masz pytania dotyczące Twojej implementacji lub chcesz, aby stał się ona włącznikiem? Wyślij wiadomość e-mail do zespołu produktu **acconaks@microsoft.com**

## <a name="reference-links"></a>Linki odwołań

[Usługa Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
