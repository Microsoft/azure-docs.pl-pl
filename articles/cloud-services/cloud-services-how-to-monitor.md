---
title: Monitorowanie usługi w chmurze platformy Azure (klasycznej) | Microsoft Docs
description: W tym artykule opisano monitorowanie usług w chmurze platformy Azure oraz ich opcje.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743376"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Wprowadzenie do monitorowania usługi w chmurze (klasycznej)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Kluczowe metryki wydajności można monitorować dla każdej usługi w chmurze. Każda rola usługi w chmurze zbiera minimalne dane: użycie procesora, użycie sieci i użycie dysku. Jeśli usługa w chmurze ma `Microsoft.Azure.Diagnostics` rozszerzenie zastosowane do roli, ta rola może zbierać dodatkowe punkty danych. Ten artykuł zawiera wprowadzenie do Diagnostyka Azure Cloud Services.

W przypadku podstawowego monitorowania dane liczników wydajności z wystąpień ról są próbkowane i zbierane w 3-minutowych interwałach. Te podstawowe dane monitorowania nie są przechowywane na koncie magazynu i nie są z nim skojarzone żadne dodatkowe koszty.

Dzięki zaawansowanemu monitorowaniu dodatkowe metryki są próbkowane i zbierane z interwałem wynoszącym 5 minut, 1 godzina i 12 godzin. Zagregowane dane są przechowywane na koncie magazynu w tabelach i usuwane po upływie 10 dni. Używane konto magazynu jest konfigurowane przez rolę; dla różnych ról można użyć różnych kont magazynu. Jest to konfiguracja z parametrami połączenia w plikach [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) i [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .


## <a name="basic-monitoring"></a>Podstawowe monitorowanie

Zgodnie z opisem we wprowadzeniu usługa w chmurze automatycznie zbiera podstawowe dane monitorowania z maszyny wirtualnej hosta. Te dane obejmują wartość procentową procesora CPU, dane we/wychodzące oraz odczyt/zapis na dysku. Zebrane dane monitorowania są automatycznie wyświetlane na stronach przeglądów i metryk w usłudze w chmurze, w Azure Portal. 

Podstawowe monitorowanie nie wymaga konta magazynu. 

![podstawowe kafelki monitorowania usługi w chmurze](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Zaawansowane monitorowanie obejmuje użycie rozszerzenia **Diagnostyka Azure** (i opcjonalnie zestawu SDK Application Insights) na roli, którą chcesz monitorować. Rozszerzenie diagnostyki używa pliku konfiguracji (na rolę) o nazwie **Diagnostics. wadcfgx** , aby skonfigurować monitorowane metryki diagnostyki. Rozszerzenie diagnostyki platformy Azure gromadzi i zapisuje dane na koncie usługi Azure Storage. Te ustawienia są konfigurowane w plikach **. wadcfgx**, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)i [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) . Oznacza to, że istnieje dodatkowy koszt związany z zaawansowanym monitorowaniem.

Po utworzeniu każdej roli program Visual Studio dodaje do niej rozszerzenie Diagnostyka Azure. To rozszerzenie diagnostyki może zbierać następujące typy informacji:

* Niestandardowe liczniki wydajności
* Dzienniki aplikacji
* Dzienniki zdarzeń systemu Windows
* Źródło zdarzeń platformy .NET
* Dzienniki usług IIS
* Śledzenie zdarzeń systemu Windows w oparciu o manifest
* Zrzuty awaryjne
* Dzienniki błędów klienta

> [!IMPORTANT]
> Chociaż wszystkie te dane są agregowane na koncie magazynu, Portal **nie zapewnia** natywnego sposobu grafowania danych. Zdecydowanie zaleca się, aby zintegrować inną usługę, taką jak Application Insights, z aplikacją.

## <a name="setup-diagnostics-extension"></a>Zainstaluj rozszerzenie diagnostyki

Po pierwsze, jeśli nie masz **klasycznego** konta magazynu, [Utwórz je](../storage/common/storage-account-create.md). Upewnij się, że konto magazynu zostało utworzone przy użyciu określonego **klasycznego modelu wdrażania** .

Następnie przejdź do zasobu **konta magazynu (klasycznego)** . Wybierz pozycję **Ustawienia**  >  **klucze dostępu** i skopiuj wartość **podstawowe parametry połączenia** . Ta wartość jest potrzebna dla usługi w chmurze. 

Istnieją dwa pliki konfiguracji, które należy zmienić w celu zapewnienia, że Zaawansowana diagnostyka zostanie włączona, **ServiceDefinition. csdef** i **ServiceConfiguration. cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef

W pliku **ServiceDefinition. csdef** Dodaj nowe ustawienie o nazwie `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` dla każdej roli, która używa zaawansowanej diagnostyki. Program Visual Studio dodaje tę wartość do pliku podczas tworzenia nowego projektu. W przypadku braku tej możliwości możesz ją dodać teraz. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Definiuje nowe ustawienie, które należy dodać do każdego pliku **ServiceConfiguration. cscfg** . 

Najprawdopodobniej masz dwa pliki **. cscfg** o nazwie **ServiceConfiguration. Cloud. cscfg** do wdrożenia na platformie Azure oraz jedną nazwę **ServiceConfiguration. local. cscfg** , która jest używana na potrzeby wdrożeń lokalnych w emulowanym środowisku. Otwórz i Zmień każdy plik **. cscfg** . Dodaj ustawienie o nazwie `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` . Ustaw wartość na **podstawowe parametry połączenia** klasycznego konta magazynu. Jeśli chcesz użyć magazynu lokalnego na komputerze deweloperskim, użyj programu `UseDevelopmentStorage=true` .

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Użyj Application Insights

Po opublikowaniu usługi w chmurze w programie Visual Studio można wysłać dane diagnostyczne do Application Insights. W tym momencie możesz utworzyć zasób usługi Application Insights Azure lub wysłać dane do istniejącego zasobu platformy Azure. Usługa w chmurze może być monitorowana przez Application Insights w celu zapewnienia dostępności, wydajności, błędów i użycia. Niestandardowe wykresy można dodać do Application Insights, aby zobaczyć dane, które są najbardziej istotne. Dane wystąpienia roli można zbierać przy użyciu zestawu SDK Application Insights w projekcie usługi w chmurze. Aby uzyskać więcej informacji na temat integrowania Application Insights, zobacz [Application Insights z Cloud Services](../azure-monitor/app/cloudservices.md).

Należy pamiętać, że podczas korzystania z Application Insights, aby wyświetlić liczniki wydajności (i inne ustawienia) określone za pomocą rozszerzenia Windows Diagnostyka Azure, można uzyskać bogatsze środowisko integracji zestawu SDK Application Insights z rolami procesów roboczych i sieci Web.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o Application Insights z Cloud Services](../azure-monitor/app/cloudservices.md)
- [Konfigurowanie liczników wydajności](diagnostics-performance-counters.md)




