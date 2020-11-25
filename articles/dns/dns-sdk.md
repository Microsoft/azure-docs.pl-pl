---
title: Tworzenie stref DNS i zestawów rekordów przy użyciu zestawu .NET SDK
titleSuffix: Azure DNS
description: W tej ścieżce szkoleniowej Rozpocznij tworzenie stref DNS i zestawów rekordów w Azure DNS przy użyciu zestawu .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015064"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Tworzenie stref DNS i zestawów rekordów przy użyciu zestawu .NET SDK

Można zautomatyzować operacje tworzenia, usuwania lub aktualizowania stref DNS, zestawów rekordów i rekordów przy użyciu zestawu SDK systemu DNS z biblioteką zarządzania usługi DNS platformy .NET. Pełny projekt programu Visual Studio jest dostępny [tutaj.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Tworzenie konta nazwy głównej usługi

Zazwyczaj dostęp programistyczny do zasobów platformy Azure uzyskuje się za pośrednictwem dedykowanego konta, a nie poświadczeń użytkownika. Te dedykowane konta są nazywane kontami podmiotu usługi. Aby użyć przykładowego projektu Azure DNS SDK, należy najpierw utworzyć konto głównej usługi i przypisać mu odpowiednie uprawnienia.

1. Postępuj zgodnie z [tymi instrukcjami](../active-directory/develop/howto-authenticate-service-principal-powershell.md) , aby utworzyć konto nazwy głównej usługi (projekt Azure DNS SDK przykład zakłada uwierzytelnianie oparte na hasłach).
2. Utwórz grupę zasobów ([jak to opisano w tym miejscu](../azure-resource-manager/templates/deploy-portal.md)).
3. Użyj usługi Azure RBAC, aby przyznać uprawnienia współautora strefy DNS konta głównego usługi do grupy zasobów ([Oto jak to zrobić](../role-based-access-control/role-assignments-portal.md)).
4. Jeśli używasz przykładowego projektu Azure DNS zestawu SDK, edytuj plik "program. cs" w następujący sposób:

   * Wstaw poprawne wartości dla `tenantId` , `clientId` (znane również jako identyfikator konta), `secret` (hasło konta nazwy głównej usługi) i `subscriptionId` jak używane w kroku 1.
   * Wprowadź nazwę grupy zasobów wybraną w kroku 2.
   * Wprowadź wybraną nazwę strefy DNS.

## <a name="nuget-packages-and-namespace-declarations"></a>Deklaracje pakietów NuGet i przestrzeni nazw

Aby użyć Azure DNS .NET SDK, należy zainstalować pakiet NuGet **biblioteki zarządzania Azure DNS** i inne wymagane pakiety platformy Azure.

1. W programie **Visual Studio** Otwórz projekt lub nowy projekt.
2. Wybierz kolejno pozycje **Narzędzia** **>** **Menedżer pakietów NuGet** **>** **Zarządzanie pakietami NuGet dla rozwiązania...**
3. Kliknij przycisk **Przeglądaj**, zaznacz pole wyboru **Uwzględnij wersję wstępną** i wpisz **Microsoft. Azure. Management. DNS** w polu wyszukiwania.
4. Wybierz pakiet i kliknij pozycję **Zainstaluj** , aby dodać go do projektu programu Visual Studio.
5. Powtórz ten proces, aby zainstalować również następujące pakiety: **Microsoft. Rest. ClientRuntime. Azure. Authentication** i **Microsoft. Azure. Management. ResourceManager**.

## <a name="add-namespace-declarations"></a>Dodawanie deklaracji przestrzeni nazw

Dodaj następujące deklaracje przestrzeni nazw

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicjowanie klienta zarządzania usługą DNS

`DnsManagementClient`Zawiera metody i właściwości niezbędne do zarządzania strefami i zestawami rekordów DNS.  Poniższy kod loguje się do konta głównego usługi i tworzy `DnsManagementClient` obiekt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Tworzenie lub aktualizowanie strefy DNS

Aby utworzyć strefę DNS, najpierw tworzony jest obiekt "Zone" zawierający parametry strefy DNS. Ze względu na to, że strefy DNS nie są połączone z określonym regionem, lokalizacja jest ustawiona na wartość "globalna". W tym przykładzie dodano również [Azure Resource Manager "tag"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) do strefy.

Aby faktycznie utworzyć lub zaktualizować strefę w Azure DNS, obiekt strefy zawierający parametry strefy jest przenoszona do `DnsManagementClient.Zones.CreateOrUpdateAsyc` metody.

> [!NOTE]
> DnsManagementClient obsługuje trzy tryby działania: synchroniczne ("metodę createorupdate"), asynchroniczne ("CreateOrUpdateAsync") lub asynchroniczne z dostępem do odpowiedzi HTTP ("CreateOrUpdateWithHttpMessagesAsync").  Możesz wybrać dowolny z tych trybów, w zależności od potrzeb aplikacji.

Azure DNS obsługuje optymistyczną współbieżność, nazywaną [ETag](./dns-getstarted-powershell.md). W tym przykładzie Określanie znaku "*" dla nagłówka "If-None-Match" oznacza Azure DNS, aby utworzyć strefę DNS, jeśli taka jeszcze nie istnieje.  Wywołanie kończy się niepowodzeniem, jeśli strefa o podaną nazwie już istnieje w danej grupie zasobów.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Tworzenie zestawów rekordów i rekordów DNS

Rekordy DNS są zarządzane jako zestaw rekordów. Zestaw rekordów jest zestawem rekordów o tej samej nazwie i typie rekordu w obrębie strefy.  Nazwa zestawu rekordów jest względna w stosunku do nazwy strefy, a nie w pełni kwalifikowanej nazwy DNS.

Aby utworzyć lub zaktualizować zestaw rekordów, tworzony jest obiekt parametrów "RecordSet" i przeszedł do `DnsManagementClient.RecordSets.CreateOrUpdateAsync` . Podobnie jak w przypadku stref DNS, istnieją trzy tryby działania: synchroniczne ("metodę createorupdate"), asynchroniczne ("CreateOrUpdateAsync") lub asynchroniczne z dostępem do odpowiedzi HTTP ("CreateOrUpdateWithHttpMessagesAsync").

Podobnie jak w przypadku stref DNS, operacje na zestawach rekordów obejmują obsługę optymistycznej współbieżności.  W tym przykładzie, ponieważ nie określono opcji "If-Match" ani "If-None-Match", zestaw rekordów jest zawsze tworzony.  To wywołanie zastępuje wszystkie istniejące zestawy rekordów o tej samej nazwie i typie rekordu w tej strefie DNS.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Pobierz strefy i zestawy rekordów

`DnsManagementClient.Zones.Get`Metody i `DnsManagementClient.RecordSets.Get` pobierają odpowiednio poszczególne strefy i zestawy rekordów. Zestawy rekordów są identyfikowane według ich typu, nazwy i strefy oraz grupy zasobów, w których istnieją. Strefy są identyfikowane przez ich nazwę i grupę zasobów, w której istnieją.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizowanie istniejącego zestawu rekordów

Aby zaktualizować istniejący zestaw rekordów DNS, najpierw Pobierz zestaw rekordów, a następnie zaktualizuj zawartość zestawu rekordów, a następnie prześlij zmianę.  W tym przykładzie określimy element "ETag" z pobranego zestawu rekordów w parametrze "If-Match". Wywołanie nie powiedzie się, jeśli w międzyczasie operacja współbieżna zmodyfikował zestaw rekordów.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Wyświetlanie listy stref i zestawów rekordów

Aby wyświetlić listę stref, należy użyć metod *DnsManagementClient. Zones. list...* , które obsługują wyświetlanie wszystkich stref w danej grupie zasobów lub wszystkich stref w danej subskrypcji platformy Azure (między grupami zasobów). Aby wyświetlić listę zestawów rekordów, użyj metod *DnsManagementClient. Recordsets. list...* , które obsługują listę wszystkich zestawów rekordów w danej strefie lub tylko zestawy rekordów określonego typu.

Należy pamiętać, że podczas wyświetlania listy stref i zestawów rekordów może być podzielony na strony.  Poniższy przykład pokazuje, jak wykonać iterację stron wyników. (Sztucznie niewielki rozmiar strony "2") służy do wymuszania stronicowania. w tym celu należy pominąć ten parametr i użyć domyślnego rozmiaru strony).

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Następne kroki

Pobierz [przykładowy projekt Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), który zawiera dalsze przykłady dotyczące korzystania z zestawu SDK Azure DNS .NET, w tym przykłady dla innych typów rekordów DNS.