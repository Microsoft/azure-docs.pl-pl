---
title: Wdrażanie aplikacji .NET w kontenerze na platformie Azure Service Fabric
description: Dowiedz się, jak konteneryzować istniejącą aplikację platformy .NET przy użyciu programu Visual Studio i jak debugować kontenery lokalnie w usłudze Service Fabric. Konteneryzowana aplikacja jest wypychana do usługi Azure Container Registry i wdrażana w klastrze usługi Service Fabric. Po wdrożeniu na platformie Azure aplikacja utrwala dane za pomocą usługi Azure SQL DB.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: 7c77b2e5b60aef246b513cb852f6231ba7531056
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743815"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Samouczek: wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric

W tym samouczku przedstawiono sposób konteneryzacji istniejącej aplikacji ASP.NET i tworzenia z niej pakietu będącego aplikacją usługi Service Fabric.  Uruchom kontenery lokalnie w klastrze programistycznym usługi Service Fabric, a następnie wdróż aplikację na platformie Azure.  Aplikacja będzie utrwalać dane w usłudze [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Konteneryzowanie istniejącej aplikacji za pomocą programu Visual Studio
> * Tworzenie bazy danych w Azure SQL Database
> * Tworzenie rejestru kontenerów platformy Azure
> * Wdrażanie aplikacji usługi Service Fabric na platformie Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. Zainstaluj program [Docker CE dla systemu Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), który umożliwia uruchamianie kontenerów w systemie Windows 10.
3. Zainstaluj [środowisko uruchomieniowe usługi Service Fabric w wersji 6.2 lub nowszej](service-fabric-get-started.md) i [zestaw SDK usługi Service Fabric w wersji 3.1](service-fabric-get-started.md) lub nowszej.
4. Zainstaluj [program Visual Studio 2019 w wersji 16,1](https://www.visualstudio.com/) lub nowszej przy użyciu obciążeń deweloperskich i **ASP.NET** na **platformie Azure** .
5. Zainstaluj [Azure PowerShell][link-azure-powershell-install]

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Pobieranie i uruchamianie aplikacji Fabrikam Fiber CallCenter

1. Pobierz aplikację przykładową [Fabrikam Fiber CallCenter][link-fabrikam-github].  Kliknij link **pobierania archiwum**.  Z katalogu *sourceCode* w pliku *fabrikam.zip* wyodrębnij plik *sourceCode.zip*, a następnie katalog *VS2015* na komputerze.

2. Sprawdź, czy aplikację Fabrikam Fiber CallCenter można skompilować i uruchomić bez błędów.  Uruchom program Visual Studio jako **administrator**, a następnie otwórz plik [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Naciśnij klawisz F5, aby debugować i uruchomić aplikację.

   ![Zrzut ekranu przedstawiający stronę główną aplikacji Fabrikam Fiber CallCenter działającą na hoście lokalnym. Na stronie zostanie wyświetlony pulpit nawigacyjny z listą wywołań obsługi.][fabrikam-web-page]

## <a name="containerize-the-application"></a>Konteneryzowanie aplikacji

1. Kliknij prawym przyciskiem myszy projekt **FabrikamFiber.Web** i wybierz pozycję **Dodaj** > **Obsługa orkiestratora kontenerów**.  Wybierz pozycję **Service Fabric** jako orkiestratora kontenerów i kliknij pozycję **OK**.

2. Jeśli zostanie wyświetlony monit, kliknij przycisk **tak** , aby przełączyć teraz kontenery platformy Docker do systemu Windows.

   Nowy projekt aplikacji usługi Service Fabric **FabrikamFiber.CallCenterApplication** zostanie utworzony w rozwiązaniu.  Plik Dockerfile zostanie dodany do istniejącego projektu **FabrikamFiber.Web**.  Katalog **PackageRoot** także zostanie dodany do projektu **FabrikamFiber.Web**. Zawiera on manifest usługi i ustawienia dla nowej usługi FabrikamFiber.Web.

   Kontener jest teraz gotowy do skompilowania i spakowania w aplikację usługi Service Fabric. Po skompilowaniu obrazu kontenera na komputerze możesz wypchnąć go do dowolnego rejestru kontenerów, a następnie ściągnąć na dowolny host w celu uruchomienia.

## <a name="create-an-azure-sql-db"></a>Tworzenie bazy danych usługi Azure SQL DB

Gdy aplikacja Fabrikam Fiber CallCenter działa w środowisku produkcyjnym, dane muszą być utrwalane w bazie danych. Obecnie nie ma możliwości zagwarantowania trwałości danych w kontenerze, dlatego nie można zapisywać danych produkcyjnych w programie SQL Server w kontenerze.

Zalecamy usługę [Azure SQL Database](../azure-sql/database/powershell-script-content-guide.md). Aby skonfigurować i uruchomić zarządzaną bazę danych SQL Server na platformie Azure, uruchom następujący skrypt.  Zmodyfikuj zmienne skryptu odpowiednio do potrzeb. Element *clientIP* określa adres IP Twojego komputera programistycznego. Zanotuj zwróconą przez skrypt nazwę serwera.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> Jeśli jesteś za firmową zaporą, adres IP komputera programistycznego może nie być adresem IP ujawnianym w Internecie. Aby sprawdzić, czy baza danych ma poprawny adres IP dla reguły zapory, przejdź do witryny [Azure Portal](https://portal.azure.com) i znajdź swoją bazę danych w sekcji Bazy danych SQL. Kliknij jej nazwę, a następnie w sekcji Przegląd kliknij pozycję „Ustaw zaporę serwera”. Parametr „Adres IP klienta” określa adres IP Twojej maszyny programistycznej. Upewnij się, że jest on zgodny z adresem IP w regule „AllowClient”.

## <a name="update-the-web-config"></a>Aktualizowanie konfiguracji internetowej

W projekcie **FabrikamFiber.Web** zaktualizuj parametry połączenia w pliku **web.config**, aby wskazywały na program SQL Server w kontenerze.  Zaktualizuj część *Server* parametrów połączenia do poziomu nazwy serwera utworzonej przez poprzedni skrypt. Powinna to być informacja podobna do „fab-fiber-751718376.database.windows.net”.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>Na potrzeby lokalnego debugowania można używać dowolnego programu SQL Server, ale musi on być dostępny z hosta. Baza danych **localdb** nie obsługuje jednak komunikacji typu `container -> host`. Jeśli podczas tworzenia kompilacji wydania aplikacji internetowej ma zostać użyta inna usługa SQL Database, dodaj kolejne parametry połączenia do pliku *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Uruchamianie aplikacji konteneryzowanych lokalnie

Naciśnij klawisz **F5**, aby uruchomić i debugować aplikację w kontenerze w lokalnym klastrze programistycznym usługi Service Fabric. Kliknij przycisk **Tak**, jeśli zostanie wyświetlone pole komunikatu z prośbą o nadanie grupie „ServiceFabricAllowedUsers” uprawnień do odczytu i wykonywania do katalogu projektu programu Visual Studio.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Teraz, gdy aplikacja działa lokalnie, rozpocznij przygotowania do wdrożenia na platformie Azure.  Obrazy kontenerów muszą być przechowywane w rejestrze kontenerów.  Utwórz usługę [Azure Container Registry](../container-registry/container-registry-intro.md) za pomocą następującego skryptu. Nazwa rejestru kontenera jest widoczna w innych subskrypcjach platformy Azure, dlatego musi być unikatowa.
Przed wdrożeniem aplikacji na platformie Azure wypchnij obraz kontenera do tego rejestru.  Gdy aplikacja jest wdrażana na klastrze platformy Azure, obraz kontenera jest ściągany z tego rejestru.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Tworzenie klastra usługi Service Fabric na platformie Azure

Aplikacje usługi Service Fabric działają w klastrze — połączonym z siecią zestawie maszyn wirtualnych lub fizycznych.  Przed wdrożeniem aplikacji na platformie Azure utwórz klaster usługi Service Fabric na platformie Azure.

Oto co możesz zrobić:

* Utworzyć klaster testowy z poziomu programu Visual Studio. Ta opcja służy do tworzenia bezpiecznego klastra bezpośrednio z poziomu programu Visual Studio z preferowaną konfiguracją.
* [Utworzyć zabezpieczony klaster na podstawie szablonu](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

W ramach tego samouczka klaster jest tworzony za pomocą programu Visual Studio, co jest bardzo przydatne w przypadku scenariuszy testowych. Jeśli utworzono klaster w inny sposób lub jest używany istniejący klaster, możesz skopiować i wkleić punkt końcowy połączenia lub wybrać go z subskrypcji.

Zanim rozpoczniesz, w Eksploratorze rozwiązań otwórz plik FabrikamFiber.Web -> PackageRoot -> ServiceManifest.xml. Zwróć uwagę na numer portu frontonu internetowego podany w polu **Punkt końcowy**.

Podczas tworzenia klastra:

1. Kliknij prawym przyciskiem myszy projekt aplikacji **FabrikamFiber.CallCenterApplication** w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**.
2. Zaloguj się przy użyciu konta platformy Azure, aby uzyskać dostęp do subskrypcji.
3. Wybierz listę rozwijaną **Punkt końcowy połączenia**, a następnie wybierz polecenie **Utwórz nowy klaster...**.
4. W oknie dialogowym **Tworzenie klastra** zmodyfikuj następujące ustawienia:

    a. Określ nazwę klastra w polu **Nazwa klastra**, a także subskrypcję i lokalizację, których chcesz użyć. Zanotuj nazwę grupy zasobów klastra.

    b. Opcjonalnie możesz zmodyfikować liczbę węzłów. Domyślne ustawienie to trzy węzły, czyli minimalna liczba wymagana do testowania scenariuszy usługi Service Fabric.

    c. Wybierz kartę **certyfikat** . Na tej karcie wpisz hasło, które zostanie użyte do zabezpieczenia certyfikatu klastra. Ten certyfikat pomaga zabezpieczyć klaster. Możesz również zmodyfikować ścieżkę, w której ma zostać zapisany certyfikat. Program Visual Studio może również automatycznie zaimportować certyfikat, ponieważ jest to krok wymagany do opublikowania aplikacji w klastrze.

    d. Wybierz kartę **Szczegóły maszyny wirtualnej** . Określ hasło, którego chcesz użyć dla Virtual Machines (VM) tworzącego klaster. Za pomocą nazwy użytkownika i hasła można zdalnie łączyć się z maszynami wirtualnymi. Należy również wybrać rozmiar maszyny wirtualnej oraz zmienić obraz maszyny wirtualnej, jeśli jest to konieczne.

    > [!IMPORTANT]
    > Wybierz jednostkę SKU, która obsługuje uruchomione kontenery. System operacyjny Windows Server na węzłach klastra musi być zgodny z systemem operacyjnym Windows Server kontenera. Aby dowiedzieć się więcej, zobacz temat [Windows Server container OS and host OS compatibility](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility) (Zgodność między systemami operacyjnymi Windows Server kontenera i hosta). Domyślnie w tym samouczku zostanie utworzony obraz platformy Docker opartej na systemie Windows Server 2016 LTSC. Kontenery oparte na tym obrazie zostaną uruchomione na klastrach utworzonych za pomocą Windows Server 2016 Datacenter with Containers. Jednak jeśli utworzysz klaster lub użyjesz istniejącego klastra w oparciu o Windows Server Datacenter Core 1709 with Containers, zmień obraz systemu operacyjnego Windows Server, na którym jest oparty kontener. Otwórz plik **Dockerfile** w projekcie **FabrikamFiber.Web**, usuń komentarz dotyczący istniejącej instrukcji akcji `FROM` (na podstawie `windowsservercore-ltsc`) i usuń komentarz dotyczący instrukcji akcji `FROM` na podstawie `windowsservercore-1709`.

    e. Na karcie **Zaawansowane** wyświetl port aplikacji otwierany w module równoważenia obciążenia przy wdrażaniu klastra. Jest to port zanotowany przed rozpoczęciem tworzenia klastra. Można również dodać istniejący klucz usługi Application Insights, do którego mają być kierowane pliki dziennika aplikacji.

    f. Po zakończeniu modyfikowania ustawień wybierz przycisk **Utwórz**.

5. Tworzenie klastra może zająć kilka minut. Informacja o ukończeniu tworzenia klastra pojawi się w oknie danych wyjściowych.

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Zezwalaj aplikacji na korzystanie z platformy Azure na dostęp do SQL Database

Wcześniej dla usługi SQL została utworzona reguła zapory, która udziela dostępu aplikacji uruchomionej lokalnie.  W następnej kolejności należy włączyć dostęp do usługi SQL DB dla aplikacji uruchomionej na platformie Azure.  Utwórz [punkt końcowy usługi dla sieci wirtualnej](../azure-sql/database/vnet-service-endpoint-rule-overview.md) dla klastra usługi Service Fabric, a następnie utwórz regułę, która umożliwia dostęp do usługi SQL DB temu punktowi końcowemu. Pamiętaj o określeniu zmiennej grupy zasobów klastra zanotowanej podczas tworzenia klastra.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Wdrożenie aplikacji na platformie Azure

Kiedy aplikacja jest gotowa, można wdrożyć ją w klastrze platformy Azure bezpośrednio z programu Visual Studio.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji **FabrikamFiber.CallCenterApplication** i wybierz polecenie **Publikuj**.  W polu **Punkt końcowy połączenia** wybierz punkt końcowy klastra, który został utworzony wcześniej.  W polu **Azure Container Registry** wybierz rejestr kontenerów utworzony wcześniej.  Kliknij polecenie **Publikuj**, aby wdrożyć aplikację w klastrze platformy Azure.

![Publikowanie aplikacji][publish-app]

Śledzenie postępu wdrażania w oknie danych wyjściowych. Gdy aplikacja jest wdrażana, otwórz przeglądarkę, a następnie wpisz adres klastra i port aplikacji. Na przykład `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Zrzut ekranu przedstawiający stronę główną aplikacji Fabrikam Fiber CallCenter działającą w systemie azure.com. Na stronie zostanie wyświetlony pulpit nawigacyjny z listą wywołań obsługi.][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Konfigurowanie ciągłej integracji i ciągłego wdrażania (CI/CD) przy użyciu klastra usługi Service Fabric

Aby dowiedzieć się, jak skonfigurować wdrożenie aplikacji ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric za pomocą usługi Azure DevOps, zobacz [Samouczek: wdrażanie aplikacji przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Proces opisany w tym samouczku jest taki sam dla tego projektu (FabrikamFiber) — wystarczy po prostu pominąć pobieranie przykładu Voting i zastąpić nazwę repozytorium Voting nazwą FabrikamFiber.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy usuń wszystkie utworzone zasoby.  Najprostszym sposobem jest usunięcie grup zasobów, które zawierają klaster usługi Service Fabric, usługę Azure SQL DB i usługę Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Konteneryzowanie istniejącej aplikacji za pomocą programu Visual Studio
> * Tworzenie bazy danych w Azure SQL Database
> * Tworzenie rejestru kontenerów platformy Azure
> * Wdrażanie aplikacji usługi Service Fabric na platformie Azure

Z następnej części samouczka dowiesz się, jak [wdrażać aplikację kontenera przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
