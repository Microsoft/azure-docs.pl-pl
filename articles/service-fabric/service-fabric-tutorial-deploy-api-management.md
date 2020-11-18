---
title: Integracja API Management z Service Fabric na platformie Azure
description: Dowiedz się, jak szybko rozpocząć pracę z usługą Azure API Management i kierować ruchem do usługi zaplecza w programie Service Fabric.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 61a13d80fd67751d77bc77199fa433143ad92048
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831705"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Integracja API Management z Service Fabric na platformie Azure

Wdrażanie usługi Azure API Management z usługą Service Fabric odbywa się w ramach zaawansowanego scenariusza.  Usługa API Management jest przydatna, gdy trzeba opublikować interfejsy API z obszernym zestawem reguł rozsyłania dla usług zaplecza Service Fabric. Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. W usłudze Service Fabric bramą może być dowolna usługa bezstanowa przeznaczona dla ruchu przychodzącego, na przykład aplikacja ASP.NET Core albo usługa Event Hubs, IoT Hub lub Azure API Management.

W tym artykule opisano sposób konfigurowania [usługi Azure API Management](../api-management/api-management-key-concepts.md) przy użyciu Service Fabric do kierowania ruchu do usługi zaplecza w Service Fabric.  Wykonanie podanych instrukcji pozwoli wdrożyć usługę API Management w sieci wirtualnej i skonfigurować kierowanie ruchu do bezstanowych usług zaplecza za pomocą operacji interfejsu API. Aby dowiedzieć się więcej o scenariuszach użycia usługi Azure API Management z usługą Service Fabric, zobacz artykuł z [omówieniem](service-fabric-api-management-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Dostępność

> [!IMPORTANT]
> Ta funkcja jest dostępna w warstwach **Premium** i **Developer** API Management ze względu na wymaganą obsługę sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj program [Azure PowerShell](/powershell/azure/install-az-ps) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Utwórz bezpieczny [klaster systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) w sieciowej grupie zabezpieczeń.
* W przypadku wdrażania klastra systemu Windows skonfiguruj środowisko deweloperskie w systemie Windows. Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com) oraz **wieloplatformowe obciążenia programistyczne** **platformy Azure**, **ASP.NET i Web Development** oraz platformy .NET Core.  Następnie skonfiguruj [środowisko deweloperskie platformy .NET](service-fabric-get-started.md).

## <a name="network-topology"></a>Topologia sieci

