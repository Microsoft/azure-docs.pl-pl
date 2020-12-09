---
title: Ponowne używanie szablonów w chmurach
description: Opracowuj szablony Azure Resource Manager (szablony ARM), które działają spójnie dla różnych środowisk chmury. Utwórz nowe lub zaktualizuj istniejące szablony dla Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 806556a8da97ec84fe8141b95198b4a7da95c062
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928362"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Opracowywanie szablonów ARM w celu zapewnienia spójności chmury

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Kluczową zaletą platformy Azure jest spójność. Inwestycje programistyczne dla jednej lokalizacji są wielokrotnego użytku w innym. Szablon Azure Resource Manager (szablon ARM) pozwala spójnie i powtarzać wdrożenia w różnych środowiskach, takich jak globalne platformy Azure, suwerenne chmury platformy Azure i Azure Stack. Jednak aby ponownie użyć szablonów w chmurach, należy wziąć pod uwagę zależności specyficzne dla chmury, jak opisano w tym przewodniku.

Firma Microsoft oferuje inteligentne, gotowe dla przedsiębiorstwa usługi w chmurze w wielu lokalizacjach, w tym:

* Globalna platforma platformy Azure obsługiwana przez rosnącą sieć centrów danych zarządzanych przez firmę Microsoft w regionach na całym świecie.
* Izolowane suwerenne chmury, takie jak Azure (Niemcy, Azure Government i Chiny). Suwerenne chmury zapewniają spójną platformę z większością tych samych doskonałych funkcji, do których mają dostęp globalni klienci platformy Azure.
* Azure Stack platformę chmury hybrydowej, która umożliwia dostarczanie usług platformy Azure z centrum danych organizacji. Przedsiębiorstwa mogą skonfigurować Azure Stack w swoich centrach danych lub korzystać z usług platformy Azure od dostawców usług, uruchamiając Azure Stack w swoich obiektach (czasami znanych jako regiony hostowane).

Na początku wszystkich tych chmur Azure Resource Manager udostępnia interfejs API umożliwiający szeroką gamę interfejsów użytkownika komunikowanie się z platformą Azure. Ten interfejs API zapewnia zaawansowane możliwości infrastruktury jako kodu. Wszystkie typy zasobów dostępne na platformie Azure Cloud Platform można wdrożyć i skonfigurować przy użyciu Azure Resource Manager. Za pomocą jednego szablonu można wdrożyć i skonfigurować kompletną aplikację do stanu końcowego.

![Środowiska platformy Azure](./media/templates-cloud-consistency/environments.png)

Spójność globalnej platformy Azure, suwerenne chmury, chmury hostowane i chmura w centrum danych ułatwiają korzystanie z Azure Resource Manager. Inwestycji programistycznych można użyć w tych chmurach podczas konfigurowania wdrażania i konfigurowania zasobów opartych na szablonach.

Jednak mimo że chmury globalne, suwerenne, hostowane i hybrydowe zapewniają spójne usługi, nie wszystkie chmury są identyczne. W związku z tym można utworzyć szablon z zależnościami dotyczącymi funkcji dostępnych tylko w określonej chmurze.

W pozostałej części tego przewodnika opisano obszary, które należy wziąć pod uwagę podczas planowania tworzenia nowych lub aktualizowania istniejących szablonów ARM dla Azure Stack. Ogólnie rzecz biorąc, Lista kontrolna powinna zawierać następujące elementy:

* Sprawdź, czy funkcje, punkty końcowe, usługi i inne zasoby w szablonie są dostępne w docelowych lokalizacjach wdrożenia.
* Przechowuj zagnieżdżone szablony i artefakty konfiguracji w dostępnych lokalizacjach, zapewniając dostęp między chmurami.
* Użyj dynamicznych odwołań zamiast twardych i niezakodowanych elementów.
* Upewnij się, że parametry szablonu są używane w chmurach docelowych.
* Sprawdź, czy właściwości specyficzne dla zasobów są dostępne dla chmur docelowych.

Aby zapoznać się z wprowadzeniem do szablonów ARM, zobacz [Template Deployment](overview.md).

## <a name="ensure-template-functions-work"></a>Upewnij się, że funkcje szablonu działają

Podstawowa składnia szablonu ARM to JSON. Szablony używają nadzbioru kodu JSON, rozszerzając składnię z wyrażeniami i funkcjami. Procesor języka szablonów jest często aktualizowany w celu obsługi dodatkowych funkcji szablonu. Aby uzyskać szczegółowy opis funkcji dostępnych szablonów, zobacz [funkcje szablonu ARM](template-functions.md).

Nowe funkcje szablonu wprowadzone do Azure Resource Manager nie są natychmiast dostępne w przypadku suwerennych chmur lub Azure Stack. Aby pomyślnie wdrożyć szablon, wszystkie funkcje, do których odwołuje się ten szablon, muszą być dostępne w chmurze docelowej.

