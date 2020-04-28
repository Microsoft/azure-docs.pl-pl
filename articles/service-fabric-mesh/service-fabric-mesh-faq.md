---
title: Typowe pytania dotyczące siatki sieci szkieletowej usług Azure
description: Dowiedz się więcej o często zadawanych pytaniach i odpowiedziach dotyczących usługi Azure Service Fabric Mesh.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 2a5c2ea63d162eb6fb78ab702e0519f8ac25dcc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252488"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Często zadawane pytania dotyczące siatki sieci szkieletowej usług

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. W tym artykule zawiera odpowiedzi na często zadawane pytania.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak zgłosić problem lub zadać pytanie?

Zadawaj pytania, utrzyj odpowiedzi od inżynierów firmy Microsoft i zgłaszaj problemy w repozytorium GitHub w sieci szkieletowej.Ask questions, get answers from Microsoft engineers, and report issues in the [service-fabric-mesh-preview GitHub repo](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Przydział i koszt

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Jaki jest koszt uczestnictwa w wersji zapoznawczej?

Obecnie nie ma żadnych opłat za wdrażanie aplikacji lub kontenerów w wersji zapoznawczej siatki. Prosimy o aktualizacje w maju, aby umożliwić rozliczenia. Zachęcamy jednak do usunięcia zasobów, które wdrażasz, a nie do pozostawienia ich uruchomionych, chyba że aktywnie je testujesz.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Czy istnieje limit przydziału liczby rdzeni i pamięci RAM?

Tak. Przydziały dla każdej subskrypcji to:

- Liczba zgłoszeń: 5
- Rdzenie na aplikację: 12
- Całkowita pamięć RAM na aplikację: 48 GB
- Punkty końcowe sieci i transferu przychodzącego: 5
- Woluminy platformy Azure, które można dołączyć: 10
- Liczba replik usługi: 3
- Największy kontener, który można wdrożyć, jest ograniczony do 4 rdzeni i 16 GB pamięci RAM.
- Można przydzielić częściowe rdzenie do kontenerów w przyrostach od 0,5 rdzeni, maksymalnie do 6 rdzeni.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Jak długo mogę pozostawić wdrożoną aplikację?

Obecnie ograniczyliśmy okres ważności aplikacji do dwóch dni. Ma to na celu zmaksymalizowanie wykorzystania wolnych rdzeni przydzielonych do podglądu. W rezultacie można uruchamiać dane wdrożenie w sposób ciągły tylko przez 48 godzin, po czym zostanie ono zamknięte.

Jeśli widzisz to się stało, można sprawdzić, czy system `az mesh app show` zamknął go, uruchamiając polecenie w wierszu polecenia platformy Azure. Sprawdź, czy zwraca`"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Przykład: 

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

Aby usunąć grupę zasobów, użyj tego `az group delete <nameOfResourceGroup>` polecenia.

## <a name="deployments"></a>Wdrożenia

### <a name="what-container-images-are-supported"></a>Jakie obrazy kontenerów są obsługiwane?

Jeśli tworzysz na komputerze z aktualizacją Windows Fall Creators Update (wersja 1709), możesz używać tylko obrazów doklin systemu Windows w wersji 1709.

Jeśli tworzysz na komputerze z aktualizacją systemu Windows 10 kwietnia 2018 r. (wersja 1803), możesz użyć obrazów dokowane systemu Windows w wersji 1709 lub Windows w wersji 1803.

Do wdrażania usług można użyć następujących obrazów systemu operacyjnego kontenera:
- Windows - windowsservercore i nanoserver
    - System Windows Server 1709
    - System Windows Server 1803
    - System Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Brak znanych ograniczeń

> [!NOTE]
> Narzędzia programu Visual Studio dla programu Mesh nie obsługują jeszcze wdrażania w kontenerach systemu Windows Server 2019 i 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Jakie typy aplikacji można wdrożyć? 

Można wdrożyć wszystko, co działa w kontenerach, które mieszczą się w ograniczeniach nałożonych na zasób aplikacji (zobacz powyżej, aby uzyskać więcej informacji na temat przydziałów). Jeśli wykryjemy, że używasz usługi Mesh do uruchamiania nielegalnych obciążeń lub nadużywania systemu (tj. górnictwa), zastrzegamy sobie prawo do zakończenia wdrożeń i zablokowania subskrypcji z uruchamiania w usłudze. Skontaktuj się z nami, jeśli masz jakieś pytania dotyczące uruchamiania określonego obciążenia. 

## <a name="developer-experience-issues"></a>Problemy z doświadczeniem programisty

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Rozpoznawanie DNS z kontenera nie działa

Wychodzące zapytania DNS z kontenera do usługi DNS sieci szkieletowej usług sieci szkieletowych mogą w pewnych okolicznościach zakończyć się niepowodzeniem. To jest badane. Aby ograniczyć:

- Jako podstawowy obraz kontenera użyj aktualizacji Windows Fall Creators (wersja 1709) lub nowszej.
- Jeśli sama nazwa usługi nie działa, spróbuj w pełni kwalifikowaną nazwę: ServiceName.ApplicationName.
- W pliku platformy Docker dla `EXPOSE <port>` usługi dodaj, gdzie port jest portem, na którym ujawniasz usługę. Przykład:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>System DNS nie działa tak samo jak w przypadku klastrów deweloperskich sieci szkieletowej usług i w usłudze Mesh

Może być konieczne odwołanie się do usług inaczej w lokalnym klastrze deweloperów niż w usłudze Azure Mesh.

W lokalnym klastrze `{serviceName}.{applicationName}`rozwoju użyj . W usłudze Azure `{servicename}`Service Fabric Mesh użyj programu . 

Usługa Azure Mesh nie obsługuje obecnie rozpoznawania nazw DNS w różnych aplikacjach.

Aby uzyskać inne znane problemy z systemem DNS z uruchamianiem klastra programistycznego sieci szkieletowej usług w systemie Windows 10, zobacz: [Debugowanie kontenerów systemu Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) i [znane problemy z systemem DNS](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Networking

ServiceFabric sieci NAT może zniknąć podczas korzystania z aplikacji na komputerze lokalnym. Aby zdiagnozować, czy tak się stało, uruchom następujące czynności z wiersza polecenia:

`docker network ls`i zanotuj, czy `servicefabric_nat` jest na liście.  Jeśli nie, uruchom następujące polecenie:`docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Rozwiąż ten problem, nawet jeśli aplikacja jest już wdrożona lokalnie i w stanie złej kondycji.

### <a name="issues-running-multiple-apps"></a>Problemy z uruchamianiem wielu aplikacji

Może wystąpić dostępność procesora CPU i limity są stałe we wszystkich aplikacjach. Aby ograniczyć:
- Utwórz klaster pięciu węzłów.
- Zmniejsz użycie procesora CPU w usługach w aplikacji, która jest wdrażana. Na przykład w pliku service.yaml usługi `cpu: 1.0` zmień na`cpu: 0.5`

Wielu aplikacji nie można wdrożyć w klastrze z jednym węzłem. Aby ograniczyć:
- Użyj klastra pięciu węzłów podczas wdrażania wielu aplikacji w klastrze lokalnym.
- Usuń aplikacje, których obecnie nie testujesz.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>Narzędzie VS ma ograniczoną obsługę kontenerów systemu Windows

Narzędzia programu Visual Studio obsługują tylko wdrażanie kontenerów systemu Windows z podstawową wersją systemu operacyjnego systemu Windows Server 1709 i 1803 już dziś. 

## <a name="feature-gaps-and-other-known-issues"></a>Luki w funkcjach i inne znane problemy

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Po wdrożeniu aplikacji skojarzony z nią zasób sieciowy nie ma adresu IP

Istnieje znany problem, w którym adres IP nie staje się natychmiast dostępny. Sprawdź stan zasobu sieciowego w ciągu kilku minut, aby zobaczyć skojarzony adres IP.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Moja aplikacja nie może uzyskać dostępu do odpowiedniego zasobu sieciowego/woluminu

W modelu aplikacji użyj pełnego identyfikatora zasobu dla sieci i woluminów, aby uzyskać dostęp do skojarzonego zasobu. Oto przykład z przykładu szybkiego startu:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Gdy skaluję w poziomie, dotyczy to wszystkich moich kontenerów, w tym uruchomionych

Jest to błąd i poprawka jest implementowana.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sieci szkieletowej usług, przeczytaj [omówienie](service-fabric-mesh-overview.md).
