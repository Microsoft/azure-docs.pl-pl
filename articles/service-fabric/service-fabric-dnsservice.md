---
title: Usługa DNS Service Fabric Azure
description: Użyj usługi DNS Service Fabric do odnajdywania mikrousług z wewnątrz klastra.
ms.topic: conceptual
ms.date: 7/20/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f7f06920820cdc73f8d3101ab24ee46625931ee4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91268047"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS Service in Azure Service Fabric (Usługa DNS w usłudze Azure Service Fabric)
Usługa DNS to opcjonalna usługa systemowa, którą można włączyć w klastrze w celu odnajdywania innych usług przy użyciu protokołu DNS. 

Wiele usług, szczególnie usługi kontenerowe, są adresowane za pomocą wstępnie istniejącego adresu URL. Jest to pożądane, aby można było rozpoznać te usługi przy użyciu standardowego protokołu DNS, a nie protokołu Usługa nazewnictwa Service Fabric. Usługa DNS umożliwia mapowanie nazw DNS na nazwę usługi, dlatego rozwiązuje adresy IP punktów końcowych. Takie funkcje utrzymują przenośność usług kontenerowych na różnych platformach i ułatwiają wykonywanie scenariuszy "Unieś" i "Shift", umożliwiając używanie istniejących adresów URL usług zamiast konieczności ponownego pisania kodu w celu wykorzystania Usługa nazewnictwa. 

Usługa DNS mapuje nazwy DNS na nazwy usług, które z kolei są rozwiązywane przez Usługa nazewnictwa w celu zwrócenia punktu końcowego usługi. Nazwa DNS usługi jest udostępniana w momencie tworzenia. Na poniższym diagramie przedstawiono, w jaki sposób usługa DNS działa w przypadku usług bezstanowych.

![Diagram przedstawiający sposób mapowania nazw DNS na nazwy usług przez usługę DNS dla usług bezstanowych.](./media/service-fabric-dnsservice/stateless-dns.png)

Począwszy od Service Fabric w wersji 6,3, Service Fabric protokół DNS został rozszerzony w celu uwzględnienia schematu do adresowania podzielonych usług stanowych. Te rozszerzenia umożliwiają rozpoznawanie określonych adresów IP partycji przy użyciu kombinacji nazw DNS usługi stanowej i nazwy partycji. Obsługiwane są wszystkie trzy schematy partycjonowania:

- Nazwane partycjonowanie
- Partycjonowanie w zakresie
- Partycjonowanie pojedyncze

Na poniższym diagramie przedstawiono, w jaki sposób usługa DNS działa w przypadku partycjonowanych usług stanowych.

![Diagram przedstawiający sposób mapowania nazw DNS na nazwy usług przez usługę DNS dla partycjonowanych usług bezstanowych.](./media/service-fabric-dnsservice/stateful-dns.png)

