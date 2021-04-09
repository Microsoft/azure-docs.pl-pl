---
title: Często zadawane pytania dotyczące siatki Service Fabric platformy Azure
description: Zapoznaj się z często zadawanymi pytaniami i odpowiedziami dotyczącymi usługi Azure Service Fabric siatką.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626040"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Często zadawane pytania dotyczące Service Fabric siatki

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. W tym artykule znajdują się odpowiedzi na często zadawane pytania.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak mogę zgłosić problem lub zadać pytanie?

Zadawaj pytania, uzyskuj odpowiedzi od inżynierów firmy Microsoft i Zgłoś problemy w [usłudze Service-Fabric-siatkowe repozytorium GitHub](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Przydział i koszt

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Jaki jest koszt uczestnictwa w wersji zapoznawczej?

Obecnie nie są naliczane opłaty za wdrażanie aplikacji lub kontenerów w wersji zapoznawczej siatki. Obejrzyj aktualizacje w programie, aby umożliwić korzystanie z rozliczeń. Zachęcamy jednak do usunięcia wdrożonych zasobów, które nie opuszczają ich działania, o ile nie zostaną aktywnie przetestowane.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Czy istnieje limit przydziału liczby rdzeni i pamięci RAM?

Tak. Przydziały dla każdej subskrypcji są następujące:

- Liczba aplikacji: 5
- Rdzenie na aplikację: 12
- Łączna ilość pamięci RAM na aplikację: 48 GB
- Punkty końcowe sieci i transferu danych przychodzących: 5
- Woluminy platformy Azure, które można dołączyć: 10
- Liczba replik usługi: 3
- Największy kontener, który można wdrożyć, jest ograniczony do 4 rdzeni i 16GB pamięci RAM.
- Można przydzielić rdzenie częściowe do kontenerów z przyrostem 0,5 rdzeni, maksymalnie 6 rdzeni.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Jak długo mogę pozostawić aplikację wdrożoną?

Obecnie ograniczono okres istnienia aplikacji na dwa dni. Jest to w celu zmaksymalizowania używania bezpłatnych rdzeni przyznanych do wersji zapoznawczej. W związku z tym można uruchamiać tylko danego wdrożenia w sposób ciągły przez 48 godzin, po upływie którego czas zostanie zamknięty.

Jeśli zobaczysz ten problem, możesz sprawdzić, czy system zamknie go, uruchamiając `az mesh app show` polecenie w interfejsie wiersza polecenia platformy Azure. Sprawdź, czy funkcja zwróci wartość `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Na przykład: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Aby usunąć grupę zasobów, użyj `az group delete <nameOfResourceGroup>` polecenia.

## <a name="deployments"></a>Wdrożenia

### <a name="what-container-images-are-supported"></a>Jakie obrazy kontenerów są obsługiwane?

W przypadku tworzenia aplikacji na komputerze z aktualizacją programu Windows Update Creators (wersja 1709) można używać tylko obrazów platformy Docker systemu Windows w wersji 1709.

W przypadku tworzenia aplikacji na komputerze z aktualizacją 2018 (wersja 1803) z systemem Windows 10 kwietnia można użyć obrazów platformy Docker systemu Windows w wersji 1709 lub 1803 Windows.

Następujące obrazy systemu operacyjnego kontenera mogą służyć do wdrażania usług:
- Windows-windowsservercore i nanoserver
    - System Windows Server 1709
    - System Windows Server 1803
    - System Windows Server 1809
    - LTSC systemu Windows Server 2019
- Linux
    - Brak znanych ograniczeń

> [!NOTE]
> Narzędzia programu Visual Studio dla siatki nie obsługują jeszcze wdrażania w kontenerach z systemem Windows Server 2019 i 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Jakie typy aplikacji można wdrażać? 

Można wdrożyć wszystkie elementy działające w kontenerach, które mieszczą się w ograniczeniach umieszczonych w zasobie aplikacji (Zobacz powyżej, aby uzyskać więcej informacji na temat przydziałów). W przypadku wykrycia, że używasz siatki do uruchamiania niedozwolonych obciążeń lub nadużycie systemu (tj. wyszukiwania), zastrzegamy sobie prawo do zakończenia wdrożeń i listy blokowania się, że subskrypcja nie zostanie uruchomiona w usłudze. Skontaktuj się z nami, jeśli masz jakieś pytania dotyczące uruchamiania określonego obciążenia. 

## <a name="developer-experience-issues"></a>Problemy dotyczące środowiska deweloperskiego

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Rozpoznawanie nazw DNS z kontenera nie działa

Wychodzące zapytania DNS z kontenera do usługi DNS Service Fabric mogą kończyć się niepowodzeniem w pewnych okolicznościach. Trwa badanie. Aby wyeliminować:

- Użyj aktualizacji Windows Update Creators (wersja 1709) lub nowszej jako obrazu podstawowego kontenera.
- Jeśli sama nazwa usługi nie działa, spróbuj użyć w pełni kwalifikowanej nazwy: ServiceName. ApplicationName.
- W pliku platformy Docker dla usługi Dodaj port, `EXPOSE <port>` gdzie jest to port, na którym ujawniasz swoją usługę. Na przykład:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>System DNS nie działa tak samo, jak w przypadku Service Fabric klastrów programistycznych i siatki

Może zajść konieczność odwołująca się do usług w inny sposób w lokalnym klastrze projektowym niż w sieci Azure.

W lokalnym klastrze deweloperskim Użyj programu `{serviceName}.{applicationName}` . Na platformie Azure Service Fabric siatka Użyj `{servicename}` . 

Siatka Azure nie obsługuje obecnie rozpoznawania nazw DNS między aplikacjami.

Inne znane problemy z usługą DNS dotyczące uruchamiania Service Fabric klastra projektowego w systemie Windows 10 można znaleźć w temacie: [debugowanie kontenerów systemu Windows](../service-fabric/service-fabric-how-to-debug-windows-containers.md) i [znanych problemów z usługą DNS](../service-fabric/service-fabric-dnsservice.md#known-issues).

### <a name="networking"></a>Sieć

Translator adresów sieciowych usługi servicefabric może zniknąć podczas korzystania z aplikacji na komputerze lokalnym. Aby zdiagnozować, czy ten wystąpił, uruchom następujące polecenie w wierszu polecenia:

`docker network ls` i sprawdź, czy `servicefabric_nat` jest wyświetlana lista.  Jeśli nie, uruchom następujące polecenie: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Spowoduje to rozwiązanie problemu, nawet jeśli aplikacja została już wdrożona lokalnie i w złej kondycji.

### <a name="issues-running-multiple-apps"></a>Problemy z uruchamianiem wielu aplikacji

W przypadku wszystkich aplikacji mogą wystąpić problemy z dostępnością procesora CPU i ograniczeniami. Aby wyeliminować:
- Utwórz klaster z pięcioma węzłami.
- Zmniejsz użycie procesora CPU w usługach w ramach wdrożonej aplikacji. Na przykład w pliku Service. YAML usługi przejdź `cpu: 1.0` do `cpu: 0.5`

Nie można wdrożyć wielu aplikacji w klastrze z jednym węzłem. Aby wyeliminować:
- W przypadku wdrażania wielu aplikacji w klastrze lokalnym należy użyć pięciu węzłów klastra.
- Usuń aplikacje, które nie są obecnie testowane.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>Narzędzia VS mają ograniczoną obsługę kontenerów systemu Windows

Narzędzia Visual Studio obsługują tylko wdrażanie kontenerów systemu Windows z podstawową wersją systemu operacyjnego Windows Server 1709 i 1803. 

## <a name="feature-gaps-and-other-known-issues"></a>Luki w funkcjach i inne znane problemy

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Po wdrożeniu aplikacji, skojarzony z nią zasób sieciowy nie ma adresu IP

Istnieje znany problem, w którym adres IP nie staje się dostępny natychmiast. Sprawdź stan zasobu sieciowego za kilka minut, aby wyświetlić skojarzony adres IP.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Aplikacja nie może uzyskać dostępu do odpowiedniego zasobu sieci/woluminu

W modelu aplikacji użyj pełnego identyfikatora zasobu dla sieci i woluminów, aby móc uzyskać dostęp do skojarzonego zasobu. Oto przykład instrukcji szybkiego startu:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Po skalowaniu w poziomie wszystkie moje kontenery podlegają zmianom, w tym uruchamiania

Jest to usterka, a poprawka jest implementowana.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Fabric siatki, zapoznaj się z [omówieniem](service-fabric-mesh-overview.md).
