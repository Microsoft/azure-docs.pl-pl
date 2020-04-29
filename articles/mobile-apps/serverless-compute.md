---
title: Tworzenie zaplecza bezserwerowej aplikacji mobilnej przy użyciu Azure Functions i innych usług
description: Dowiedz się więcej na temat usług obliczeniowych używanych do tworzenia pełnego, bezserwerowego zaplecza aplikacji mobilnej.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240143"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Tworzenie składników zaplecza mobilnego za pomocą usług obliczeniowych
Każda aplikacja mobilna wymaga zaplecza, który jest odpowiedzialny za magazyn danych, logikę biznesową i bezpieczeństwo. Zarządzanie infrastrukturą w celu hostowania i wykonywania kodu zaplecza wymaga zmiany rozmiaru, aprowizacji i skalowania wielu serwerów. Konieczne jest również zarządzanie aktualizacjami systemu operacyjnego oraz używanymi sprzętami i zastosowaniem poprawek zabezpieczeń. Następnie należy monitorować wszystkie te składniki infrastruktury pod kątem wydajności, dostępności i odporności na uszkodzenia. 

Architektura bezserwerowa jest przydatna dla tego typu scenariusza, ponieważ nie ma żadnych serwerów do zarządzania i nie ma żadnych zarządzanych przez system operacyjny ani do nich aktualizacji sprzętowych. Architektura bezserwerowa oszczędza czas i koszt dewelopera, co oznacza szybszy czas wprowadzenia na rynek i skoncentrowanie się na tworzeniu aplikacji.

## <a name="benefits-of-compute"></a>Zalety obliczeń
- Abstrakcja serwerów oznacza, że nie trzeba martwić się o hosting, stosowanie poprawek i zabezpieczeń, co umożliwia skoncentrowanie się wyłącznie na kodzie.
- Szybkie i wydajne skalowanie zapewnia, że zasoby są obsługiwane automatycznie lub na żądanie na dowolnym wymaganej skali.
- Wysoka dostępność i odporność na uszkodzenia.
- Większość rozliczeń gwarantuje, że opłaty są naliczane tylko wtedy, gdy Twój kod jest rzeczywiście uruchomiony.
- Kod jest uruchamiany w chmurze zapisaną w wybranym języku.

Poniższe usługi umożliwiają włączenie funkcji obliczeniowych bezserwerowych w aplikacjach mobilnych.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) to oparte na zdarzeniach środowisko obliczeniowe, za pomocą którego można wykonać swój kod, zapisany w wybranym języku programowania, bez obaw o serwery. Nie trzeba zarządzać aplikacją ani infrastrukturą, aby ją uruchomić. Funkcje są skalowane na żądanie i płacisz tylko za czas działania kodu. Usługa Azure Functions to doskonały sposób implementacji interfejsu API dla aplikacji mobilnej. Są one łatwe do wdrożenia i konserwacji i są dostępne za pośrednictwem protokołu HTTP.

