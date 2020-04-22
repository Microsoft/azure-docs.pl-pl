---
title: Model zasobów aplikacji sieci szkieletowej usługi Azure
description: Ten artykuł zawiera omówienie zarządzania aplikacją usługi Azure Service Fabric przy użyciu usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682642"
---
# <a name="service-fabric-application-resource-model"></a>Model zasobów aplikacji sieci szkieletowej usług

Masz wiele opcji wdrażania aplikacji sieci szkieletowej usługi Azure w klastrze sieci szkieletowej usług. Zalecamy korzystanie z usługi Azure Resource Manager. Jeśli używasz Menedżera zasobów, można opisać aplikacje i usługi w JSON, a następnie wdrożyć je w tym samym szablonie Menedżera zasobów co klaster. W przeciwieństwie do korzystania z programu PowerShell lub interfejsu wiersza polecenia platformy Azure do wdrażania aplikacji i zarządzania nimi, jeśli używasz Menedżera zasobów, nie trzeba czekać na klaster, aby być gotowym; rejestracja aplikacji, inicjowanie obsługi administracyjnej i wdrażanie może się zdarzyć w jednym kroku. Korzystanie z Menedżera zasobów jest najlepszym sposobem zarządzania cyklem życia aplikacji w klastrze. Aby uzyskać więcej informacji, zobacz [Najważniejsze wskazówki: Infrastruktura jako kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Zarządzanie aplikacjami jako zasobami w Menedżerze zasobów może pomóc w uzyskaniu ulepszeń w następujących obszarach:

* Dziennik inspekcji: Menedżer zasobów przeprowadza inspekcje każdej operacji i przechowuje szczegółowy dziennik działań. Dziennik aktywności może pomóc w śledzeniu wszelkich zmian wprowadzonych w aplikacjach i klastrze.
* Kontrola dostępu oparta na rolach: można zarządzać dostępem do klastrów i aplikacji wdrożonych w klastrze przy użyciu tego samego szablonu Menedżera zasobów.
* Wydajność zarządzania: korzystanie z Menedżera zasobów zapewnia jedną lokalizację (witrynę Azure portal) do zarządzania klastrem i wdrożeniami aplikacji krytycznych.

W tym dokumencie dowiesz się, jak:

> [!div class="checklist"]
>
> * Wdrażanie zasobów aplikacji przy użyciu Menedżera zasobów.
> * Uaktualnianie zasobów aplikacji przy użyciu Menedżera zasobów.
> * Usuwanie zasobów aplikacji.

## <a name="deploy-application-resources"></a>Wdrażanie zasobów aplikacji

Kroki wysokiego poziomu, które można wykonać w celu wdrożenia aplikacji i jej usług przy użyciu modelu zasobów aplikacji Menedżera zasobów są:
1. Pakiet kodu aplikacji.
1. Prześlij pakiet.
1. Odwoływać się do lokalizacji pakietu w szablonie Menedżera zasobów jako zasobu aplikacji. 

Aby uzyskać więcej informacji, zobacz [Pakiet aplikacji](service-fabric-package-apps.md#create-an-sfpkg).

Następnie należy utworzyć szablon Menedżera zasobów, zaktualizować plik parametrów ze szczegółami aplikacji i wdrożyć szablon w klastrze sieci szkieletowej usług. [Zapoznaj się z przykładami](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby wdrożyć aplikację z szablonu Menedżera zasobów, musisz mieć konto magazynu. Konto magazynu służy do wystawiania obrazu aplikacji. 

Można ponownie użyć istniejącego konta magazynu lub utworzyć nowe konto magazynu dla przemieszczania aplikacji. Jeśli używasz istniejącego konta magazynu, możesz pominąć ten krok. 

![Tworzenie konta magazynu][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Konfigurowanie konta magazynu

Po utworzeniu konta magazynu należy utworzyć kontener obiektów blob, w którym można przemieszczać aplikacje. W witrynie Azure portal przejdź do konta usługi Azure Storage, na którym chcesz przechowywać aplikacje. Wybierz **pozycję Obiekty blob** > **Dodaj kontener**. 

Zasoby w klastrze można zabezpieczyć, ustawiając publiczny poziom dostępu na **prywatny**. Dostęp można udzielić na wiele sposobów:

* Autoryzuj dostęp do obiektów blob i kolejek przy użyciu [usługi Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Udziel dostępu do danych obiektów blob i kolejek platformy Azure przy użyciu [funkcji RBAC w witrynie Azure portal](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegowanie dostępu przy użyciu [sygnatury dostępu współdzielonego](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature).

Przykład na poniższym zrzucie ekranu używa anonimowego dostępu do odczytu dla obiektów blob.

![Tworzenie obiektu blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Etap aplikacji na koncie magazynu

Przed wdrożeniem aplikacji należy zorganizować aplikację w magazynie obiektów blob. W tym samouczku tworzymy pakiet aplikacji ręcznie. Należy pamiętać, że ten krok można zautomatyzować. Aby uzyskać więcej informacji, zobacz [Pakowanie aplikacji](service-fabric-package-apps.md#create-an-sfpkg). 

W tym samouczku używamy [przykładowej aplikacji głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt **głosowania,** a następnie wybierz pozycję **Pakiet**.

   ![Aplikacja pakietu][PackageApplication]  
1. Przejdź do katalogu *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug.* Skompresuj zawartość do pliku o nazwie *Voting.zip*. *Plik ApplicationManifest.xml* powinien znajdować się w katalogu głównym w pliku zip.

   ![Aplikacja zip][ZipApplication]  
1. Zmień nazwę pliku, aby zmienić rozszerzenie z .zip na *.sfpkg*.

1. W witrynie Azure portal w kontenerze **aplikacji** dla konta magazynu wybierz pozycję **Przekaż**, a następnie przekaż **plik Voting.sfpkg**. 

   ![Przekaż pakiet aplikacji][UploadAppPkg]

Teraz aplikacja jest teraz przemieszczana i można utworzyć szablon Menedżera zasobów, aby wdrożyć aplikację.

### <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

Przykładowa aplikacja zawiera [szablony usługi Azure Resource Manager,](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) których można użyć do wdrożenia aplikacji. Nazwy plików szablonów to *UserApp.json* i *UserApp.Parameters.json*.

> [!NOTE]
> Plik *UserApp.Parameters.json* musi zostać zaktualizowany o nazwę klastra.
>
>

| Parametr              | Opis                                 | Przykład                                                      | Komentarze                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Nazwa klastra, w który wdrażasz | sf-klaster123                                                |                                                              |
| aplikacja            | Nazwa aplikacji                 | Voting (Głosowanie)                                                       |
| nazwa typu aplikacji    | Nazwa typu aplikacji           | Rodzaj głosowania                                                   | Musi być zgodny z applicationmanifest.xml                 |
| aplikacjaTypeVersion | Wersja typu aplikacji         | 1.0.0                                                        | Musi być zgodny z applicationmanifest.xml                 |
| Servicename            | Nazwa usługi         | Głosowanie ~ VotingWeb                                             | Musi być w formacie ApplicationName~ServiceType            |
| nazwa typu usługi        | Nazwa typu usługi                | VotingWeb                                                    | Musi być zgodny z servicemanifest.xml                 |
| aplikacjaPackageUrl          | Adres URL magazynu obiektów blob aplikacji     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Adres URL pakietu aplikacji w magazynie obiektów blob (procedura ustawiania adresu URL jest opisana w dalszej części artykułu) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Wdrażanie aplikacji

Uruchom polecenie cmdlet **New-AzResourceGroupDeployment,** aby wdrożyć aplikację w grupie zasobów zawierającej klaster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Uaktualnianie aplikacji sieci szkieletowej usług przy użyciu Menedżera zasobów

Możesz uaktualnić aplikację, która jest już wdrożona do klastra sieci szkieletowej usług z jednego z następujących powodów:

* Nowa usługa jest dodawana do aplikacji. Definicja usługi musi zostać dodana do plików *service-manifest.xml* i *application-manifest.xml,* gdy usługa jest dodawana do aplikacji. Aby odzwierciedlić nową wersję aplikacji, należy również zmienić wersję typu aplikacji z 1.0.0 na 1.0.1 w [Aplikacji UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Nowa wersja istniejącej usługi jest dodawany do aplikacji. Przykłady obejmują zmiany kodu aplikacji i aktualizacje wersji i nazwy typu aplikacji. W przypadku tego uaktualnienia zaktualizuj plik UserApp.Parameters.json w ten sposób:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Usuwanie zasobów aplikacji

Aby usunąć aplikację wdrożoną przy użyciu modelu zasobów aplikacji w Menedżerze zasobów:

1. Użyj polecenia cmdlet [Get-AzResource,](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) aby uzyskać identyfikator zasobu dla aplikacji:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Użyj polecenia cmdlet [Usuń-AzResource,](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) aby usunąć zasoby aplikacji:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Następne kroki

Uzyskaj informacje o modelu zasobów aplikacji:

* [Modelowanie aplikacji w sieci szkieletowej usług](service-fabric-application-model.md)
* [Manifesty aplikacji i usług sieci szkieletowej usług](service-fabric-application-and-service-manifests.md)
* [Najważniejsze wskazówki: Infrastruktura jako kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Zarządzanie aplikacjami i usługami jako zasobami platformy Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