Funkcje Azure Resource Manager są zawsze wprowadzane do globalnej platformy Azure jako pierwsze. Za pomocą poniższego skryptu programu PowerShell można sprawdzić, czy nowo wprowadzone funkcje szablonu są również dostępne w Azure Stack:

1. Utwórz klon repozytorium GitHub: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. Po utworzeniu lokalnego klona repozytorium, Połącz się z Azure Resource Managerm docelowym przy użyciu programu PowerShell.

1. Zaimportuj moduł PSM1 i wykonaj Test-AzureRmTemplateFunctions polecenie cmdlet:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Skrypt wdraża wiele, zminimalizowane szablony, z których każdy zawiera tylko unikatowe funkcje szablonu. W danych wyjściowych skryptu są raportowane obsługiwane i niedostępne funkcje szablonu.

## <a name="working-with-linked-artifacts"></a>Praca z połączonymi artefaktami

Szablon może zawierać odwołania do połączonych artefaktów i zawiera zasób wdrożenia, który łączy się z innym szablonem. Połączone szablony (nazywane również szablonem zagnieżdżonym) są pobierane przez Menedżer zasobów w czasie wykonywania. Szablon może również zawierać odwołania do artefaktów dla rozszerzeń maszyny wirtualnej (VM). Te artefakty są pobierane przez rozszerzenie maszyny wirtualnej działające w ramach maszyny wirtualnej w celu skonfigurowania rozszerzenia maszyny wirtualnej podczas wdrażania szablonu.

W poniższych sekcjach opisano zagadnienia dotyczące spójności w chmurze podczas tworzenia szablonów zawierających artefakty, które są zewnętrzne względem głównego szablonu wdrożenia.

### <a name="use-nested-templates-across-regions"></a>Używanie szablonów zagnieżdżonych w różnych regionach

Szablony mogą być rozłożone na małe szablony wielokrotnego użytku, z których każdy ma określony cel i mogą być ponownie używane w różnych scenariuszach wdrażania. Aby wykonać wdrożenie, należy określić pojedynczy szablon znany jako główny lub główny szablon. Określa zasoby do wdrożenia, takie jak sieci wirtualne, maszyny wirtualne i aplikacje sieci Web. Szablon główny może również zawierać link do innego szablonu, co oznacza, że można zagnieżdżać szablony. Podobnie szablon zagnieżdżony może zawierać linki do innych szablonów. Można zagnieździć do maksymalnie pięciu poziomów.

Poniższy kod przedstawia sposób, w jaki parametr templateLink odwołuje się do zagnieżdżonego szablonu:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager oblicza główny szablon w czasie wykonywania i pobiera i oblicza każdy zagnieżdżony szablon. Po pobraniu wszystkich zagnieżdżonych szablonów szablon zostanie spłaszczony i zostanie zainicjowany dalsze przetwarzanie.

### <a name="make-linked-templates-accessible-across-clouds"></a>Udostępnianie szablonów połączonych w chmurach

Zastanów się tam, gdzie i jak mają być przechowywane wszystkie połączone szablony. W czasie wykonywania Azure Resource Manager pobierania — i w związku z tym wymaga bezpośredniego dostępu do — wszystkie połączone szablony. Typowym sposobem przechowywania szablonów zagnieżdżonych jest użycie usługi GitHub. Repozytorium GitHub może zawierać pliki, które są dostępne publicznie za pomocą adresu URL. Mimo że ta technika działa dobrze w przypadku chmury publicznej i suwerennych chmur, środowisko Azure Stack może znajdować się w sieci firmowej lub w odłączonej lokalizacji zdalnej bez wychodzącego dostępu do Internetu. W takich przypadkach Azure Resource Manager nie może pobrać zagnieżdżonych szablonów.

Lepszym rozwiązaniem w przypadku wdrożeń obejmujących wiele chmur jest przechowywanie połączonych szablonów w lokalizacji dostępnej dla chmury docelowej. Najlepiej, gdy wszystkie artefakty wdrożenia są utrzymywane w programie i wdrożone z potoku ciągłej integracji/ciągłego tworzenia oprogramowania (CI/CD). Alternatywnie można przechowywać zagnieżdżone szablony w kontenerze magazynu obiektów blob, z którego Azure Resource Manager mogą je pobrać.

Ponieważ magazyn obiektów BLOB w każdej chmurze używa innej nazwy domeny (FQDN) punktu końcowego, skonfiguruj szablon przy użyciu lokalizacji połączonych szablonów z dwoma parametrami. Parametry mogą akceptować dane wprowadzane przez użytkownika w czasie wdrażania. Szablony są zwykle tworzone i udostępniane przez wiele osób, dlatego najlepszym rozwiązaniem jest użycie standardowej nazwy dla tych parametrów. Konwencje nazewnictwa ułatwiają tworzenie szablonów do wielokrotnego użytku w regionach, chmurach i autorach.