Porty dynamiczne nie są obsługiwane przez usługę DNS. Aby rozwiązać usługi udostępniane na portach dynamicznych, użyj [usługi zwrotnego serwera proxy](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Włączanie usługi DNS
> [!NOTE]
> Usługa DNS dla usług Service Fabric Services nie jest jeszcze obsługiwana w systemie Linux.

Podczas tworzenia klastra przy użyciu portalu usługa DNS jest domyślnie włączona w polu wyboru **Dołącz usługę DNS** w menu **Konfiguracja klastra** :

![Włączanie usługi DNS za pomocą portalu](./media/service-fabric-dnsservice/enable-dns-service.png)

Jeśli nie używasz portalu do utworzenia klastra lub jeśli aktualizujesz istniejący klaster, musisz włączyć usługę DNS w szablonie:

- Aby wdrożyć nowy klaster, można użyć [przykładowych szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) lub utworzyć własny szablon Menedżer zasobów. 
- Aby zaktualizować istniejący klaster, możesz przejść do grupy zasobów klastra w portalu, a następnie kliknąć pozycję **skrypt automatyzacji** , aby współpracować z szablonem, który odzwierciedla bieżący stan klastra i innych zasobów w grupie. Aby dowiedzieć się więcej, zobacz [Eksportowanie szablonu z grupy zasobów](../azure-resource-manager/templates/export-template-portal.md).

Po utworzeniu szablonu można włączyć usługę DNS, wykonując następujące czynności:

1. Sprawdź, czy `apiversion` dla zasobu jest ustawiona wartość `2017-07-01-preview` lub nowsza `Microsoft.ServiceFabric/clusters` , a jeśli nie, zaktualizuj go, jak pokazano w następującym przykładzie:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz należy włączyć usługę DNS w jeden z następujących sposobów:

   - Aby włączyć usługę DNS z ustawieniami domyślnymi, należy dodać ją do `addonFeatures` sekcji w `properties` sekcji, jak pokazano w następującym przykładzie:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Aby włączyć usługę inną niż ustawienia domyślne, Dodaj `DnsService` sekcję do `fabricSettings` sekcji w `properties` sekcji. W takim przypadku nie trzeba dodawać DnsService do `addonFeatures` . Aby dowiedzieć się więcej na temat właściwości, które można ustawić dla usługi DNS, zobacz [Ustawienia usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
3. Po zaktualizowaniu szablonu klastra przy użyciu zmian zastosuj je i pozwól na zakończenie uaktualniania. Po zakończeniu uaktualniania usługa systemu DNS uruchamia się w klastrze. Nazwa usługi to `fabric:/System/DnsService` i można ją znaleźć w sekcji usługa **systemowa** w Eksploratorze Service Fabric. 

> [!NOTE]
> Podczas uaktualniania usługi DNS z wyłączone do włączonej, Service Fabric Explorer może nie odzwierciedlać nowego stanu. Aby rozwiązać ten problem, uruchom ponownie węzły, modyfikując UpgradePolicy w szablonie Azure Resource Manager. Więcej informacji można znaleźć w temacie [Service Fabric Template Reference](/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) .

> [!NOTE]
> Włączenie usługi DNS podczas tworzenia na komputerze lokalnym spowoduje zastąpienie niektórych ustawień DNS. Jeśli występują problemy z połączeniem z Internetem, sprawdź ustawienia DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Ustawianie nazwy DNS usługi
Możesz ustawić nazwę DNS dla usług w sposób deklaratywny dla domyślnych usług w pliku ApplicationManifest.xml lub za pomocą poleceń programu PowerShell.

Nazwa DNS usługi jest rozpoznawana w klastrze, dlatego ważne jest, aby zapewnić unikatowość nazwy DNS w całym klastrze. 

Zdecydowanie zaleca się użycie schematu nazewnictwa, `<ServiceDnsName>.<AppInstanceName>` na przykład `service1.application1` . Jeśli aplikacja jest wdrażana za pomocą narzędzia Docker, usługi są automatycznie przypisywane nazwy DNS przy użyciu tego schematu nazewnictwa.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ustawianie nazwy DNS dla usługi domyślnej w ApplicationManifest.xml
Otwórz projekt w programie Visual Studio lub ulubionym edytorze i Otwórz plik ApplicationManifest.xml. Przejdź do sekcji Usługi domyślne, a dla każdej usługi Dodaj `ServiceDnsName` atrybut. Poniższy przykład pokazuje, jak ustawić nazwę DNS usługi na `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Po wdrożeniu aplikacji wystąpienie usługi w Eksploratorze Service Fabric wyświetla nazwę DNS dla tego wystąpienia, jak pokazano na poniższym rysunku: 

![punkty końcowe usługi](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

Poniższy przykład ustawia nazwę DNS usługi stanowej na `statefulsvc.app` . Usługa używa nazwanego schematu partycjonowania. Zauważ, że nazwy partycji są małymi literami. Jest to wymagane w przypadku partycji, które będą przeznaczone dla kwerend DNS; Aby uzyskać więcej informacji, zobacz [Tworzenie zapytań DNS na partycji usługi stanowej](#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ustawianie nazwy DNS dla usługi przy użyciu programu PowerShell
Nazwę DNS usługi można ustawić podczas jej tworzenia przy użyciu `New-ServiceFabricService` polecenia programu PowerShell. Poniższy przykład tworzy nową usługę bezstanową z nazwą DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>Przeglądania Wykonywanie zapytań DNS na partycji usługi stanowej
Począwszy od Service Fabric w wersji 6,3 Usługa Service Fabric DNS obsługuje zapytania dotyczące partycji usług.

W przypadku partycji, które będą używane w zapytaniach DNS, mają zastosowanie następujące ograniczenia dotyczące nazewnictwa:

   - Nazwa partycji powinna być zgodna z systemem DNS.
   - Nie należy używać nazw partycji obejmujących wiele etykiet (obejmujących kropkę, ".", w nazwie).
   - Nazwy partycji powinny być małymi literami.

Zapytania DNS, które są przeznaczone dla partycji, są sformatowane w następujący sposób:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Gdzie:

- *Pierwsza etykieta-usługa-dnsname* jest pierwszą częścią nazwy usługi DNS.
- *PartitionPrefix* jest wartością, którą można ustawić w sekcji DnsService manifestu klastra lub za pomocą szablonu Menedżer zasobów klastra. Wartość domyślna to "--". Aby dowiedzieć się więcej, zobacz  [Ustawienia usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- Wartość *Target-Partition-Name* to nazwa partycji. 
- *PartitionSuffix* jest wartością, którą można ustawić w sekcji DnsService manifestu klastra lub za pomocą szablonu Menedżer zasobów klastra. Wartość domyślna to pusty ciąg. Aby dowiedzieć się więcej, zobacz  [Ustawienia usługi DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Pozostałe partycje-Service-dnsname* jest pozostałą częścią nazwy usługi DNS.

W poniższych przykładach pokazano zapytania DNS dla usług partycjonowanych uruchomionych w klastrze z ustawieniami domyślnymi dla `PartitionPrefix` i `PartitionSuffix` : 

- Aby usunąć partycję "0" usługi o nazwie DNS `backendrangedschemesvc.application` używającej schematu partycjonowania z zakresem, użyj `backendrangedschemesvc-0.application` .
- Aby usunąć partycję "First" usługi o nazwie DNS `backendnamedschemesvc.application` używającej schematu partycjonowania, użyj `backendnamedschemesvc-first.application` .

Usługa DNS zwraca adres IP repliki podstawowej partycji. Jeśli partycja nie zostanie określona, usługa zwraca adres IP podstawowej repliki losowo wybranej partycji.

## <a name="using-dns-in-your-services"></a>Korzystanie z systemu DNS w usługach
W przypadku wdrażania więcej niż jednej usługi można znaleźć punkty końcowe innych usług do komunikowania się przy użyciu nazwy DNS. Usługa DNS działa w przypadku usług bezstanowych, a w Service Fabric w wersji 6,3 i nowszych dla usług stanowych. W przypadku usług stanowych działających w wersjach Service Fabric wcześniejszych niż 6,3 można użyć wbudowanej [usługi serwera proxy](./service-fabric-reverseproxy.md) dla wywołań http, aby wywołać określoną partycję usługi. 

Porty dynamiczne nie są obsługiwane przez usługę DNS. Usługi zwrotnego serwera proxy można użyć do rozpoznawania usług korzystających z portów dynamicznych.

Poniższy kod pokazuje, jak wywołać usługę bezstanową za pomocą systemu DNS. Jest to zwykłe połączenie http, w którym można podać nazwę DNS, port i dowolną opcjonalną ścieżkę w ramach adresu URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

Poniższy kod przedstawia wywołanie na określonej partycji usługi stanowej. W takim przypadku nazwa DNS zawiera nazwę partycji (partition1). Wywołanie przyjmuje klaster z wartościami domyślnymi dla `PartitionPrefix` i `PartitionSuffix` .

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Znane problemy
* W przypadku Service Fabric w wersji 6,3 lub nowszej występuje problem z wyszukiwaniem w systemie DNS nazw usług zawierających łącznik w nazwie DNS. Aby uzyskać więcej informacji na temat tego problemu, śledź następujący [problem](https://github.com/Azure/service-fabric-issues/issues/1197)z usługą GitHub. Poprawka zostanie naprawiona w następnej aktualizacji 6,3. 

* Usługa DNS dla usług Service Fabric Services nie jest jeszcze obsługiwana w systemie Linux. Usługa DNS jest obsługiwana w przypadku kontenerów w systemie Linux. Rozwiązaniem ręcznym przy użyciu programu Fabric Client/ServicePartitionResolver jest dostępna alternatywa.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o komunikacji usługi w ramach klastra przy użyciu  [usługi Connect i Komunikuj się z usługami](service-fabric-connect-and-communicate-with-services.md)
