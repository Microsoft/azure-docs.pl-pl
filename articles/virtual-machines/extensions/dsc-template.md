---
title: Rozszerzenie konfiguracji żądanego stanu z szablonami Azure Resource Manager
description: Dowiedz się więcej na temat definicji szablonu Menedżer zasobów dla rozszerzenia konfiguracji żądanego stanu (DSC) na platformie Azure.
services: virtual-machines
author: mgoedtel
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/09/2021
ms.author: magoedte
ms.openlocfilehash: 6d22e93fe8bd36e24520108bc310d5b08c25120c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560025"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Rozszerzenie konfiguracji żądanego stanu z szablonami Azure Resource Manager

W tym artykule opisano szablon Azure Resource Manager na potrzeby [obsługi rozszerzenia konfiguracji żądanego stanu (DSC)](dsc-overview.md). Wiele przykładów używa **RegistrationURL** (dostarczonych jako ciąg) i **RegistrationKey** (dostarczonych jako [PSCredential](/dotnet/api/system.management.automation.pscredential) do dołączenia do Azure Automation. Aby uzyskać szczegółowe informacje na temat uzyskiwania tych wartości, zobacz sekcję dołączanie [maszyn w celu zarządzania przez Azure Automation konfiguracja stanu — bezpieczna Rejestracja](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration).

> [!NOTE]
> Mogą wystąpić nieco inne przykłady schematu. Zmiana schematu wystąpiła w wersji 2016 października. Aby uzyskać szczegółowe informacje, zobacz [Aktualizacja w poprzednim formacie](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Przykład szablonu dla maszyny wirtualnej z systemem Windows

Poniższy fragment kodu znajduje się w sekcji **zasobów** szablonu.
Rozszerzenie DSC dziedziczy domyślne właściwości rozszerzenia.
Aby uzyskać więcej informacji, zobacz [Klasa VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/Microsoft.Powershell.DSC')]",
  "apiVersion": "2018-06-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Przykład szablonu dla zestawów skalowania maszyn wirtualnych z systemem Windows

Węzeł zestawu skalowania maszyn wirtualnych ma sekcję **Właściwości** , która ma atrybut **VirtualMachineProfile, extensionProfile** .
W obszarze **rozszerzenia** Dodaj szczegóły rozszerzenia DSC.

Rozszerzenie DSC dziedziczy domyślne właściwości rozszerzenia.
Aby uzyskać więcej informacji, zobacz [Klasa VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Szczegółowe informacje o ustawieniach

Użyj poniższego schematu w sekcji **ustawień** rozszerzenia DSC platformy Azure w szablonie Menedżer zasobów.

Aby uzyskać listę argumentów dostępnych dla domyślnego skryptu konfiguracji, zobacz [domyślny skrypt konfiguracji](#default-configuration-script).

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Szczegóły

| Nazwa właściwości | Typ | Opis |
| --- | --- | --- |
| Settings. wmfVersion |ciąg |Określa wersję programu Windows Management Framework (WMF), która powinna zostać zainstalowana na maszynie wirtualnej. Ustawienie tej właściwości na **Najnowsza** spowoduje zainstalowanie najnowszej wersji programu WMF. Obecnie jedyne możliwe wartości tej właściwości to **4,0**, **5,0**, **5,1** i **najnowsze**. Te możliwe wartości podlegają aktualizacjom. Wartość domyślna to **Najnowsza**. |
| settings.configwersja. URL |ciąg |Określa lokalizację adresu URL, z którego ma zostać pobrany plik konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu współdzielonego, ustaw właściwość **protectedSettings.configurationUrlSasToken** na wartość tokenu SAS. Ta właściwość jest wymagana, jeśli **settings.configwersja. Script** lub **settings.configwersja. Function** . Jeśli dla tych właściwości nie podano wartości, rozszerzenie wywoła domyślny skrypt konfiguracji w celu skonfigurowania metadanych lokalizacji Configuration Manager (LCM) i należy podać argumenty. |
| settings.configwersja. Script |ciąg |Określa nazwę pliku skryptu, który zawiera definicję konfiguracji DSC. Ten skrypt musi znajdować się w folderze głównym pliku zip, który jest pobierany z adresu URL określonego przez właściwość **settings.configwersja. URL** . Ta właściwość jest wymagana, jeśli jest zdefiniowany **settings.configwersja. URL** lub **settings.configwersja. Script** . Jeśli nie podano wartości dla tych właściwości, rozszerzenie wywołuje domyślny skrypt konfiguracji w celu ustawienia metadanych LCM i należy podać argumenty. |
| settings.configwersja. Function |ciąg |Określa nazwę konfiguracji DSC. Konfiguracja o nazwie musi być uwzględniona w skrypcie, który **settings.configwersja. Script** definiuje. Ta właściwość jest wymagana, jeśli określono **settings.configwersja. URL** lub **settings.configwersja. Function** . Jeśli nie podano wartości dla tych właściwości, rozszerzenie wywołuje domyślny skrypt konfiguracji w celu ustawienia metadanych LCM i należy podać argumenty. |
| settings.configurationArguments |Kolekcja |Definiuje wszystkie parametry, które mają zostać przekazane do konfiguracji DSC. Ta właściwość nie jest zaszyfrowana. |
| settings.configurationData. URL |ciąg |Określa adres URL, z którego należy pobrać plik danych konfiguracji (psd1), który ma być używany jako dane wejściowe dla konfiguracji DSC. Jeśli podany adres URL wymaga tokenu sygnatury dostępu współdzielonego, ustaw właściwość **protectedSettings.configurationDataUrlSasToken** na wartość tokenu SAS. |
| Settings. privacy. DataCollection |ciąg |Włącza lub wyłącza zbieranie danych telemetrycznych. Jedyne możliwe wartości tej właściwości to **enable**, **disable**, **""** lub **$null**. Pozostawienie tej właściwości pustej lub wartości null włącza telemetrię. Wartość domyślna to **""**. Aby uzyskać więcej informacji, zobacz [zbieranie danych rozszerzenia DSC platformy Azure](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/). |
| Settings. advancedOptions. downloadMappings |Kolekcja |Definiuje alternatywne lokalizacje, z których ma zostać pobrane WMF. Aby uzyskać więcej informacji, zobacz [rozszerzenie Azure DSC 2,8 i sposób mapowania pobierania zależności rozszerzeń do własnej lokalizacji](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/). |
| protectedSettings.configurationArguments |Kolekcja |Definiuje wszystkie parametry, które mają zostać przekazane do konfiguracji DSC. Ta właściwość jest zaszyfrowana. |
| protectedSettings.configurationUrlSasToken |ciąg |Określa token sygnatury dostępu współdzielonego, aby uzyskać dostęp do adresu URL, który **settings.configwersja. URL** definiuje. Ta właściwość jest zaszyfrowana. |
| protectedSettings.configurationDataUrlSasToken |ciąg |Określa token sygnatury dostępu współdzielonego, aby uzyskać dostęp do adresu URL, który  **settings.configurationData. URL** definiuje. Ta właściwość jest zaszyfrowana. |

## <a name="default-configuration-script"></a>Domyślny skrypt konfiguracji

Aby uzyskać więcej informacji na temat następujących wartości, zobacz [Local Configuration Manager — ustawienia podstawowe](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Możesz użyć domyślnego skryptu konfiguracji rozszerzenia DSC, aby skonfigurować tylko właściwości LCM wymienione w poniższej tabeli.

| Nazwa właściwości | Typ | Opis |
| --- | --- | --- |
| protectedSettings.configurationArguments. RegistrationKey |PSCredential |Wymagana właściwość. Określa klucz używany przez węzeł do zarejestrowania się w usłudze Azure Automation jako hasło obiektu poświadczeń programu PowerShell. Tę wartość można automatycznie wykryć przy użyciu metody **ListKeys** na koncie usługi Automation.  Zapoznaj się z [przykładem](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments. RegistrationUrl |ciąg |Wymagana właściwość. Określa adres URL punktu końcowego automatyzacji, gdzie węzeł próbuje zarejestrować. Tę wartość można automatycznie wykryć przy użyciu metody **referencyjnej** względem konta usługi Automation. |
| settings.configurationArguments. NodeConfigurationName |ciąg |Wymagana właściwość. Określa konfigurację węzła w ramach konta usługi Automation, która ma zostać przypisana do węzła. |
| settings.configurationArguments.ConfigurationMode |ciąg |Określa tryb LCM. Prawidłowe opcje to **ApplyOnly**, **ApplyandMonitor** i **ApplyandAutoCorrect**.  Wartość domyślna to **ApplyandMonitor**. |
| settings.configurationArguments. RefreshFrequencyMins | równ | Określa, jak często LCM próbuje sprawdzić konto usługi Automation pod kątem aktualizacji.  Wartość domyślna to **30**.  Wartość minimalna to **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | równ | Określa, jak często LCM sprawdza bieżącą konfigurację. Wartość domyślna to **15**. Wartość minimalna to **15**. |
| settings.configurationArguments. RebootNodeIfNeeded | boolean | Określa, czy węzeł może być automatycznie uruchamiany ponownie, jeśli operacja DSC zostanie zażądana. Wartość domyślna to **false**. |
| settings.configurationArguments. ActionAfterReboot | ciąg | Określa, co się stanie po ponownym uruchomieniu podczas stosowania konfiguracji. Prawidłowe opcje to **ContinueConfiguration** i **StopConfiguration**. Wartość domyślna to **ContinueConfiguration**. |
| settings.configurationArguments. AllowModuleOverwrite | boolean | Określa, czy LCM zastępuje istniejące moduły w węźle. Wartość domyślna to **false**. |

## <a name="settings-vs-protectedsettings"></a>ustawienia a protectedSettings

Wszystkie ustawienia są zapisywane w pliku tekstowym ustawień na maszynie wirtualnej.
Właściwości wymienione w obszarze **Ustawienia** są właściwościami publicznymi.
Właściwości publiczne nie są szyfrowane w pliku tekstowym ustawień.
Właściwości wymienione w obszarze **protectedSettings** są szyfrowane za pomocą certyfikatu i nie są wyświetlane w postaci zwykłego tekstu w pliku ustawień na maszynie wirtualnej.

Jeśli konfiguracja wymaga poświadczeń, można dołączyć poświadczenia w programie **protectedSettings**:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Przykładowy skrypt konfiguracji

W poniższym przykładzie pokazano domyślne zachowanie rozszerzenia DSC, które ma na celu dostarczenie ustawień metadanych w celu przeprowadzenia LCM i zarejestrowania się w usłudze Automation DSC.
Argumenty konfiguracji są wymagane.
Argumenty konfiguracji są przesyłane do domyślnego skryptu konfiguracji w celu ustawienia metadanych LCM.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Przykład użycia skryptu konfiguracji w usłudze Azure Storage

Poniższy przykład pochodzi z [omówienia obsługi rozszerzeń DSC](dsc-overview.md).
Ten przykład używa szablonów Menedżer zasobów zamiast poleceń cmdlet do wdrożenia rozszerzenia.
Zapisz konfigurację IisInstall.ps1, umieść ją w pliku zip (przykład: `iisinstall.zip` ), a następnie Przekaż plik do dostępnego adresu URL.
Ten przykład korzysta z usługi Azure Blob Storage, ale pliki zip można pobrać z dowolnej lokalizacji.

W szablonie Menedżer zasobów następujący kod instruuje maszynę wirtualną, aby pobrała właściwy plik, a następnie uruchomi odpowiednią funkcję programu PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Przykład użycia przywoływanych wartości rejestracji Azure Automation

Poniższy przykład pobiera **RegistrationUrl** i **RegistrationKey** , odwołując się do właściwości konta Azure Automation i przy użyciu metody  **ListKeys** w celu pobrania klucza podstawowego (0).  W tym przykładzie parametry **automationAccountName** i **NodeConfigName** zostały przekazane do szablonu.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Aktualizacja z poprzedniego formatu

Wszystkie ustawienia w poprzednim formacie rozszerzenia (i które mają właściwości publiczne **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken** lub **Właściwości**) są automatycznie dostosowywane do bieżącego formatu rozszerzenia.
Działają one tak jak wcześniej.

Poniższy schemat pokazuje, jak wygląda poprzedni Schemat ustawień:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Oto, jak poprzedni format dostosowuje się do bieżącego formatu:

| Bieżąca nazwa właściwości | Poprzedni odpowiednik schematu |
| --- | --- |
| Settings. wmfVersion |Ustawienia. WMFVersion |
| settings.configwersja. URL |Ustawienia. ModulesUrl |
| settings.configwersja. Script |Pierwsza część settings.ConfigurationFunction (przed \\ \\ ) |
| settings.configwersja. Function |Druga część settings.ConfigurationFunction (po \\ \\ ) |
| settings.configuration.module.name | Ustawienia. ModuleSource |
| settings.configwersja. module. Version | Ustawienia. ModuleVersion |
| settings.configurationArguments |Ustawienia. Aœciwoœci |
| settings.configurationData. URL |protectedSettings. DataBlobUri (bez tokenu SAS) |
| Settings. privacy. DataCollection |Ustawienia. Prywatność. DataCollection |
| Settings. advancedOptions. downloadMappings |Ustawienia. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings. Properties |
| protectedSettings.configurationUrlSasToken |Ustawienia. SasToken |
| protectedSettings.configurationDataUrlSasToken |Token sygnatury dostępu współdzielonego z protectedSettings. DataBlobUri |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono niektóre błędy, które można napotkać i jak można je naprawić.

### <a name="invalid-values"></a>Nieprawidłowe wartości

"Privacy. DataCollection to" {0} ".
Jedyne możliwe wartości to "", "Enable" i "Disable" ".
"WmfVersion to" {0} ".
Tylko możliwe wartości to... i "Najnowsza" ".

**Problem**: podana wartość jest niedozwolona.

**Rozwiązanie**: Zmień nieprawidłową wartość na prawidłową wartość.
Aby uzyskać więcej informacji, zobacz tabelę w obszarze [szczegóły](#details).

### <a name="invalid-url"></a>Nieprawidłowy adres URL

"ConfigurationData. URL to" {0} ". To nie jest prawidłowy adres URL "" DataBlobUri to "" {0} . To nie jest prawidłowy adres URL "Configuration. URL to" {0} ". To nie jest prawidłowy adres URL "

**Problem**: podany adres URL jest nieprawidłowy.

**Rozwiązanie**: Sprawdź wszystkie podane adresy URL.
Upewnij się, że wszystkie adresy URL są rozpoznawane jako prawidłowe lokalizacje, do których rozszerzenie może uzyskać dostęp na komputerze zdalnym.

### <a name="invalid-registrationkey-type"></a>Nieprawidłowy typ RegistrationKey

"Nieprawidłowy typ parametru RegistrationKey typu PSCredential."

**Problem**: wartość *RegistrationKey* w protectedSettings.configurationArguments nie może być określona jako typ inny niż PSCredential.

**Rozwiązanie**: zmień wpis protectedSettings.configUrationArguments dla RegistrationKey na typ PSCredential, używając następującego formatu:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Nieprawidłowy typ ConfigurationArgument

"Nieprawidłowy typ configurationArguments {0} "

**Problem**: Właściwość *ConfigurationArguments* nie może zostać rozpoznana jako obiekt **tabeli skrótów** .

**Rozwiązanie**: Ustaw właściwość *ConfigurationArguments* jako **tablicę skrótów**.
Postępuj zgodnie z formatem podanym w powyższych przykładach. Obejrzyj cudzysłowy, przecinki i nawiasy klamrowe.

### <a name="duplicate-configurationarguments"></a>Duplikuj ConfigurationArguments

"Znaleziono zduplikowane argumenty" {0} "w publicznych i chronionych configurationArguments"

**Problem**: *ConfigurationArguments* w ustawieniach publicznych i *ConfigurationArguments* w ustawieniach chronionych mają właściwości o tej samej nazwie.

**Rozwiązanie**: Usuń jedną z zduplikowanych właściwości.

### <a name="missing-properties"></a>Brakujące właściwości

"settings.Configwersja. Function wymaga, aby settings.configwersja. URL lub settings.configwersja. module został określony"

"settings.Configwersja. URL wymaga, aby settings.configwersja. Script został określony"

"settings.Configwersja. Script wymaga, aby określono settings.configwersja. URL"

"settings.Configwersja. URL wymaga określenia settings.configwersja. Function"

"protectedSettings.ConfigurationUrlSasToken wymaga, aby określono settings.configwersja. URL"

"protectedSettings.ConfigurationDataUrlSasToken wymaga, aby określono settings.configurationData. URL"

**Problem**: zdefiniowana właściwość wymaga innej właściwości, która nie istnieje.

**Rozwiązania**:

- Podaj brakującą właściwość.
- Usuń właściwość, która wymaga brakującej właściwości.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [używaniu zestawów skalowania maszyn wirtualnych z rozszerzeniem DSC platformy Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Znajdź więcej szczegółowych informacji [na temat bezpiecznego zarządzania poświadczeniami DSC](dsc-credentials.md).
- Zapoznaj się z [wprowadzeniem do programu obsługi rozszerzeń DSC platformy Azure](dsc-overview.md).
- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do [centrum dokumentacji programu PowerShell](/powershell/scripting/dsc/overview/overview).