W poniższym kodzie `_artifactsLocation` służy do wskazywania pojedynczej lokalizacji zawierającej wszystkie artefakty związane z wdrożeniem. Zwróć uwagę, że została podana wartość domyślna. W czasie wdrażania, jeśli dla programu nie określono wartości wejściowej `_artifactsLocation` , zostanie użyta wartość domyślna. `_artifactsLocationSasToken`Jest używany jako dane wejściowe dla `sasToken` . Wartość domyślna powinna być ciągiem pustym dla scenariuszy, w których `_artifactsLocation` nie jest zabezpieczony — na przykład publicznego repozytorium GitHub.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

W całym szablonie linki są generowane przez połączenie podstawowego identyfikatora URI (z `_artifactsLocation` parametru) ze ścieżką względną artefaktu i `_artifactsLocationSasToken` . Poniższy kod pokazuje, jak określić łącze do szablonu zagnieżdżonego za pomocą funkcji szablonu URI:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Korzystając z tej metody, `_artifactsLocation` zostanie użyta wartość domyślna parametru. Jeśli połączone szablony muszą zostać pobrane z innej lokalizacji, dane wejściowe parametru mogą być używane w czasie wdrażania, aby przesłonić wartość domyślną — nie trzeba zmieniać samego szablonu.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Użyj _artifactsLocation zamiast linków zakodowana

Poza używanym dla szablonów zagnieżdżonych, adres URL w `_artifactsLocation` parametrze jest używany jako podstawa dla wszystkich powiązanych artefaktów szablonu wdrożenia. Niektóre rozszerzenia maszyny wirtualnej zawierają link do skryptu przechowywanego poza szablonem. Dla tych rozszerzeń nie należy umieszczaj linków. Na przykład skrypt niestandardowy i rozszerzenia DSC programu PowerShell mogą łączyć się z zewnętrznym skryptem w witrynie GitHub, jak pokazano poniżej:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Zakodowana linki do skryptu potencjalnie uniemożliwia pomyślne wdrożenie szablonu w innej lokalizacji. Podczas konfigurowania zasobu maszyny wirtualnej Agent maszyny wirtualnej działający w ramach maszyny wirtualnej inicjuje pobieranie wszystkich skryptów połączonych z rozszerzeniem maszyny wirtualnej, a następnie zapisuje skrypty na lokalnym dysku maszyny wirtualnej. Takie podejście działa podobnie jak zagnieżdżone linki szablonów wyjaśnione wcześniej w sekcji "Użyj szablonów zagnieżdżonych w regionach".

Menedżer zasobów Pobiera zagnieżdżone szablony w czasie wykonywania. W przypadku rozszerzeń maszyn wirtualnych pobieranie wszelkich zewnętrznych artefaktów jest wykonywane przez agenta maszyny wirtualnej. Oprócz innego inicjatora pobierania artefaktu rozwiązanie w definicji szablonu jest takie samo. Użyj _artifactsLocation parametru z wartością domyślną ścieżki podstawowej, w której są przechowywane wszystkie artefakty (w tym skrypty rozszerzenia maszyny wirtualnej) i `_artifactsLocationSasToken` parametr dla danych wejściowych dla sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Aby skonstruować bezwzględny identyfikator URI artefaktu, preferowana metoda polega na użyciu funkcji szablonu URI zamiast funkcji szablonu concat. Zastępując linki stałe do skryptów w rozszerzeniu maszyny wirtualnej za pomocą funkcji szablonu identyfikatora URI, ta funkcja w szablonie jest skonfigurowana pod kątem spójności chmury.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

W tym podejściu wszystkie artefakty wdrożenia, w tym skrypty konfiguracyjne, mogą być przechowywane w tej samej lokalizacji z samym szablonem. Aby zmienić lokalizację wszystkich linków, należy określić inny podstawowy adres URL dla _parametrów artifactsLocation_.

## <a name="factor-in-differing-regional-capabilities"></a>Współczynnik w różnych możliwościach regionalnych

