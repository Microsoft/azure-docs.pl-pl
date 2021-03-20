---
title: Tworzenie aplikacji kontenera Service Fabric platformy Azure
description: Utwórz pierwszą aplikację kontenera systemu Windows w usłudze Azure Service Fabric. Tworzenie obrazu platformy Docker za pomocą aplikacji w języku Python, wypchnięcie obrazu do rejestru kontenerów, a następnie skompilowanie i wdrożenie kontenera na platformie Azure Service Fabric.
ms.topic: conceptual
ms.date: 01/25/2019
ms.custom: devx-track-python
ms.openlocfilehash: 197423670ffe05f15fdc5bfd351efdfba33b53cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533778"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Uruchomienie istniejącej aplikacji w kontenerze systemu Windows w klastrze usługi Service Fabric nie wymaga dokonywania żadnych zmian w aplikacji. W tym artykule opisano tworzenie obrazu platformy Docker zawierającego aplikację sieci Web z [kolbą](http://flask.pocoo.org/) Python i wdrażanie jej w klastrze Service Fabric platformy Azure. Będziesz również udostępniać aplikację skonteneryzowaną za pomocą usługi [Azure Container Registry](../container-registry/index.yml). W tym artykule przyjęto założenie, że masz podstawową wiedzą dotyczącą platformy Docker. Aby uzyskać informacje dotyczące platformy Docker, przeczytaj artykuł [Docker Overview](https://docs.docker.com/engine/understanding-docker/) (Przegląd platformy Docker).