**Najważniejsze funkcje**
- Sterowane zdarzeniami i skalowalne, w których można użyć wyzwalaczy i powiązań, aby zdefiniować, kiedy wywoływana jest funkcja i do jakie dane są połączone.
- Korzystaj z własnych zależności, ponieważ usługa Functions obsługuje narzędzia NuGet i NPM, dzięki czemu możesz korzystać z ulubionych bibliotek.
- Zintegrowane zabezpieczenia umożliwiające ochronę funkcji wyzwalanych przez protokół HTTP z dostawcami OAuth, takimi jak Azure Active Directory, Facebook, Google, Twitter i konto Microsoft.
- Uproszczona integracja z różnymi ofertami [usług platformy Azure](/azure/azure-functions/functions-overview) i oprogramowania jako usługi (SaaS).
- Elastyczne programowanie, dzięki czemu możesz nakodować funkcje bezpośrednio w Azure Portal lub skonfigurować ciągłą integrację i wdrożyć kod za pomocą usługi GitHub, Azure DevOps Services i innych obsługiwanych narzędzi programistycznych.
- Środowisko uruchomieniowe usługi Functions to otwarte źródło i dostępne w serwisie [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Udoskonalone środowisko programistyczne, w którym można kod, testować i debugować lokalnie przy użyciu preferowanego edytora lub łatwego w użyciu interfejsu internetowego z monitorowaniem za pomocą zintegrowanych narzędzi i wbudowanych funkcji DevOps.
- Wiele języków programowania i opcji hostingu do programowania, takich jak C#, Node. js, Java, JavaScript lub Python.
- Model cen z opcją płatność za użycie oznacza, że płacisz tylko za czas działania kodu.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com)
- [Dokumentacja usługi Azure Functions](/azure/azure-functions/)
- [Azure Functions — przewodnik dla deweloperów](/azure/azure-functions/functions-reference)
- [Przewodniki Szybki start](/azure/azure-functions/functions-create-first-function-vs-code)
- [Samples](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Dzięki [Azure App Service](https://azure.microsoft.com/services/app-service/)można kompilować i hostować aplikacje sieci Web i interfejsy API RESTful w wybranym języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje zarówno system Windows, jak i Linux, a także włącza zautomatyzowane wdrożenia z usług GitHub, Azure DevOps lub dowolnego repozytorium git.

**Najważniejsze funkcje**
- Obsługa wielu języków i struktur dla ASP.NET, ASP.NET Core, Java, Ruby, Node. js, PHP lub Python. Można również uruchamiać program PowerShell oraz inne skrypty lub pliki wykonywalne jako usługi w tle.
- Optymalizacja DevOps dzięki ciągłej integracji i wdrażaniu za pomocą usługi Azure DevOps, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Zarządzaj aplikacjami w usłudze App Service za pomocą programu Azure PowerShell lub międzyplatformowego interfejsu wiersza polecenia.
- Globalna skala o wysokiej dostępności do ręcznego lub automatycznego skalowania w górę lub w dół.
- Połączenia z platformami SaaS i danymi lokalnymi, aby wybierać spośród ponad 50 łączników dla systemów korporacyjnych, takich jak SAP, SaaS Services, takich jak Salesforce, i usług internetowych, takich jak Facebook. Dostęp do danych lokalnych za pomocą połączeń hybrydowych i sieci wirtualnych platformy Azure.
- Azure App Service są zgodne ze standardami ISO, SOC i PCI. Uwierzytelnianie użytkowników za pomocą Azure Active Directory lub logowanie do mediów społecznościowych, takich jak Google, Facebook, Twitter i Microsoft. Utwórz ograniczenia adresów IP i zarządzaj tożsamościami usługi.
- Szablony aplikacji do wyboru z obszernej listy szablonów aplikacji w portalu Azure Marketplace, takich jak WordPress, Joomla i Drupal.
- Integracja programu Visual Studio z dedykowanymi narzędziami w programie Visual Studio usprawnia tworzenie, wdrażanie i debugowanie.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com/)
- [Dokumentacja Azure App Service](/azure/app-service/)
- [Przewodniki Szybki start](/azure/app-service/app-service-web-get-started-dotnet)
- [Samples](/azure/app-service/samples-cli)
- [Samouczki](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Usługa [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) zarządza hostowanym środowiskiem Kubernetes. Usługa AKS umożliwia szybkie i łatwe wdrażanie konteneryzowanych aplikacji i zarządzanie nimi bez specjalistycznej wiedzy z zakresu aranżacji kontenerów. Eliminuje to również obciążenie trwających operacji i konserwacji. AKS, uaktualnienia i skalowanie zasobów na żądanie bez przełączania aplikacji do trybu offline.

**Najważniejsze funkcje**
- Łatwo Migruj istniejące aplikacje do kontenerów i uruchamiaj je w AKS.
- Uprość wdrażanie aplikacji opartych na mikrousługach i zarządzanie nimi.
- Zabezpiecz DevOps dla AKS, aby osiągnąć balans między szybkością i zabezpieczeniami i szybciej dostarczać kod na dużą skalę.
- Łatwo Skaluj przy użyciu AKS i Azure Container Instances, aby udostępnić zasobniki w Container Instances, które zaczynają się w ciągu sekund.
- Wdrażaj urządzenia IoT na żądanie i zarządzaj nimi.
- Uczenie modeli uczenia maszynowego przy użyciu narzędzi, takich jak TensorFlow i KubeFlow.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com/)
- [Dokumentacja usługi Azure Kubernetes](/azure/aks/)
- [Przewodniki Szybki start](/azure/aks/kubernetes-walkthrough-portal)
- [Samouczki](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) to doskonałe rozwiązanie dla dowolnego scenariusza, który może działać w kontenerach izolowanych, takich jak proste aplikacje, Automatyzacja zadań i zadania kompilacji. Szybkie opracowywanie aplikacji bez konieczności zarządzania maszynami wirtualnymi.

**Najważniejsze funkcje**
- Czasy szybkiego uruchamiania jako Container Instances mogą uruchamiać kontenery na platformie Azure w kilka sekund bez konieczności aprowizacji maszyn wirtualnych i zarządzania nimi.
- Publiczna łączność IP i niestandardowa nazwa DNS.
- Zabezpieczenia na poziomie funkcji hypervisor, które gwarantują, że aplikacja jest izolowana w kontenerze, tak jak w przypadku maszyny wirtualnej.
- Niestandardowe rozmiary umożliwiające optymalne wykorzystanie przez umożliwienie dokładnej specyfikacji rdzeni procesora i pamięci. Opłaty są naliczane zgodnie z zapotrzebowaniem i za sekundę, dlatego można skutecznie dostosować wydatki w oparciu o potrzeby.
- Magazyn trwały do pobrania i trwałego stanu. Container Instances oferuje bezpośrednie instalowanie udziałów Azure Files.
- Kontenery systemu Linux i Windows zaplanowano przy użyciu tego samego interfejsu API.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com/)
- [Dokumentacja Azure Container Instances](/azure/container-instances/)
- [Przewodniki Szybki start](/azure/container-instances/container-instances-quickstart-portal)
- [Samples](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Samouczki](/azure/container-instances/container-instances-tutorial-prepare-app)