W przypadku projektowania Agile i ciągłego przepływu aktualizacji oraz nowych usług wprowadzonych na platformie Azure [regiony mogą różnić się](https://azure.microsoft.com/regions/services/) od dostępności usług lub aktualizacji. Po rygorystycznym przetestowaniu wewnętrznym nowe usługi lub aktualizacje istniejących usług są zwykle wprowadzane do małych odbiorców klientów uczestniczących w programie walidacji. Po pomyślnej weryfikacji klienta usługi lub aktualizacje są udostępniane w ramach podzestawu regionów świadczenia usługi Azure, a następnie wprowadzane do większej liczby regionów, przechodzące do suwerennych chmur i potencjalnie udostępniane klientom Azure Stack.

Wiedząc, że regiony i chmury platformy Azure mogą się różnić w swoich dostępnych usługach, możesz podejmować pewne aktywne decyzje dotyczące szablonów. Dobrym miejscem do rozpoczęcia jest zbadanie dostępnych dostawców zasobów dla chmury. Dostawca zasobów informuje zestaw zasobów i operacji, które są dostępne dla usługi platformy Azure.

Szablon wdraża i konfiguruje zasoby. Typ zasobu jest dostarczany przez dostawcę zasobów. Na przykład dostawca zasobów obliczeniowych (Microsoft. COMPUTE) udostępnia wiele typów zasobów, takich jak virtualMachines i availabilitySets. Każdy dostawca zasobów udostępnia interfejs API, który Azure Resource Manager zdefiniowany przez wspólną umowę, umożliwiając spójne, ujednolicone środowisko tworzenia dla wszystkich dostawców zasobów. Jednak dostawca zasobów dostępny na platformie Azure może nie być dostępny w chmurze suwerennej lub regionie Azure Stack.

![Dostawcy zasobów](./media/templates-cloud-consistency/resource-providers.png)

Aby sprawdzić dostawców zasobów dostępnych w danej chmurze, uruchom następujący skrypt w interfejsie wiersza polecenia platformy Azure ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Możesz również użyć następującego polecenia cmdlet programu PowerShell, aby wyświetlić dostępne dostawcy zasobów:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Weryfikowanie wersji wszystkich typów zasobów

Zestaw właściwości jest wspólny dla wszystkich typów zasobów, ale każdy zasób ma także własne specyficzne właściwości. Nowe funkcje i powiązane właściwości są dodawane do istniejących typów zasobów w czasie za pomocą nowej wersji interfejsu API. Zasób w szablonie ma własną Właściwość wersji interfejsu API — `apiVersion` . Ta wersja gwarantuje, że zmiany na platformie nie wpływają na istniejącą konfigurację zasobów w szablonie.

Nowe wersje interfejsu API wprowadzone do istniejących typów zasobów na globalnym platformie Azure mogą nie być natychmiast dostępne we wszystkich regionach, w chmurach suwerennych lub Azure Stack. Aby wyświetlić listę dostępnych dostawców zasobów, typów zasobów i wersji interfejsu API dla chmury, można użyć Eksplorator zasobów w Azure Portal. Wyszukaj Eksplorator zasobów w menu wszystkie usługi. Rozwiń węzeł dostawcy w Eksplorator zasobów, aby zwrócić wszystkich dostępnych dostawców zasobów, ich typy zasobów i wersje interfejsu API w tej chmurze.

Aby wyświetlić listę dostępnych wersji interfejsu API dla wszystkich typów zasobów w danej chmurze w interfejsie wiersza polecenia platformy Azure, uruchom następujący skrypt:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Można również użyć następującego polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Odwoływanie się do lokalizacji zasobów przy użyciu parametru

Szablon jest zawsze wdrażany w grupie zasobów, która znajduje się w regionie. Oprócz samego wdrożenia każdy zasób w szablonie ma również Właściwość Location, która służy do określania regionu do wdrożenia. Aby opracować szablon spójności chmury, musisz mieć dynamiczny sposób odwoływania się do lokalizacji zasobów, ponieważ każdy Azure Stack może zawierać unikatowe nazwy lokalizacji. Zazwyczaj zasoby są wdrażane w tym samym regionie, w którym znajduje się grupa zasobów, ale w celu obsługi scenariuszy, takich jak dostępność aplikacji między regionami, może być przydatne do rozprzestrzeniania się zasobów w różnych regionach.

Mimo że można umieszczaj nazwy regionów podczas określania właściwości zasobów w szablonie, to podejście nie gwarantuje, że szablon można wdrożyć w innych środowiskach Azure Stack, ponieważ nazwa regionu prawdopodobnie nie istnieje.

Aby obsłużyć różne regiony, Dodaj do szablonu lokalizację parametru wejściowego z wartością domyślną. Wartość domyślna zostanie użyta, jeśli podczas wdrażania nie zostanie określona żadna wartość.

Funkcja template `[resourceGroup()]` zwraca obiekt, który zawiera następujące pary klucz/wartość:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Odwołując się do klucza lokalizacji obiektu w elemencie DefaultValue parametru wejściowego, Azure Resource Manager w czasie wykonywania Zastąp `[resourceGroup().location]` funkcję szablonu nazwą lokalizacji grupy zasobów, w której wdrożono szablon.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Za pomocą tej funkcji szablonu można wdrożyć szablon w dowolnej chmurze bez konieczności wcześniejszego poznania nazw regionów. Ponadto lokalizacja określonego zasobu w szablonie może się różnić od lokalizacji grupy zasobów. W takim przypadku można skonfigurować go za pomocą dodatkowych parametrów wejściowych dla danego zasobu, podczas gdy inne zasoby w tym samym szablonie nadal korzystają z parametru wejściowego lokalizacji początkowej.

### <a name="track-versions-using-api-profiles"></a>Śledź wersje przy użyciu profilów interfejsu API

Może być bardzo trudne do śledzenia wszystkich dostępnych dostawców zasobów i powiązane wersje interfejsów API, które znajdują się w Azure Stack. Na przykład w czasie pisania Najnowsza wersja interfejsu API dla **Microsoft. COMPUTE/availabilitySets** na platformie Azure to `2018-04-01` , chociaż dostępna wersja interfejsu API jest wspólna dla platformy Azure i Azure Stack `2016-03-30` . Wspólna wersja interfejsu API dla **Microsoft. Storage/storageAccounts** współdzielona między wszystkimi lokalizacjami platformy azure i Azure Stack to `2016-01-01` , natomiast Najnowsza wersja interfejsu API na platformie Azure to `2018-02-01` .

Z tego powodu Menedżer zasobów wprowadził koncepcji profilów interfejsu API do szablonów. Bez profilów interfejsu API każdy zasób w szablonie jest skonfigurowany przy użyciu `apiVersion` elementu opisującego wersję interfejsu API dla tego konkretnego zasobu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Wersja profilu interfejsu API działa jako alias dla pojedynczej wersji interfejsu API dla każdego typu zasobu wspólnego dla platformy Azure i Azure Stack. Zamiast określania wersji interfejsu API dla każdego zasobu w szablonie, należy określić tylko wersję profilu interfejsu API w nowym elemencie głównym o nazwie `apiProfile` i pominąć `apiVersion` element dla poszczególnych zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Profil interfejsu API zapewnia, że wersje interfejsu API są dostępne w różnych lokalizacjach, więc nie trzeba ręcznie sprawdzać apiVersions, które są dostępne w określonej lokalizacji. Aby upewnić się, że wersje interfejsu API, do których odwołuje się profil interfejsu API, znajdują się w środowisku Azure Stack, operatory Azure Stack muszą zachować aktualność rozwiązania na podstawie zasad obsługi. Jeśli system ma więcej niż sześć miesięcy nieaktualnych, jest uznawany za niezgodny i środowisko musi zostać zaktualizowane.

Profil interfejsu API nie jest elementem wymaganym w szablonie. Nawet jeśli dodajesz element, będzie on używany tylko w przypadku zasobów, dla których nie `apiVersion` określono. Ten element umożliwia stopniowe wprowadzanie zmian, ale nie wymaga wprowadzania żadnych zmian w istniejących szablonach.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Sprawdź odwołania do punktów końcowych

Zasoby mogą mieć odwołania do innych usług na platformie. Na przykład publiczny adres IP może mieć przypisaną publiczną nazwę DNS. Chmura publiczna, suwerenne chmury i rozwiązania Azure Stack mają własne obszary nazw punktów końcowych. W większości przypadków zasób wymaga tylko prefiksu jako danych wejściowych w szablonie. W czasie wykonywania Azure Resource Manager dołącza do niej wartość punktu końcowego. Niektóre wartości punktów końcowych muszą być jawnie określone w szablonie.

> [!NOTE]
> Aby opracowywać szablony dla spójności chmury, nie umieszczaj przestrzeni nazw punktów końcowych.

Poniższe dwa przykłady to typowe przestrzenie nazw punktów końcowych, które należy jawnie określić podczas tworzenia zasobu:

* Konta magazynu (obiekty blob, kolejka, tabela i plik)
* Parametry połączenia dla baz danych i pamięci podręcznej platformy Azure dla Redis

Przestrzenie nazw punktów końcowych można także używać w danych wyjściowych szablonu jako informacje dla użytkownika po zakończeniu wdrożenia. Poniżej przedstawiono typowe przykłady:

* Konta magazynu (obiekty blob, kolejka, tabela i plik)
* Parametry połączenia (MySql, SQLServer, SqlAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* Wartość domainnamelabel publicznego adresu IP
* Usługi w chmurze

Ogólnie rzecz biorąc, należy unikać stałe punktów końcowych w szablonie. Najlepszym rozwiązaniem jest użycie funkcji szablonu odwołania do dynamicznego pobierania punktów końcowych. Na przykład punkt końcowy najczęściej stałe jest przestrzenią nazw punktu końcowego dla kont magazynu. Każde konto magazynu ma unikatową nazwę FQDN, która jest zbudowana przez połączenie nazwy konta magazynu z przestrzenią nazw punktu końcowego. Konto magazynu obiektów BLOB o nazwie mystorageaccount1 skutkuje innymi nazwami FQDN w zależności od chmury:

* `mystorageaccount1.blob.core.windows.net` Po utworzeniu w globalnej chmurze platformy Azure.
* `mystorageaccount1.blob.core.chinacloudapi.cn` utworzone w chmurze 21Vianet platformy Azure w Chinach.

Następująca funkcja szablonu odwołania pobiera przestrzeń nazw punktu końcowego z dostawcy zasobów magazynu:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Zamieniając wartość stałe punktu końcowego konta magazynu na `reference` funkcję szablonu, można użyć tego samego szablonu do wdrożenia w różnych środowiskach, które powiodło się bez wprowadzania jakichkolwiek zmian w odwołaniu do punktu końcowego.

### <a name="refer-to-existing-resources-by-unique-id"></a>Zapoznaj się z istniejącymi zasobami według unikatowego identyfikatora

Możesz również odwołać się do istniejącego zasobu z tej samej lub innej grupy zasobów, w ramach tej samej subskrypcji lub innej subskrypcji w ramach tej samej dzierżawy w tej samej chmurze. Aby pobrać właściwości zasobu, należy użyć unikatowego identyfikatora dla samego zasobu. `resourceId`Funkcja szablonu pobiera unikatowy identyfikator zasobu, taki jak SQL Server, jak pokazano w poniższym kodzie:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Następnie można użyć `resourceId` funkcji wewnątrz `reference` funkcji szablonu, aby pobrać właściwości bazy danych. Obiekt zwracany zawiera `fullyQualifiedDomainName` Właściwość, która przechowuje pełną wartość punktu końcowego. Ta wartość jest pobierana w czasie wykonywania i zapewnia przestrzeń nazw punktu końcowego specyficznego dla środowiska chmury. Aby zdefiniować parametry połączenia bez zakodowana przestrzeni nazw punktu końcowego, można odwołać się do właściwości obiektu zwracanego bezpośrednio w parametrach połączenia, jak pokazano:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Rozważ właściwości zasobów

Określone zasoby w środowiskach Azure Stack mają unikatowe właściwości, które należy wziąć pod uwagę w szablonie.

### <a name="ensure-vm-images-are-available"></a>Upewnij się, że obrazy maszyn wirtualnych są dostępne

Platforma Azure oferuje bogaty wybór obrazów maszyn wirtualnych. Obrazy te są tworzone i przygotowywane do wdrożenia przez firmę Microsoft i partnerów. Obrazy tworzą podstawę dla maszyn wirtualnych na platformie. Jednak szablon spójny w chmurze powinien odwoływać się tylko do dostępnych parametrów — w szczególności wydawcy, oferty i jednostki SKU obrazów maszyn wirtualnych dostępnych dla globalnych platform Azure, suwerennych chmur platformy Azure lub rozwiązania Azure Stack.

Aby pobrać listę dostępnych obrazów maszyn wirtualnych w lokalizacji, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az vm image list -all
```

Tę samą listę można pobrać przy użyciu polecenia cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) Azure PowerShell programu, a następnie określić lokalizację z `-Location` parametrem. Na przykład:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

To polecenie zajmuje kilka minut, aby zwrócić wszystkie dostępne obrazy w regionie Europa Zachodnia w globalnej chmurze platformy Azure.

Jeśli te obrazy maszyn wirtualnych zostały udostępnione do Azure Stack, zostanie wykorzystana cała dostępna pamięć. Aby obsłużyć nawet najmniejszą jednostkę skalowania, Azure Stack umożliwia wybranie obrazów, które mają zostać dodane do środowiska.

Poniższy przykład kodu przedstawia spójne podejście do odwoływania się do parametrów wydawcy, oferty i jednostki SKU w szablonach ARM:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Sprawdź rozmiary lokalnych maszyn wirtualnych

Aby opracować szablon spójności chmury, należy upewnić się, że rozmiar maszyny wirtualnej jest dostępny we wszystkich środowiskach docelowych. Rozmiary maszyn wirtualnych to grupowanie cech wydajności i możliwości. Niektóre rozmiary maszyn wirtualnych są zależne od sprzętu, na którym działa maszyna wirtualna. Na przykład jeśli chcesz wdrożyć maszynę wirtualną zoptymalizowaną od procesora GPU, sprzęt, na którym działa funkcja hypervisor, musi mieć sprzętowe procesory GPU.

Gdy firma Microsoft wprowadza nowy rozmiar maszyny wirtualnej z pewnymi zależnościami sprzętowymi, rozmiar maszyny wirtualnej jest zwykle udostępniany jako pierwszy w małym podzestawie regionów w chmurze platformy Azure. Później zostanie udostępniona dla innych regionów i chmur. Aby upewnić się, że rozmiar maszyny wirtualnej istnieje w każdej chmurze, która jest wdrażana, możesz pobrać dostępne rozmiary za pomocą następującego polecenia interfejsu CLI platformy Azure:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Aby uzyskać Azure PowerShell, użyj:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Aby uzyskać pełną listę dostępnych usług, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Sprawdź użycie usługi Azure Managed Disks w programie Azure Stack

Dyski zarządzane obsługują magazyn dla dzierżawy platformy Azure. Zamiast jawnie utworzyć konto magazynu i określić identyfikator URI wirtualnego dysku twardego (VHD), można użyć usługi Managed disks do niejawnie wykonywania tych akcji podczas wdrażania maszyny wirtualnej. Usługi Managed disks rozszerzają dostępność, umieszczając wszystkie dyski z maszyn wirtualnych w tym samym zestawie dostępności w różnych jednostkach magazynu. Ponadto istniejące wirtualne dyski twarde mogą być konwertowane ze standardu do magazynu w warstwie Premium z znacznie mniejszym przestojem.

Chociaż usługi Managed disks znajdują się w planie Azure Stack, nie są obecnie obsługiwane. Do momentu, w którym można tworzyć szablony spójne w chmurze dla Azure Stack, jawnie określając wirtualne dyski twarde przy użyciu `vhd` elementu w szablonie dla zasobu maszyny wirtualnej, jak pokazano poniżej:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

W przeciwieństwie do określania konfiguracji dysku zarządzanego w szablonie, Usuń `vhd` element z konfiguracji dysków.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Te same zmiany dotyczą także [dysków danych](../../virtual-machines/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Sprawdź, czy rozszerzenia maszyn wirtualnych są dostępne w Azure Stack

Inną kwestią dotyczącą spójności chmury jest użycie [rozszerzeń maszyny wirtualnej](../../virtual-machines/extensions/features-windows.md) w celu skonfigurowania zasobów w ramach maszyny wirtualnej. Nie wszystkie rozszerzenia maszyn wirtualnych są dostępne w Azure Stack. Szablon może określać zasoby przeznaczone dla rozszerzenia maszyny wirtualnej, tworząc zależności i warunki w ramach szablonu.

Na przykład jeśli chcesz skonfigurować maszynę wirtualną z Microsoft SQL Server, rozszerzenie maszyny wirtualnej może skonfigurować SQL Server jako część wdrożenia szablonu. Zastanów się, co się stanie, jeśli szablon wdrożenia zawiera również serwer aplikacji skonfigurowany do tworzenia bazy danych na maszynie wirtualnej z systemem SQL Server. Oprócz korzystania z rozszerzenia maszyny wirtualnej dla serwerów aplikacji można skonfigurować zależność serwera aplikacji po pomyślnym powrocie zasobu rozszerzenia maszyny wirtualnej SQL Server. Takie podejście zapewnia, że maszyna wirtualna z SQL Server jest skonfigurowana i dostępna, gdy serwer aplikacji zostanie utworzony w celu utworzenia bazy danych.

Deklaratywne podejście szablonu umożliwia zdefiniowanie stanu końcowego zasobów i ich zależności, podczas gdy platforma zajmuje się logiką wymaganą dla zależności.

#### <a name="check-that-vm-extensions-are-available"></a>Sprawdź, czy rozszerzenia maszyn wirtualnych są dostępne

Istnieje wiele typów rozszerzeń maszyn wirtualnych. Podczas opracowywania szablonu spójności w chmurze upewnij się, że używasz tylko rozszerzeń, które są dostępne we wszystkich regionach, w których szablon jest obiektem docelowym.

Aby pobrać listę rozszerzeń maszyn wirtualnych, które są dostępne dla określonego regionu (w tym przykładzie `myLocation` ), uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

Możesz również wykonać Azure PowerShell polecenie cmdlet [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) i użyć, `-Location` Aby określić lokalizację obrazu maszyny wirtualnej. Na przykład:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Upewnij się, że wersje są dostępne

Ponieważ rozszerzenia maszyn wirtualnych są zasobami Menedżer zasobów pierwszej firmy, mają własne wersje interfejsu API. Jak przedstawiono w poniższym kodzie, typ rozszerzenia maszyny wirtualnej to zasób zagnieżdżony w dostawcy zasobów Microsoft. COMPUTE.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Wersja interfejsu API zasobu rozszerzenia maszyny wirtualnej musi znajdować się we wszystkich lokalizacjach docelowych szablonu. Zależność lokalizacji działa podobnie jak dostępność wersji interfejsu API dostawcy zasobów omówiona wcześniej w sekcji "Weryfikowanie wersji wszystkich typów zasobów".

Aby pobrać listę dostępnych wersji interfejsu API dla zasobu rozszerzenia maszyny wirtualnej, należy użyć polecenia cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) z dostawcą zasobów **Microsoft. COMPUTE** , jak pokazano poniżej:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Można również użyć rozszerzeń maszyny wirtualnej w zestawach skalowania maszyn wirtualnych. Obowiązują te same warunki lokalizacji. Aby opracować szablon spójności w chmurze, upewnij się, że wersje interfejsu API są dostępne we wszystkich lokalizacjach, w których planujesz wdrożyć program. Aby pobrać wersje interfejsu API zasobu rozszerzenia maszyny wirtualnej dla zestawów skalowania, użyj tego samego polecenia cmdlet jak poprzednio, ale Określ typ zasobu zestawu skalowania maszyn wirtualnych, jak pokazano poniżej:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Wszystkie określone rozszerzenia są również w wersji. Ta wersja jest pokazana we `typeHandlerVersion` Właściwości rozszerzenia maszyny wirtualnej. Upewnij się, że wersja określona w `typeHandlerVersion` elemencie rozszerzeń maszyny wirtualnej szablonu jest dostępna w lokalizacjach, w których planujesz wdrożyć szablon. Na przykład następujący kod określa wersję 1,7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Aby pobrać listę dostępnych wersji dla określonego rozszerzenia maszyny wirtualnej, użyj polecenia cmdlet [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) . Poniższy przykład pobiera dostępne wersje rozszerzenia maszyny wirtualnej DSC programu PowerShell (Konfiguracja żądanego stanu) z **lokalizacji**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Aby uzyskać listę wydawców, użyj polecenia [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) . Aby zażądać typu, użyj polecenie [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) commend.

## <a name="tips-for-testing-and-automation"></a>Porady dotyczące testowania i automatyzacji

Jest to wyzwanie, aby śledzić wszystkie powiązane ustawienia, możliwości i ograniczenia podczas tworzenia szablonu. Typowym podejściem jest opracowanie i przetestowanie szablonów w ramach jednej chmury, zanim inne lokalizacje będą wskazywane. Jednak przede wszystkim testy są wykonywane w procesie tworzenia, tym mniej Rozwiązywanie problemów i pisanie kodu przez zespół programistyczny będzie konieczne. Wdrożenia, które nie powiodły się z powodu zależności lokalizacji, mogą być czasochłonne w celu rozwiązywania problemów. Dlatego zalecamy automatyczne testowanie tak szybko, jak to możliwe w cyklu tworzenia. Ostatecznie potrzeba mniej czasu na programowanie i mniejszej ilości zasobów, a artefakty spójne z chmurą staną się jeszcze bardziej cenne.

Na poniższej ilustracji przedstawiono typowy przykład procesu tworzenia zespołu przy użyciu zintegrowanego środowiska programistycznego (IDE). Na różnych etapach osi czasu są wykonywane różne typy testów. W tym przypadku dwaj deweloperzy pracują nad tym samym rozwiązaniem, ale ten scenariusz dotyczy jednak jednego dewelopera lub dużego zespołu. Każdy deweloper zazwyczaj tworzy lokalną kopię centralnego repozytorium, co umożliwia każdemu współdziałanie z lokalną kopią bez wpływania na innych użytkowników, którzy mogą pracować nad tymi samymi plikami.

![Diagram przedstawia dwa zestawy testów jednostkowych i testów integracji równolegle na lokalnie I D E, które scalają w przepływie projektowania C/C D w testy jednostkowe, następnie testy integracji, a następnie test wdrożenia, a następnie wdrożenie.](./media/templates-cloud-consistency/workflow.png)

Należy wziąć pod uwagę następujące wskazówki dotyczące testowania i automatyzacji:

* Korzystaj z narzędzi do testowania. Na przykład Visual Studio Code i Visual Studio obejmują funkcję IntelliSense i inne funkcje, które mogą pomóc w sprawdzeniu poprawności szablonów.
* Aby poprawić jakość kodu podczas programowania w lokalnym środowisku IDE, należy przeprowadzić analizę kodu statycznego za pomocą testów jednostkowych i testów integracji.
* W celu uzyskania jeszcze lepszego środowiska podczas początkowego opracowywania testy jednostkowe i testy integracji powinny być ostrzegane tylko wtedy, gdy wystąpi problem i kontynuują testy. W ten sposób można zidentyfikować problemy, które należy rozwiązać, i określić priorytet kolejności zmian, nazywane również wdrożeniem opartym na testach (TDD).
* Należy pamiętać, że niektóre testy można wykonać bez połączenia z Azure Resource Manager. Inne, takie jak testowanie wdrożenia szablonu, wymagają Menedżer zasobów wykonywania pewnych akcji, których nie można wykonać w trybie offline.
* Testowanie szablonu wdrożenia względem interfejsu API sprawdzania poprawności nie jest równe rzeczywistemu wdrożeniu. Ponadto nawet w przypadku wdrażania szablonu z pliku lokalnego wszystkie odwołania do szablonów zagnieżdżonych w szablonie są pobierane przez Menedżer zasobów bezpośrednio, a artefakty przywoływane przez rozszerzenia maszyn wirtualnych są pobierane przez agenta maszyny wirtualnej działającego w ramach wdrożonej maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

* [Zagadnienia dotyczące szablonu Azure Resource Manager](/azure-stack/user/azure-stack-develop-templates)
* [Najlepsze rozwiązania dotyczące szablonów ARM](template-syntax.md)