> [!NOTE]
> Ten artykuł ma zastosowanie do środowiska Windows Development.  Środowisko uruchomieniowe klastra Service Fabric i środowisko uruchomieniowe platformy Docker musi być uruchomione w tym samym systemie operacyjnym.  Nie można uruchamiać kontenerów systemu Windows w klastrze z systemem Linux.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Komputer dewelopera z następującym oprogramowaniem:
  * Visual Studio 2015 lub Visual Studio 2019.
  * [Service Fabric zestaw SDK i narzędzia](service-fabric-get-started.md).
  *  Program Docker dla systemu Windows. [Pobierz program Docker CE dla systemu Windows (wersja stabilna)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Po zainstalowaniu i uruchomieniu programu Docker kliknij prawym przyciskiem myszy jego ikonę na pasku zadań i wybierz pozycję **Switch to Windows containers** (Przełącz na kontenery systemu Windows). Ten krok jest wymagany do uruchomienia obrazów platformy Docker opartych na systemie Windows.

* Klaster systemu Windows z co najmniej trzema węzłami działającymi w systemie Windows Server z kontenerami. 

  W tym artykule wersja (kompilacja) systemu Windows Server z kontenerami działającymi w węzłach klastra musi być zgodna z tym na komputerze deweloperskim. Wynika to z faktu, że tworzysz obraz platformy Docker na komputerze deweloperskim, a istnieją ograniczenia zgodności między wersjami systemu operacyjnego kontenera i systemem operacyjnym hosta, na którym został wdrożony. Aby uzyskać więcej informacji, zobacz [Windows Server Container system operacyjny i zgodność systemu operacyjnego hosta](#windows-server-container-os-and-host-os-compatibility). 
  
    Aby określić wersję systemu Windows Server z kontenerami, które są potrzebne dla klastra, uruchom `ver` polecenie w wierszu polecenia systemu Windows na komputerze deweloperskim. Przed [utworzeniem klastra](service-fabric-cluster-creation-via-portal.md)zapoznaj się z tematem system [operacyjny kontenera systemu Windows Server i zgodność systemu operacyjnego](#windows-server-container-os-and-host-os-compatibility) .

* Rejestr w usłudze Azure Container Registry — [utwórz rejestr kontenera](../container-registry/container-registry-get-started-portal.md) w subskrypcji platformy Azure.

> [!NOTE]
> Obsługiwane jest wdrażanie kontenerów w klastrze Service Fabric uruchomionym w systemie Windows 10.  Zapoznaj się z [tym artykułem](service-fabric-how-to-debug-windows-containers.md) , aby uzyskać informacje na temat konfigurowania systemu Windows 10 do uruchamiania kontenerów systemu Windows.

> [!NOTE]
> Service Fabric wersje 6,2 i nowsze obsługują wdrażanie kontenerów w klastrach z systemem Windows Server w wersji 1709.

## <a name="define-the-docker-container"></a>Definiowanie kontenera platformy Docker

Zbuduj obraz na podstawie [obrazu Python](https://hub.docker.com/_/python/) znajdującego się w usłudze Docker Hub.

Określ kontener platformy Docker w pliku Dockerfile. Plik Dockerfile zawiera instrukcje dotyczące konfigurowania środowiska wewnątrz kontenera, ładowania aplikacji do uruchomienia i mapowania portów. Plik Dockerfile stanowi dane wejściowe dla polecenia `docker build`, za pomocą którego jest tworzony obraz.

Utwórz pusty katalog, a następnie utwórz plik *Dockerfile* (bez rozszerzenia). Dodaj następujący kod do pliku *Dockerfile* i zapisz zmiany:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Przeczytaj artykuł [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) (Informacje o pliku Dockerfile), aby uzyskać więcej informacji.

## <a name="create-a-basic-web-application"></a>Tworzenie podstawowej aplikacji internetowej
Utwórz aplikację internetową platformy Flask, która nasłuchuje na porcie 80 i zwraca wartość `Hello World!`. W tym samym katalogu utwórz plik *requirements.txt*. Dodaj następujący kod i zapisz zmiany:
```
Flask
```

Utwórz również plik *app.py* i dodaj następujący fragment:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>

## <a name="login-to-docker-and-build-the-image"></a>Zaloguj się do platformy Docker i skompiluj obraz

Następnie utworzymy obraz z uruchomioną aplikacją sieci Web. Podczas ściągania obrazów publicznych z platformy Docker (podobnie jak `python:2.7-windowsservercore` w przypadku naszych pliku dockerfile) najlepszym rozwiązaniem jest uwierzytelnienie przy użyciu konta usługi Docker Hub zamiast anonimowego żądania ściągnięcia.

> [!NOTE]
> Podczas przeprowadzania często anonimowych żądań ściągnięcia można zobaczyć błędy platformy Docker podobne do programu `ERROR: toomanyrequests: Too Many Requests.` lub `You have reached your pull rate limit.` uwierzytelnić się w usłudze Docker Hub, aby uniknąć tych błędów. Aby uzyskać więcej informacji, zobacz [Zarządzanie zawartością publiczną za pomocą Azure Container Registry](../container-registry/buffer-gate-public-content.md) .

Otwórz okno programu PowerShell i przejdź do katalogu zawierającego plik Dockerfile. Następnie uruchom następujące polecenia:

```
docker login
docker build -t helloworldapp .
```

To polecenie powoduje utworzenie nowego obrazu zgodnie z instrukcjami z pliku Dockerfile i nadanie obrazowi nazwy `helloworldapp` (dzięki zastosowaniu parametru tagowania -t). W celu utworzenia obrazu kontenera obraz podstawowy jest najpierw pobierany z usługi Docker Hub, do której jest dodawana aplikacja. 

Po zakończeniu działania polecenia budowania uruchom polecenie `docker images`, aby wyświetlić informacje o nowym obrazie:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Lokalne uruchamianie aplikacji
Przed wypchnięciem obrazu do rejestru kontenerów należy lokalnie sprawdzić jego działanie. 

Uruchom aplikację:

```
docker run -d --name my-web-site helloworldapp
```

Parametr *name* umożliwia nadanie nazwy działającemu kontenerowi (zamiast identyfikatora kontenera).

Po uruchomieniu kontenera znajdź jego adres IP, dzięki czemu będzie można połączyć się z działającym kontenerem za pomocą przeglądarki:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Jeśli to polecenie nie zwróci żadnej wartości, uruchom następujące polecenie i sprawdź, czy element **NetworkSettings** -> **Networks** dla adresu IP:
```
docker inspect my-web-site
```

Nawiąż połączenie z działającym kontenerem. Otwórz przeglądarkę internetową, wskazując na zwrócony adres IP, na przykład "http: \/ /172.31.194.61". W przeglądarce powinien zostać wyświetlony nagłówek „Hello World!”.

Aby zatrzymać kontener, uruchom polecenie:

```
docker stop my-web-site
```

Usuń kontener z komputera dewelopera:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Wypychanie obrazu do rejestru kontenerów

Po sprawdzeniu, że kontener działa na komputerze dewelopera, wypchnij obraz do rejestru w usłudze Azure Container Registry.

Uruchom ``docker login`` , aby zalogować się do rejestru kontenerów przy użyciu [poświadczeń rejestru](../container-registry/container-registry-authentication.md).

Poniższy przykład przekazuje identyfikator i hasło [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Na przykład nazwę główną usługi można było przypisać do rejestru dla scenariusza automatyzacji. Możesz też zalogować się przy użyciu nazwy użytkownika i hasła do rejestru.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Następujące polecenie tworzy tag lub alias obrazu z w pełni kwalifikowaną ścieżką do Twojego rejestru. W tym przykładzie obraz jest umieszczany w przestrzeni nazw ```samples```, aby uniknąć zaśmiecania katalogu głównego rejestru.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Wypchnij obraz do rejestru kontenerów:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Tworzenie konteneryzowanej usługi w programie Visual Studio
Zestaw SDK oraz narzędzia usługi Service Fabric udostępniają szablon usługi ułatwiający utworzenie konteneryzowanej aplikacji.

1. Uruchom program Visual Studio. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.
2. Wybierz pozycję **Aplikacja usługi Service Fabric**, nadaj jej nazwę „MyFirstContainer” i kliknij przycisk **OK**.
3. Z listy **szablonów usług** wybierz pozycję **Kontener**.
4. W polu **Nazwa obrazu** wprowadź „myregistry.azurecr.io/samples/helloworldapp”. Jest to obraz wypchnięty do repozytorium kontenerów.
5. Nadaj nazwę usłudze i kliknij przycisk **OK**.

## <a name="configure-communication"></a>Konfigurowanie komunikacji
Konteneryzowana usługa wymaga punktu końcowego dla celów komunikacyjnych. Dodaj element `Endpoint` z protokołem, portem i typem do pliku ServiceManifest.xml. W tym przykładzie jest używany stały port 8081. Jeśli nie określono portu, zostanie wybrany losowy port z zakresu portów aplikacji. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Dodatkowe punkty końcowe dla usługi mogą być dodawane przez zadeklarowanie dodatkowych elementów punktu końcowego z odpowiednimi wartościami właściwości. Każdy port może deklarować tylko jedną wartość protokołu.

Definiując punkt końcowy, usługa Service Fabric publikuje punkt końcowy w usłudze Naming. Inne usługi działające w klastrze mogą rozpoznać ten kontener. Komunikację między kontenerami można również realizować za pomocą [odwrotnego serwera proxy](service-fabric-reverseproxy.md). Aby zrealizować komunikację, należy podać port nasłuchujący HTTP odwrotnego serwera proxy i nazwę usług, z którymi chcesz się komunikować, jako zmienne środowiskowe.

Usługa nasłuchuje na określonym porcie (w tym przykładzie 8081). Gdy aplikacja jest wdrażana w klastrze na platformie Azure, klaster i aplikacja są uruchamiane za modułem równoważenia obciążenia platformy Azure. Port aplikacji musi być otwarty w module równoważenia obciążenia platformy Azure, tak aby ruch przychodzący mógł przechodzić do usługi.  Możesz otworzyć ten port w module równoważenia obciążenia platformy Azure za pomocą [skryptu programu PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) lub [witryny Azure Portal](https://portal.azure.com).

## <a name="configure-and-set-environment-variables"></a>Konfigurowanie i ustawianie zmiennych środowiskowych
Zmienne środowiskowe można określić dla każdego pakietu kodu w manifeście usługi. Ta funkcja jest dostępna dla wszystkich usług niezależnie od tego, czy są one wdrażane jako kontenery, procesy, czy pliki wykonywalne gościa. Wartości zmiennych środowiskowych można przesłonić w manifeście aplikacji lub podać je podczas wdrażania jako parametry aplikacji.

Następujący fragment kodu XML manifestu usługi stanowi przykład sposobu określania zmiennych środowiskowych dla pakietu kodu:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Te zmienne środowiskowe można przesłonić w manifeście aplikacji:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Konfigurowanie mapowania portów kontenera typu port do hosta i odnajdywania między kontenerami
Skonfiguruj port hosta używany do komunikacji z kontenerem. Powiązanie portu mapuje port, na którym nasłuchuje usługa wewnątrz kontenera, na port na hoście. Dodaj element `PortBinding` w elemencie `ContainerHostPolicies` pliku ApplicationManifest.xml. W tym artykule wartość portu dla parametru `ContainerPort` to 80 (kontener uwidacznia port 80, jak określono w pliku Dockerfile), a wartością parametru `EndpointRef` jest „Guest1TypeEndpoint” (punkt końcowy zdefiniowany wcześniej w manifeście usługi). Żądania przychodzące do usługi na porcie 8081 są mapowane na port 80 w kontenerze.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> Dodatkowe PortBindings dla usługi mogą być dodawane przez zadeklarowanie dodatkowych elementów Portbinding z odpowiednimi wartościami właściwości.

## <a name="configure-container-repository-authentication"></a>Konfigurowanie uwierzytelniania repozytorium kontenerów

Zobacz [uwierzytelnianie repozytorium kontenerów](configure-container-repository-credentials.md), aby dowiedzieć się, jak skonfigurować różne typy uwierzytelniania na potrzeby pobierania obrazów kontenerów.

## <a name="configure-isolation-mode"></a>Konfigurowanie trybu izolacji
System Windows obsługuje dwa tryby izolacji dla kontenerów: tryb procesu oraz tryb funkcji Hyper-V. W trybie izolacji procesu wszystkie kontenery działające na tym samym hoście współdzielą jądro z hostem. W trybie izolacji funkcji Hyper-V jądra są odizolowane dla każdego kontenera funkcji Hyper-V i hosta kontenera. Tryb izolacji można określić w elemencie `ContainerHostPolicies` pliku manifestu aplikacji. Tryby izolacji, które można określić, to `process`, `hyperv` i `default`. Domyślnie tryb izolacji procesu na hostach z systemem Windows Server. Na hostach z systemem Windows 10 obsługiwana jest tylko tryb izolacji funkcji Hyper-V, więc kontener jest uruchamiany w trybie izolacji funkcji Hyper-V niezależnie od jego ustawień trybu izolacji. Poniższy fragment kodu przedstawia sposób określania trybu izolacji w pliku manifestu aplikacji.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > Tryb izolacji funkcji Hyper-V jest dostępny na jednostkach magazynowych Azure serii Ev3 i Dv3, które obsługują wirtualizację zagnieżdżoną. 
   >
   >

## <a name="configure-resource-governance"></a>Konfigurowanie zarządzania zasobami
[Zarządzanie zasobami](service-fabric-resource-governance.md) ogranicza zasoby, z których kontener może korzystać na hoście. Element `ResourceGovernancePolicy` określany w manifeście aplikacji służy do deklarowania limitów zasobów pakietu kodu usługi. Limity można ustawić dla następujących zasobów: Memory, MemorySwap, CpuShares (CPU — względna waga), MemoryReservationInMB, BlkioWeight (BlockIO — względna waga). W tym przykładzie pakiet usług Guest1Pkg otrzymuje dostęp do jednego rdzenia w węzłach klastra, w których jest umieszczony. Limity pamięci są bezwzględne, więc pakiet kodu jest ograniczony do 1024 MB pamięci (i ma rezerwację tej samej ilości ze wstępną gwarancją). Pakiety kodu (kontenery lub procesy) nie mogą przydzielać pamięci ponad ten limit. Podjęcie próby takiego przydzielenia spowoduje wyjątek braku pamięci. Aby wymuszanie limitu zasobów działało, wszystkie pakiety kodu w ramach pakietu usług powinny mieć określone limity pamięci.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Konfigurowanie funkcji HEALTHCHECK platformy Docker 

Począwszy od wersji 6.1, usługa Service Fabric automatycznie integruje zdarzenia [funkcji HEALTHCHECK platformy Docker](https://docs.docker.com/engine/reference/builder/#healthcheck) z raportem o kondycji systemu. Oznacza to, że jeśli w kontenerze włączono funkcję **HEALTHCHECK**, usługa Service Fabric będzie raportować kondycję przy każdej zmianie stanu kondycji kontenera zgłoszonej przez platformę Docker. Raport kondycji **OK** pojawi się w narzędziu [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), gdy wartość *health_status* będzie równa *healthy*, a raport **OSTRZEŻENIE** pojawi się, gdy wartość *health_status* będzie równa *unhealthy*. 

Począwszy od najnowszej wersji odświeżania programu v 6.4, można określić, że oceny Docker HEALTHCHECK powinny być zgłaszane jako błąd. Jeśli ta opcja jest włączona, zostanie wyświetlony **Raport kondycji prawidłowy,** gdy *health_status* jest w *dobrej* kondycji i pojawi się **komunikat o błędzie** , gdy *health_status* jest w złej *kondycji*.

Instrukcja **HEALTHCHECK** wskazująca rzeczywisty test wykonywany w celu monitorowania kondycji kontenera musi występować w pliku Dockerfile używanym podczas generowania obrazu kontenera.

![Zrzut ekranu przedstawia szczegóły wdrożonego pakietu usługi NodeServicePackage.][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

Możesz skonfigurować zachowanie funkcji **HEALTHCHECK** dla każdego kontenera, określając opcje **HealthConfig** jako część sekcji **ContainerHostPolicies** w pliku ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Domyślnie *IncludeDockerHealthStatusInSystemHealthReport* ma wartość **true**, *wartość restartcontaineronunhealthydockerhealthstatus* ma wartość **false**, a *TreatContainerUnhealthyStatusAsError* jest ustawiona na **wartość false**. 

Jeśli wartość *RestartContainerOnUnhealthyDockerHealthStatus* jest ustawiona na **true**, kontener wielokrotnie raportujący złą kondycję jest uruchamiany ponownie (potencjalnie w innych węzłach).

Jeśli *TreatContainerUnhealthyStatusAsError* ma **wartość true**, raporty kondycji **błędów** pojawią się, gdy *health_status* kontenera jest w *złej kondycji*.

Aby wyłączyć integrację funkcji **HEALTHCHECK** dla całego klastra usługi Service Fabric, należy ustawić wartość [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) na **false**.

## <a name="deploy-the-container-application"></a>Wdrażanie aplikacji kontenera
Zapisz wszystkie zmiany i skompiluj aplikację. Aby opublikować aplikację, w Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł **MyFirstContainer** i wybierz pozycję **Publikuj**.

W obszarze **Punkt końcowy połączenia** wprowadź punkt końcowy zarządzania dla klastra. Na przykład `containercluster.westus2.cloudapp.azure.com:19000`. Punkt końcowy połączenia klienta można znaleźć na karcie Przegląd klastra w witrynie [Azure Portal](https://portal.azure.com).

Kliknij przycisk **Opublikuj**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) to oparte na sieci Web narzędzie do sprawdzania aplikacji i węzłów oraz zarządzania nimi w klastrze usługi Service Fabric. Otwórz przeglądarkę i przejdź do adresu `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/`, a następnie wykonaj procedurę wdrażania aplikacji. Aplikacja zostanie wdrożona, ale pozostanie w stanie błędu do czasu pobrania obrazu na węzły klastra (co może zająć trochę czasu w zależności od rozmiaru obrazu): ![Błąd][1]

Aplikacja jest gotowa, gdy ma stan ```Ready```: ![Gotowa][2]

Otwórz przeglądarkę i przejdź pod adres `http://containercluster.westus2.cloudapp.azure.com:8081`. W przeglądarce powinien zostać wyświetlony nagłówek „Hello World!”.

## <a name="clean-up"></a>Czyszczenie

Jeśli podczas działania klastra nadal są naliczane opłaty, rozważ [usunięcie klastra](./service-fabric-tutorial-delete-cluster.md).

Po wypchnięciu obrazu do rejestru kontenerów można usunąć lokalny obraz z komputera dewelopera:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>System operacyjny kontenera systemu Windows Server i zgodność systemu operacyjnego hosta

Kontenery systemu Windows Server nie są zgodne ze wszystkimi wersjami systemu operacyjnego hosta. Na przykład:
 
- Kontenery systemu Windows Server utworzone przy użyciu systemu Windows Server w wersji 1709 nie działają na hoście z systemem Windows Server w wersji 2016. 
- Kontenery systemu Windows Server utworzone przy użyciu systemu Windows Server 2016 działają w trybie izolacji funkcji Hyper-V tylko na hoście z systemem Windows Server w wersji 1709. 
- W przypadku kontenerów systemu Windows Server zbudowanych przy użyciu systemu Windows Server 2016 może być konieczne upewnienie się, że wersja systemu operacyjnego kontenera i systemu operacyjnego hosta są takie same, gdy działa w trybie izolacji procesu na hoście z systemem Windows Server 2016.
 
Aby dowiedzieć się więcej, zobacz [zgodność wersji kontenera systemu Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Podczas kompilowania i wdrażania kontenerów w klastrze Service Fabric należy wziąć pod uwagę zgodność systemu operacyjnego hosta i systemu operacyjnego kontenera. Na przykład:

- Upewnij się, że wdrożono kontenery z systemem operacyjnym zgodnym z systemem operacyjnym w węzłach klastra.
- Upewnij się, że tryb izolacji określony dla aplikacji kontenera jest zgodny z obsługą systemu operacyjnego kontenera w węźle, w którym jest wdrażany.
- Zastanów się, jak uaktualnienia systemu operacyjnego do węzłów klastra lub kontenerów mogą mieć wpływ na ich zgodność. 

Zalecamy następujące metody, aby upewnić się, że kontenery są poprawnie wdrożone w klastrze Service Fabric:

- Użyj jawnego tagowania obrazu z obrazami platformy Docker, aby określić wersję systemu operacyjnego Windows Server, z którego jest zbudowany kontener. 
- Użyj [tagowania systemu operacyjnego](#specify-os-build-specific-container-images) w pliku manifestu aplikacji, aby upewnić się, że aplikacja jest zgodna z różnymi wersjami i uaktualnieniami systemu Windows Server.

> [!NOTE]
> Za pomocą Service Fabric w wersji 6,2 i nowszych można wdrożyć kontenery oparte na systemie Windows Server 2016 lokalnie na hoście z systemem Windows 10. W systemie Windows 10 kontenery są uruchamiane w trybie izolacji funkcji Hyper-V, niezależnie od trybu izolacji ustawionego w manifeście aplikacji. Aby dowiedzieć się więcej, zobacz [Konfigurowanie trybu izolacji](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>Określanie obrazów kontenera dla kompilacji systemu operacyjnego 

Kontenery systemu Windows Server mogą być niezgodne z różnymi wersjami systemu operacyjnego. Na przykład kontenery systemu Windows Server utworzone przy użyciu systemu Windows Server 2016 nie działają w systemie Windows Server w wersji 1709 w trybie izolacji procesu. W związku z tym, jeśli węzły klastra są aktualizowane do najnowszej wersji, usługi kontenerów utworzone przy użyciu wcześniejszych wersji systemu operacyjnego mogą zakończyć się niepowodzeniem. Aby obejść ten element przy użyciu wersji 6,1 środowiska uruchomieniowego i nowszego, Service Fabric obsługuje określanie wielu obrazów systemu operacyjnego na kontener i tagowanie ich z wersjami kompilacji systemu operacyjnego w manifeście aplikacji. Wersję kompilacji systemu operacyjnego można uzyskać, uruchamiając `winver` polecenie w wierszu polecenia systemu Windows. Przed zaktualizowaniem systemu operacyjnego na węzłach najpierw zaktualizuj manifesty aplikacji i określ elementy przesłaniające obrazy dla poszczególnych wersji systemu operacyjnego. Poniższy fragment kodu przedstawia, w jaki sposób określić wiele obrazów kontenera w manifeście aplikacji **ApplicationManifest.xml**:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
Wersja kompilacji dla systemu Windows Server 2016 to 14393, a dla systemu Windows Server w wersji 1709 — 16299. Manifest usługi wciąż określa tylko jeden obraz na usługę kontenera, jak przedstawiono poniżej:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Funkcja oznaczania wersji kompilacji systemu operacyjnego jest dostępna tylko dla usługi Service Fabric w systemie Windows
   >

Jeśli wersja kompilacji podstawowego systemu operacyjnego na maszynie wirtualnej to 16299 (wersja 1709), usługa Service Fabric wybiera obraz kontenera odpowiadający tej wersji systemu Windows Server. Jeśli w manifeście aplikacji wraz z oznakowanymi obrazami kontenera dostarczany jest nieoznakowany obraz kontenera, usługa Service Fabric traktuje nieoznakowany obraz jako ten, która działa w różnych wersjach. Otaguj jawnie obrazy kontenera, aby uniknąć problemów podczas uaktualniania.

Nieotagowany obraz kontenera będzie działać jako przesłonięcie względem obrazu podanego w pliku ServiceManifest. Dlatego obraz „myregistry.azurecr.io/samples/helloworldappDefault” spowoduje przesłonięcie obrazu o nazwie „myregistry.azurecr.io/samples/helloworldapp” w pliku ServiceManifest.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Kompletny przykład aplikacji i manifestów usługi Service Fabric
W tym miejscu przedstawiono kompletne manifesty usługi i aplikacji używane w tym artykule.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Konfigurowanie interwału czasu wymuszania przerwania działania kontenera

Możesz skonfigurować interwał czasu środowiska uruchomieniowego, po upływie którego kontener ma zostać usunięty po rozpoczęciu usuwania usługi (lub przenoszenia do innego węzła). Skonfigurowanie interwału czasu powoduje wysłanie polecenia `docker stop <time in seconds>` do kontenera.  Aby uzyskać więcej informacji, zobacz [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Interwał czasu oczekiwania jest określony w sekcji `Hosting`. `Hosting`Sekcję można dodać podczas tworzenia klastra lub później w ramach uaktualnienia konfiguracji. W poniższym fragmencie manifestu klastra pokazano, jak ustawić interwał oczekiwania:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
    }
]
```
Domyślny interwał to 10 sekund. Ta konfiguracja jest dynamiczna, dlatego uaktualnienie samej konfiguracji w klastrze powoduje zaktualizowanie limitu czasu. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Konfigurowanie środowiska uruchomieniowego w celu usunięcia nieużywanych obrazów kontenerów

Możesz skonfigurować klaster usługi Service Fabric w celu usuwania nieużywanych obrazów kontenerów z węzła. Ta konfiguracja pozwala na ponowne przechwycenie miejsca na dysku w przypadku zbyt wielu obrazów kontenerów w węźle. Aby włączyć tę funkcję, zaktualizuj sekcję [hostingu](service-fabric-cluster-fabric-settings.md#hosting) w manifeście klastra, jak pokazano w poniższym fragmencie kodu: 


```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
    } 
]
```

Jeśli nie chcesz usuwać pewnych obrazów, określ je przy użyciu parametru `ContainerImagesToSkip`.  


## <a name="configure-container-image-download-time"></a>Konfigurowanie czasu pobierania obrazów kontenerów

Środowisko uruchomieniowe usługi Service Fabric przydziela 20 minut na pobieranie i wyodrębnianie obrazów kontenerów, co sprawdza się w przypadku większości obrazów kontenerów. W przypadku dużych obrazów lub wolnego połączenia sieciowego może być konieczne wydłużenie czasu oczekiwania przed przerwaniem pobierania i wyodrębniania obrazu. Ten limit czasu można ustawić przy użyciu atrybutu **ContainerImageDownloadTimeout** w sekcji **Hosting** manifestu klastra, jak pokazano w poniższym fragmencie kodu:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Ustawianie zasad przechowywania kontenerów

Aby ułatwić diagnozowanie błędów uruchamiania kontenerów, usługa Service Fabric (w wersji 6.1 lub nowszej) obsługuje przechowywanie kontenerów, w przypadku których działanie zostało przerwane lub uruchamianie nie powiodło się. Te zasady można ustawić w pliku **ApplicationManifest.xml**, jak pokazano w poniższym fragmencie kodu:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Ustawienie **ContainersRetentionCount** określa liczbę kontenerów do przechowywania w przypadku wystąpienia w nich błędu. Jeśli zostanie określona wartość ujemna, będą przechowywane wszystkie kontenery z błędami. Jeśli atrybut **ContainersRetentionCount** nie zostanie określony, nie będą przechowywane żadne kontenery. Atrybut **ContainersRetentionCount** obsługuje też parametry aplikacji, dzięki czemu użytkownicy mogą określać różne wartości dla klastrów testowych i produkcyjnych. W przypadku używania tej funkcji można zastosować ograniczenia rozmieszczania, tak aby obiektem docelowym usługi kontenera był określony węzeł, co zapobiega przenoszeniu usługi kontenera do innych węzłów. Wszelkie kontenery przechowywane przy użyciu tej funkcji należy usunąć ręcznie.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Uruchamianie demona platformy Docker z niestandardowymi argumentami

W środowisku uruchomieniowym usługi Service Fabric w wersji 6.2 lub nowszej można uruchomić demona platformy Docker z niestandardowymi argumentami. Jeśli określono niestandardowe argumenty, usługa Service Fabric nie przekaże żadnego innego argumentu do aparatu platformy Docker z wyjątkiem argumentu `--pidfile`. Dlatego nie należy przekazywać elementu `--pidfile` jako argumentu. Ponadto argument powinien nadal mieć włączone nasłuchiwanie przez demona platformy Docker w domyślnym potoku nazw w systemie Windows (lub w gnieździe domeny systemu Unix w systemie Linux), aby umożliwić komunikację usługi Service Fabric z demonem. Niestandardowe argumenty są przekazywane w manifeście klastra w sekcji **Hosting** w obszarze **ContainerServiceArguments**, jak pokazano w następującym fragmencie kodu: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-containers-overview.md).
* Zapoznaj się z samouczkiem [Deploy a .NET application in a container](service-fabric-host-app-in-a-container.md) (Wdrażanie aplikacji .NET w kontenerze).
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.
* Sprawdź [przykłady kodu kontenera usługi Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) w witrynie GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
