---
title: Najlepsze rozwiązania dotyczące szablonów
description: Opisuje zalecane podejścia do tworzenia szablonów Azure Resource Manager (szablony ARM). Oferuje sugestie pozwalające uniknąć typowych problemów związanych z korzystaniem z szablonów.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 583a113df9cdb1951daf1002dd69531f050cfb54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258001"
---
# <a name="arm-template-best-practices"></a>Najlepsze praktyki dotyczące szablonów ARM

W tym artykule przedstawiono sposób użycia zalecanych praktyk podczas konstruowania szablonu Azure Resource Manager (szablon ARM). Te zalecenia pozwalają uniknąć typowych problemów związanych z wdrażaniem rozwiązania przy użyciu szablonu ARM.

## <a name="template-limits"></a>Limity szablonów

Ogranicz rozmiar szablonu do 4 MB, a każdy plik parametrów do 64 KB. Limit 4 MB ma zastosowanie do końcowego stanu szablonu po jego rozszerzeniu z powtarzającymi się definicjami zasobów i wartościami zmiennych i parametrów.

Jest również ograniczona do:

* parametry 256
* 256 zmienne
* 800 zasobów (w tym liczba kopii)
* 64 wartości wyjściowe
* 24 576 znaków w wyrażeniu szablonu

Można przekroczyć niektóre limity szablonów przy użyciu zagnieżdżonego szablonu. Aby uzyskać więcej informacji, zobacz [Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure](linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub danych wyjściowych, można połączyć kilka wartości z obiektem. Aby uzyskać więcej informacji, zobacz [obiekty jako parametry](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).

## <a name="resource-group"></a>Grupa zasobów

Podczas wdrażania zasobów w grupie zasobów Grupa zasobów przechowuje metadane dotyczące zasobów. Metadane są przechowywane w lokalizacji grupy zasobów.

Jeśli region grupy zasobów jest tymczasowo niedostępny, nie można zaktualizować zasobów w grupie zasobów, ponieważ metadane są niedostępne. Zasoby w innych regionach będą nadal działać zgodnie z oczekiwaniami, ale nie można ich zaktualizować. Aby zminimalizować ryzyko, Znajdź grupę zasobów i zasoby w tym samym regionie.

## <a name="parameters"></a>Parametry

Informacje przedstawione w tej sekcji mogą być przydatne podczas pracy z [parametrami](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Ogólne zalecenia dotyczące parametrów

* Zminimalizuj użycie parametrów. Zamiast tego należy użyć zmiennych lub wartości literału dla właściwości, które nie muszą być określone podczas wdrażania.

* Użyj notacji CamelCase przypadku nazw parametrów.

* Użyj parametrów dla ustawień, które różnią się w zależności od środowiska, takich jak jednostka SKU, rozmiar lub pojemność.

* Użyj parametrów nazw zasobów, które chcesz określić do łatwej identyfikacji.

* Podaj opis każdego parametru w metadanych.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* Zdefiniuj domyślne wartości parametrów, które nie są poufne. Określając wartość domyślną, łatwiej jest wdrożyć szablon i użytkownicy szablonu widzą przykład odpowiedniej wartości. Każda wartość domyślna parametru musi być prawidłowa dla wszystkich użytkowników w domyślnej konfiguracji wdrożenia.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "defaultValue": "Standard_GRS",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* Aby określić opcjonalny parametr, nie używaj pustego ciągu jako wartości domyślnej. Zamiast tego należy użyć wartości literału lub wyrażenia języka w celu skonstruowania wartości.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   }
   ```

* Używaj `allowedValues` oszczędnie. Użyj go tylko wtedy, gdy musisz upewnić się, że niektóre wartości nie są uwzględnione w dozwolonych opcjach. Jeśli używasz `allowedValues` zbyt szerokiego okresu, możesz zablokować prawidłowe wdrożenia, nie zachowując Aktualności listy.

* Jeśli nazwa parametru w szablonie jest zgodna z parametrem w poleceniu wdrażania programu PowerShell, Menedżer zasobów rozwiązuje ten konflikt nazw przez dodanie przyrostka **FromTemplate** do parametru szablonu. Na przykład jeśli w szablonie zostanie uwzględniony parametr o nazwie **ResourceGroupName** , powoduje to konflikt z parametrem **ResourceGroupName** w poleceniu cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Podczas wdrażania zostanie wyświetlony monit o podanie wartości dla **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Zalecenia dotyczące zabezpieczeń parametrów

* Zawsze używaj parametrów nazw użytkowników i haseł (lub wpisów tajnych).

* Używane `securestring` dla wszystkich haseł i wpisów tajnych. W przypadku przekazania poufnych danych do obiektu JSON należy użyć `secureObject` typu. Nie można odczytać parametrów szablonu z bezpiecznym ciągiem lub bezpiecznych typów obiektów po wdrożeniu zasobów.

    ```json
    "parameters": {
      "secretValue": {
        "type": "securestring",
        "metadata": {
          "description": "The value of the secret to store in the vault."
        }
      }
    }
    ```

* Nie należy podawać wartości domyślnych dla nazw użytkowników, haseł ani wartości, które wymagają `secureString` typu.

* Nie należy podawać wartości domyślnych dla właściwości, które zwiększają obszar ataków aplikacji.

### <a name="location-recommendations-for-parameters"></a>Zalecenia dotyczące lokalizacji parametrów

* Użyj parametru, aby określić lokalizację dla zasobów, i ustaw wartość domyślną na `resourceGroup().location` . Podanie parametru Location umożliwia użytkownikom szablonu określenie lokalizacji, w której mają uprawnienia do wdrażania zasobów.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* Nie określaj `allowedValues` parametru Location. Określone lokalizacje mogą nie być dostępne we wszystkich chmurach.

* Użyj wartości parametru Location dla zasobów, które mogą znajdować się w tej samej lokalizacji. To podejście minimalizuje liczbę użytkowników, którzy otrzymają prośbę o podanie informacji o lokalizacji.

* W przypadku zasobów, które nie są dostępne we wszystkich lokalizacjach, należy użyć oddzielnego parametru lub określić wartość lokalizacji literału.

## <a name="variables"></a>Zmienne

Poniższe informacje mogą być przydatne podczas pracy ze [zmiennymi](template-variables.md):

* Użyj notacji CamelCase przypadku nazw zmiennych.

* Użyj zmiennych dla wartości, które mają być używane więcej niż raz w szablonie. Jeśli wartość jest używana tylko raz, ustalona wartość ułatwia odczytywanie szablonu.

* Używaj zmiennych dla wartości, które są konstruowane na podstawie złożonego rozmieszczenia funkcji szablonu. Szablon jest łatwiejszy do odczytania, gdy złożone wyrażenie pojawia się tylko w zmiennych.

* Nie można użyć funkcji [Reference](template-functions-resource.md#reference) w `variables` sekcji szablonu. `reference`Funkcja dziedziczy wartość ze stanu środowiska uruchomieniowego zasobu. Jednak zmienne są rozwiązywane podczas początkowej analizy szablonu. Skonstruuj wartości, które wymagają `reference` funkcji bezpośrednio w `resources` sekcji lub `outputs` szablonu.

* Dołącz zmienne nazw zasobów, które muszą być unikatowe.

* Użyj [pętli kopiowania w zmiennych](copy-variables.md) , aby utworzyć powtarzający się wzorzec obiektów JSON.

* Usuń nieużywane zmienne.

## <a name="api-version"></a>Wersja interfejsu API

Ustaw `apiVersion` Właściwość na zakodowaną wersję interfejsu API dla typu zasobu. W przypadku tworzenia nowego szablonu zalecamy użycie najnowszej wersji interfejsu API dla typu zasobu. Aby określić dostępne wartości, zobacz [Dokumentacja szablonu](/azure/templates/).

Gdy szablon działa zgodnie z oczekiwaniami, zalecamy dalsze korzystanie z tej samej wersji interfejsu API. Korzystając z tej samej wersji interfejsu API, nie trzeba martwić się o istotne zmiany, które mogą zostać wprowadzone w nowszych wersjach.

Nie używaj parametru dla wersji interfejsu API. Właściwości i wartości zasobów mogą się różnić w zależności od wersji interfejsu API. Funkcja IntelliSense w edytorze kodu nie może określić poprawnego schematu, gdy wersja interfejsu API jest ustawiona na wartość parametru. Jeśli przejdziesz do wersji interfejsu API, która nie jest zgodna z właściwościami w szablonie, wdrożenie zakończy się niepowodzeniem.

Nie używaj zmiennych dla wersji interfejsu API. W szczególności nie należy używać [funkcji Providers](template-functions-resource.md#providers) do dynamicznego pobierania wersji interfejsu API podczas wdrażania. Dynamicznie pobierana wersja interfejsu API może nie być zgodna z właściwościami w szablonie.

## <a name="resource-dependencies"></a>Zależności zasobów

Podczas wybierania [zależności](define-resource-dependency.md) , które należy ustawić, należy przestrzegać następujących wytycznych:

* Użyj `reference` funkcji i przekaż nazwę zasobu, aby ustawić niejawną zależność między zasobami, które muszą udostępniać właściwość. Nie dodawaj jawnego `dependsOn` elementu, gdy została już zdefiniowana zależność niejawna. Takie podejście zmniejsza ryzyko związane z niepotrzebnymi zależnościami. Przykład ustawiania zależności niejawnej można znaleźć w temacie [Reference and list Functions](define-resource-dependency.md#reference-and-list-functions).

* Ustaw zasób podrzędny jako zależny od jego zasobu nadrzędnego.

* Zasoby z [elementem Condition](conditional-resource-deployment.md) ustawionym na wartość false są automatycznie usuwane z kolejności zależności. Ustaw zależności, tak jakby zasób jest zawsze wdrażany.

* Zezwól na kaskadowe zależności bez ustawiania ich jawnie. Na przykład maszyna wirtualna zależy od interfejsu sieci wirtualnej, a interfejs sieci wirtualnej zależy od sieci wirtualnej i publicznych adresów IP. W związku z tym, maszyna wirtualna jest wdrażana po wszystkich trzech zasobach, ale nie ustawia jawnie maszyny wirtualnej jako zależnej od wszystkich trzech zasobów. Takie podejście wyjaśnia kolejność zależności i ułatwia późniejsze zmiany szablonu.

* Jeśli wartość można ustalić przed wdrożeniem, spróbuj wdrożyć zasób bez zależności. Na przykład jeśli wartość konfiguracji wymaga nazwy innego zasobu, może to oznaczać, że zależność nie jest wymagana. Te wskazówki nie zawsze działają, ponieważ niektóre zasoby weryfikują istnienie innego zasobu. Jeśli wystąpi błąd, Dodaj zależność.

## <a name="resources"></a>Zasoby

Poniższe informacje mogą być przydatne podczas pracy z [zasobami](template-syntax.md#resources):

* Aby pomóc innym współautorom zrozumieć cel zasobu, określ `comments` dla każdego zasobu w szablonie.

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* Jeśli używasz *publicznego punktu końcowego* w szablonie (na przykład publicznego punktu końcowego usługi Azure Blob Storage), *nie używaj twardej* nazwy przestrzeni nazw. Użyj `reference` funkcji, aby dynamicznie pobrać przestrzeń nazw. Tego podejścia można użyć do wdrożenia szablonu w różnych publicznych środowiskach nazw bez ręcznej zmiany punktu końcowego w szablonie. Ustaw wersję interfejsu API na tę samą wersję, która jest używana dla konta magazynu w szablonie.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   Jeśli konto magazynu zostanie wdrożone w tym samym szablonie, który tworzysz, a nazwa konta magazynu nie jest współdzielona z innym zasobem w szablonie, nie trzeba określać przestrzeni nazw dostawcy ani `apiVersion` podczas odwoływania się do zasobu. Poniższy przykład pokazuje uproszczoną składnię.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   Można również odwołać się do istniejącego konta magazynu znajdującego się w innej grupie zasobów.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* Przypisz publiczne adresy IP do maszyny wirtualnej tylko wtedy, gdy jest ona wymagana przez aplikację. Aby nawiązać połączenie z maszyną wirtualną (VM) na potrzeby debugowania lub w celach zarządzania lub administracyjne, Użyj reguł NAT dla ruchu przychodzącego, bramy sieci wirtualnej lub serwera przesiadkowego.

     Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi, zobacz:

   * [Uruchamianie maszyn wirtualnych dla architektury N-warstwowej na platformie Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Konfigurowanie dostępu do usługi WinRM dla maszyn wirtualnych w Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny wirtualnej przy użyciu Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Zezwalanie na dostęp zewnętrzny do maszyny wirtualnej przy użyciu programu PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Zezwalanie na dostęp zewnętrzny do maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/linux/nsg-quickstart.md)

* `domainNameLabel`Właściwość publicznych adresów IP musi być unikatowa. `domainNameLabel`Wartość musi mieć długość od 3 do 63 znaków i postępować zgodnie z regułami określonymi w tym wyrażeniu regularnym: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . Ponieważ `uniqueString` funkcja generuje ciąg o długości 13 znaków, `dnsPrefixString` parametr jest ograniczony do 50 znaków.

    ```json
    "parameters": {
      "dnsPrefixString": {
        "type": "string",
        "maxLength": 50,
        "metadata": {
          "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
        }
      }
    },
    "variables": {
      "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
    }
    ```

* Po dodaniu hasła do niestandardowego rozszerzenia skryptu Użyj `commandToExecute` Właściwości we `protectedSettings` właściwości.

    ```json
    "properties": {
      "publisher": "Microsoft.Azure.Extensions",
      "type": "CustomScript",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
        ]
      },
      "protectedSettings": {
        "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
      }
    }
    ```

   > [!NOTE]
   > Aby upewnić się, że wpisy tajne są szyfrowane, gdy są one przesyłane jako parametry do maszyn wirtualnych i rozszerzeń, użyj `protectedSettings` właściwości odpowiednich rozszerzeń.

* Określ wartości jawne dla właściwości, które mają wartości domyślne, które mogą ulec zmianie w czasie. Na przykład jeśli wdrażasz klaster AKS, możesz określić lub pominąć `kubernetesVersion` Właściwość. Jeśli nie zostanie on określony, [klaster zostanie domyślnie przystosowana do wersji pomocniczej N-1 i najnowszej poprawki](../../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions). W przypadku wdrażania klastra przy użyciu szablonu ARM to zachowanie domyślne może nie być oczekiwane. Ponowne wdrożenie szablonu może spowodować nieoczekiwane uaktualnienie klastra do nowej wersji Kubernetes. Zamiast tego należy rozważyć określenie jawnego numeru wersji, a następnie jego ręczne zmianę, gdy wszystko jest gotowe do uaktualnienia klastra.

## <a name="use-test-toolkit"></a>Korzystanie z zestawu narzędzi testowych

Zestaw narzędzi do testowania szablonów ARM jest skryptem, który sprawdza, czy szablon używa zalecanych praktyk. Jeśli szablon nie jest zgodny z zalecanymi praktykami, zwraca listę ostrzeżeń z sugerowanymi zmianami. Zestaw narzędzi testowych może pomóc Ci w dowiedzieć się, jak zaimplementować najlepsze rozwiązania w szablonie.

Po zakończeniu szablonu Uruchom zestaw narzędzi test Toolkit, aby zobaczyć, czy można poprawić jego implementację. Aby uzyskać więcej informacji, zobacz [Korzystanie z zestawu narzędzi usługi ARM Template test Toolkit](test-toolkit.md).

## <a name="next-steps"></a>Następne kroki

* Informacje o strukturze pliku szablonu znajdują się w temacie [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia szablonów, które działają we wszystkich środowiskach w chmurze platformy Azure, zobacz [opracowywanie szablonów ARM pod kątem spójności chmury](templates-cloud-consistency.md).