Teraz, gdy masz bezpieczny [klaster systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure, wdróż API Management w sieci wirtualnej (VNET) w podsieci i sieciowej grupy zabezpieczeń Wyznaczeni dla API Management. W tym artykule szablon Menedżer zasobów API Management jest wstępnie skonfigurowany do używania nazw sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń, które zostały skonfigurowane w [samouczku klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) . w tym artykule opisano następujące topologie na platformie Azure, w których API Management i Service Fabric znajdują się w podsieciach tego samego Virtual Network:

 ![Podpis pod obrazem][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Logowanie do platformy Azure i wybieranie subskrypcji

Przed wykonaniem poleceń platformy Azure zaloguj się na konto platformy Azure i wybierz subskrypcję.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Wdrażanie usługi zaplecza Service Fabric

Do skonfigurowania usługi API Management pod kątem kierowania ruchu do usługi zaplecza Service Fabric potrzebna jest uruchomiona usługa, umożliwiająca akceptowanie żądań.  

Utwórz niezawodną usługę bezstanową ASP.NET Core przy użyciu szablonu projektu domyślnego interfejsu API sieci Web. Spowoduje to utworzenie punktu końcowego HTTP dla usługi, który należy udostępnić za pośrednictwem usługi Azure API Management.

Uruchom program Visual Studio jako administrator i utwórz usługę ASP.NET Core:

 1. W programie Visual Studio wybierz pozycję Plik -> Nowy projekt.
 2. W obszarze Chmura wybierz szablon aplikacji Service Fabric i nadaj mu nazwę **„ApiApplication”**.
 3. Wybierz szablon bezstanowej usługi ASP.NET Core i nazwij projekt **„WebApiService”**.
 4. Wybierz szablon projektu Web API ASP.NET Core 2,1.
 5. Po utworzeniu projektu otwórz plik `PackageRoot\ServiceManifest.xml` i usuń atrybut `Port` z konfiguracji zasobu punktu końcowego:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Usunięcie portu pozwala Service Fabric określić port dynamicznie z zakresu portów aplikacji, otwierając go za pomocą sieciowej grupy zabezpieczeń w klastrze Menedżer zasobów klastra, zezwalając na przepływ ruchu do niego z API Management.

 6. W programie Visual Studio naciśnij klawisz F5, aby sprawdzić, czy internetowy interfejs API jest dostępny lokalnie.

    Otwórz narzędzie Service Fabric Explorer i przejdź do konkretnego wystąpienia usługi ASP.NET Core, aby wyświetlić podstawowy adres, na jakim nasłuchuje ta usługa. Dodaj ciąg `/api/values` do podstawowego adresu, a następnie otwórz ten adres w przeglądarce. Spowoduje to wywołanie metody Get klasy ValuesController w szablonie internetowego interfejsu API. Szablon zwróci domyślną odpowiedź — tablicę JSON, zawierającą dwa ciągi:

    ```json
    ["value1", "value2"]`
    ```

    Jest to punkt końcowy, który należy udostępnić za pomocą usługi API Management na platformie Azure.

 7. Teraz wdróż aplikację w klastrze na platformie Azure. W programie Visual Studio kliknij prawym przyciskiem myszy projekt aplikacji, a następnie wybierz polecenie **Opublikuj**. Podaj punkt końcowy klastra (na przykład `mycluster.southcentralus.cloudapp.azure.com:19000`), aby wdrożyć aplikację w klastrze usługi Service Fabric na platformie Azure.

W klastrze powinna teraz być uruchomiona bezstanowa usługa ASP.NET Core o nazwie `fabric:/ApiApplication/WebApiService`.

## <a name="download-and-understand-the-resource-manager-templates"></a>Pobieranie szablonów usługi Resource Manager i zapoznawanie się z nimi

Pobierz i zapisz plik parametrów oraz następujące szablony usługi Resource Manager:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

Szablon *network-apim.json* umożliwia wdrożenie nowej podsieci i sieciowej grupy zabezpieczeń w sieci wirtualnej, w której wdrożono klaster usługi Service Fabric.

W poniższych sekcjach opisano zasoby definiowane za pomocą szablonu *apim.json*. Poszczególne sekcje zawierają linki do dokumentacji szablonów, umożliwiające uzyskanie dodatkowych informacji. Konfigurowalne parametry, zdefiniowane w pliku *apim.parameters.json*, zostaną ustawione w dalszej części tego artykułu.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Szablon [Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) opisuje wystąpienie usługi API Management i zawiera następujące informacje: nazwę, jednostkę SKU lub warstwę, lokalizację grupy zasobów oraz informacje o wydawcy i sieci wirtualnej.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

Szablon [Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) umożliwia konfigurowanie zabezpieczeń usługi API Management. W ramach odnajdywania usługi usługa API Management musi zostać uwierzytelniona w klastrze usługi Service Fabric przy użyciu certyfikatu klienta, który ma dostęp do klastra. W tym artykule użyto tego samego certyfikatu określonego wcześniej podczas tworzenia [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor), który jest domyślnie używany do uzyskiwania dostępu do klastra.

W tym artykule jest stosowany ten sam certyfikat do uwierzytelniania klienta i zabezpieczenia węzła klastra na węzeł. Jeśli masz skonfigurowany osobny certyfikat klienta, możesz go używać do korzystania z klastra usługi Service Fabric. Podaj **nazwę**, **hasło** i **dane** (szyfrowany ciąg base64) dotyczące pliku klucza prywatnego (pfx) certyfikatu klastra, który został określony podczas tworzenia klastra usługi Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

Szablon [Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) opisuje usługę zaplecza, do której jest przekazywany ruch.

W przypadku usługi Service Fabric zapleczem jest klaster usługi Service Fabric, a nie określona usługa Service Fabric. Dzięki temu za pomocą jednej zasady można kierować ruch do więcej niż jednej usługi w klastrze. Pole **url** zawiera w pełni kwalifikowaną nazwę usługi w klastrze, do której są domyślnie kierowane wszystkie żądania, jeśli w zasadach zaplecza nie podano nazwy usługi. Jeśli nie potrzebujesz usługi rezerwowej, możesz użyć wymyślonej nazwy, takiej jak „fabric:/fake/service”. Pole **resourceId** określa punkt końcowy zarządzania klastrem.  Pola **clientCertificateThumbprint** i **serverCertificateThumbprints** identyfikują certyfikaty używane do uwierzytelniania w klastrze.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

Szablon [Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) umożliwia utworzenie produktu. W usłudze Azure API Management produkt zawiera co najmniej jeden interfejs API oraz limit przydziału użycia i warunki użytkowania. Po opublikowaniu produktu deweloperzy mogą go zasubskrybować i zacząć korzystać z jego interfejsów API.

W polu **displayName** wpisz opisową nazwę produktu, a w polu **description** — opis. W tym artykule wymagana jest subskrypcja, ale jej zatwierdzenie przez administratora nie jest wymagane.  **Stan** tego produktu ma wartość „opublikowany” i jest widoczny dla subskrybentów.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

Szablon [Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) umożliwia utworzenie interfejsu API. Interfejs API usługi API Management reprezentuje zestaw operacji, które mogą być wywoływane przez aplikacje klienckie. Po dodaniu operacji do produktu jest dodawany interfejs API, który można opublikować. Opublikowany interfejs API można zasubskrybować, a deweloperzy mogą go używać.

* Pole **displayName** może zawierać dowolną nazwę interfejsu API. W tym artykule Użyj "Service Fabric App".
* Pole **name** zawiera unikatową i opisową nazwę interfejsu API, taką jak „service-fabric-app”. Jest ona wyświetlana w portalach dewelopera i wydawcy.
* Pole **serviceUrl** zawiera odwołanie do usługi HTTP implementującej interfejs API. Usługa API Management przekazuje żądania na ten adres. W przypadku zapleczy usługi Service Fabric ta wartość adresu URL nie jest używana. Można tu wpisać dowolną wartość. W tym artykule, na przykład "http: \/ /servicefabric".
* Wartość pola **path** jest dołączana do podstawowego adresu URL usługi API Management. Podstawowy adres URL jest wspólny dla wszystkich interfejsów API hostowanych przez wystąpienie usługi API Management. W usłudze API Management interfejsy API są rozróżniane na podstawie sufiksów, dlatego sufiksy poszczególnych interfejsów API dla danego wydawcy muszą być unikatowe.
* Pole **protocols** określa, których protokołów można używać w celu uzyskania dostępu do interfejsu API. W tym artykule wymieniono **protokoły http** i **https**.
* Pole **path** jest sufiksem interfejsu API. W tym artykule należy użyć "MojaApl".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Przed użyciem interfejsu API usługi API Management należy dodać do niego operacje.  Klienci zewnętrzni używają operacji do komunikowania się z bezstanową usługą ASP.NET Core uruchomioną w klastrze usługi Service Fabric.

Aby dodać operację interfejsu API frontonu, podaj następujące wartości:

* Pola **displayName** i **description** opisują operację. Na potrzeby tego artykułu Użyj "wartości".
* Pole **method** określa polecenie HTTP.  W tym artykule określ polecenie **Get**.
* Wartość pola **urlTemplate** jest dołączana do podstawowego adresu URL interfejsu API i określa pojedynczą operację HTTP.  W tym artykule należy użyć, `/api/values` Jeśli dodano usługę zaplecza platformy .NET lub `getMessage` Dodano usługę zaplecza Java.  Określona tutaj ścieżka URL jest domyślnie wysyłana do usługi Service Fabric zaplecza. W przypadku podania ścieżki URL używanej przez usługę, takiej jak „/api/values”, operacja działa bez dodatkowych modyfikacji. Można również podać ścieżkę URL, która różni się od ścieżki URL używanej przez usługę Service Fabric zaplecza. W takim przypadku należy później określić nadpisanie ścieżki w zasadach operacji.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

Szablon [Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) umożliwia utworzenie zasad zaplecza, które pozwalają zintegrować wszystkie elementy. Zasady te umożliwiają skonfigurowanie usługi Service Fabric zaplecza, do której są kierowane żądania. Zasady można zastosować do dowolnej operacji interfejsu API.  Aby uzyskać więcej informacji, zobacz [Omówienie zasad](../api-management/api-management-howto-policies.md).

[Konfiguracja zaplecza dla usługi Service Fabric](../api-management/api-management-transformation-policies.md#SetBackendService) udostępnia następujące możliwości sterowania routingiem żądań:

* Wybieranie wystąpienia usługi przez podanie nazwy wystąpienia usługi Service Fabric — zakodowanej (na przykład `"fabric:/myapp/myservice"`) lub wygenerowanej na podstawie żądania HTTP (na przykład `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Rozpoznawanie partycji przez wygenerowanie klucza partycji przy użyciu dowolnego schematu partycjonowania usługi Service Fabric.
* Wybieranie repliki dla usług stanowych.
* Warunki ponownego rozpoznawania, które umożliwiają określenie warunków ponownego rozpoznawania lokalizacji usługi i wysyłania żądania.

**policyContent** to zawartość XML zasad, ujęta w znaki Json zmieniające znaczenie.  W tym artykule należy utworzyć zasady zaplecza, aby kierować żądania bezpośrednio do wdrożonej wcześniej usługi bezstanowej platformy .NET lub Java. Dodaj zasady `set-backend-service` w obszarze zasad ruchu przychodzącego.  Wartość *sf-service-instance-name* zastąp ciągiem `fabric:/ApiApplication/WebApiService`, jeśli wcześniej wdrożono usługę zaplecza platformy .NET, lub ciągiem `fabric:/EchoServerApplication/EchoServerService`, jeśli wdrożono usługę platformy Java.  Pole *backend-id* zawiera odwołanie do zasobu zaplecza, w tym przypadku zasobu `Microsoft.ApiManagement/service/backends` zdefiniowanego w szablonie *apim.json*. Pole *backend-id* może także odwoływać się do innego zasobu zaplecza, utworzonego za pomocą interfejsów API usługi API Management. W tym artykule Ustaw *Identyfikator zaplecza* na wartość parametru *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Aby uzyskać pełny zestaw atrybutów zasad zaplecza usługi Service Fabric, zobacz[API Management back-end documentation (Dokumentacja zaplecza usługi API Management)](../api-management/api-management-transformation-policies.md#SetBackendService).

## <a name="set-parameters-and-deploy-api-management"></a>Ustawianie parametrów i wdrażanie usługi API Management

Wypełnij następujące puste parametry szablonu *apim.parameters.json* dla danego wdrożenia.

|Parametr|Wartość|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Deweloper|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;ciąg zakodowany w formacie Base-64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;Ciąg XML&gt;|

Parametry *certificatePassword* i *serviceFabricCertificateThumbprint* muszą być zgodne z certyfikatem klastra użytym podczas konfigurowania klastra.

Parametr *serviceFabricCertificate* zawiera certyfikat w postaci ciągu zakodowanego w formacie Base-64, który można wygenerować za pomocą następującego skryptu:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

W parametrze *inbound_policy* wartość *sf-service-instance-name* zastąp ciągiem `fabric:/ApiApplication/WebApiService`, jeśli wcześniej wdrożono usługę zaplecza platformy .NET, lub ciągiem `fabric:/EchoServerApplication/EchoServerService`, jeśli wdrożono usługę platformy Java. Pole *backend-id* zawiera odwołanie do zasobu zaplecza, w tym przypadku zasobu `Microsoft.ApiManagement/service/backends` zdefiniowanego w szablonie *apim.json*. Pole *backend-id* może także odwoływać się do innego zasobu zaplecza, utworzonego za pomocą interfejsów API usługi API Management. W tym artykule Ustaw *Identyfikator zaplecza* na wartość parametru *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Następujący skrypt umożliwia wdrożenie szablonu i plików parametrów usługi Resource Manager dla usługi API Management:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Testowanie

Teraz możesz wysłać żądanie do usługi zaplecza Service Fabric za pośrednictwem usługi API Management bezpośrednio z witryny [Azure Portal](https://portal.azure.com).

 1. W usłudze API Management wybierz pozycję **Interfejs API**.
 2. W interfejsie API **aplikacji usługi Service Fabric** utworzonym w poprzednich krokach wybierz kartę **Test**, a następnie operację **Values**.
 3. Kliknij przycisk **Wyślij**, aby wysłać żądanie testowe do usługi zaplecza.  Powinna zostać wyświetlona mniej więcej taka odpowiedź HTTP:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Klaster składa się z innych zasobów platformy Azure poza samym zasobem klastra. Najprostszym sposobem na usunięcie klastra i wszystkich wykorzystywanych przez niego zasobów jest usunięcie grupy zasobów.

Zaloguj się do platformy Azure i wybierz identyfikator subskrypcji, z którym chcesz usunąć klaster.  Identyfikator subskrypcji można uzyskać po zalogowaniu się do [witryny Azure Portal](https://portal.azure.com). Usuń grupę zasobów i wszystkie zasoby klastra przy użyciu [polecenia cmdlet Remove-AzResourceGroup](/en-us/powershell/module/az.resources/remove-azresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o korzystaniu z [API Management](../api-management/import-and-publish.md).

[azure-powershell]: /powershell/azure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
Sieć szkieletowa — skrypty i szablony/obiekty blob/Master/templates/Service-Integration/network-apim.parameters.jsonn

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
