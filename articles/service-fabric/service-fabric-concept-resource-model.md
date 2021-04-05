---
title: Model zasobów aplikacji Service Fabric platformy Azure
description: Ten artykuł zawiera omówienie zarządzania aplikacją Service Fabric platformy Azure przy użyciu Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0019f154f301d2b688d4c16c9adb36ec386adef2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790727"
---
# <a name="service-fabric-application-resource-model"></a>Model zasobów aplikacji Service Fabric

Istnieje wiele opcji wdrażania aplikacji Service Fabric platformy Azure w klastrze Service Fabric. Zalecamy używanie Azure Resource Manager. W przypadku korzystania z Menedżer zasobów można opisać aplikacje i usługi w formacie JSON, a następnie wdrożyć je w tym samym szablonie Menedżer zasobów co klaster. W przeciwieństwie do wdrażania aplikacji i zarządzania nimi przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, jeśli używasz Menedżer zasobów, nie musisz czekać, aż klaster będzie gotowy; Wszystkie rejestracje aplikacji, aprowizacji i wdrożenia mogą wystąpić w jednym kroku. Używanie Menedżer zasobów jest najlepszym sposobem zarządzania cyklem życia aplikacji w klastrze. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania: infrastruktura jako kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Zarządzanie aplikacjami jako zasobami w Menedżer zasobów może pomóc w uzyskaniu ulepszeń w następujących obszarach:

* Dziennik inspekcji: Menedżer zasobów inspekcji każdej operacji i utrzymuje szczegółowy dziennik aktywności. Dziennik aktywności może pomóc w śledzeniu wszelkich zmian wprowadzonych w aplikacjach i w klastrze.
* Kontrola dostępu oparta na rolach: możesz zarządzać dostępem do klastrów i aplikacji wdrożonych w klastrze za pomocą tego samego szablonu Menedżer zasobów.
* Wydajność zarządzania: w przypadku korzystania z Menedżer zasobów można skorzystać z jednej lokalizacji (Azure Portal) służącej do zarządzania klastrem i krytycznych wdrożeń aplikacji.

W tym dokumencie dowiesz się, jak:

> [!div class="checklist"]
>
> * Wdróż zasoby aplikacji przy użyciu Menedżer zasobów.
> * Uaktualnij zasoby aplikacji przy użyciu Menedżer zasobów.
> * Usuwanie zasobów aplikacji.

## <a name="deploy-application-resources"></a>Wdrażanie zasobów aplikacji

Ogólne kroki wykonywane w celu wdrożenia aplikacji i jej usług przy użyciu modelu zasobów aplikacji Menedżer zasobów są następujące:
1. Pakowanie kodu aplikacji.
1. Przekaż pakiet.
1. Odwołuje się do lokalizacji pakietu w szablonie Menedżer zasobów jako zasób aplikacji. 

Aby uzyskać więcej informacji, zobacz [pakowanie aplikacji](service-fabric-package-apps.md#create-an-sfpkg).

Następnie utworzysz szablon Menedżer zasobów, zaktualizujesz plik parametrów przy użyciu szczegółów aplikacji i wdrażasz szablon w klastrze Service Fabric. [Eksploruj przykłady](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby wdrożyć aplikację na podstawie szablonu Menedżer zasobów, musisz mieć konto magazynu. Konto magazynu służy do przygotowywania obrazu aplikacji. 

Możesz użyć istniejącego konta magazynu lub utworzyć nowe konto magazynu na potrzeby przemieszczania aplikacji. Jeśli używasz istniejącego konta magazynu, możesz pominąć ten krok. 

![Tworzenie konta magazynu][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Konfigurowanie konta magazynu

Po utworzeniu konta magazynu utworzysz kontener obiektów blob, w którym można przemieszczać aplikacje. W Azure Portal przejdź do konta usługi Azure Storage, na którym chcesz przechowywać aplikacje. Wybierz pozycję **obiekty blob**  >  **Dodaj kontener**. 

Zasoby w klastrze mogą być zabezpieczone przez ustawienie publicznego poziomu dostępu do **prywatnego**. Dostęp można udzielić na wiele sposobów:

* Autoryzuj dostęp do obiektów blob i kolejek przy użyciu [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Przyznaj dostęp do danych obiektów blob platformy Azure i kolejek przy użyciu funkcji [RBAC platformy Azure w Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegowanie dostępu przy użyciu [sygnatury dostępu współdzielonego](/rest/api/storageservices/delegate-access-with-shared-access-signature).

Przykład na poniższym zrzucie ekranu używa anonimowego dostępu do odczytu dla obiektów BLOB.

![Utwórz obiekt BLOB][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Przygotowywanie aplikacji na koncie magazynu

Przed wdrożeniem aplikacji należy przygotować aplikację w usłudze BLOB Storage. W tym samouczku utworzysz pakiet aplikacji ręcznie. Należy pamiętać, że ten krok można zautomatyzować. Aby uzyskać więcej informacji, zobacz [pakowanie aplikacji](service-fabric-package-apps.md#create-an-sfpkg). 

W tym samouczku używamy [przykładowej aplikacji do głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt **głosowania** , a następnie wybierz polecenie **pakiet**.

   ![Aplikacja pakietu][PackageApplication]  
1. Przejdź do katalogu *.\Service-Fabric-dotnet-quickstart\Voting\pkg\Debug* . Zawartość zip należy umieścić w pliku o nazwie *Voting.zip*. Plik *ApplicationManifest.xml* powinien znajdować się w katalogu głównym pliku zip.

   ![Aplikacja zip][ZipApplication]  
1. Zmień nazwę pliku, aby zmienić rozszerzenie z. zip na *. sfpkg*.

1. W Azure Portal w kontenerze **aplikacje** dla konta magazynu wybierz pozycję **Przekaż**, a następnie Przekaż polecenie **głosu. sfpkg**. 

   ![Przekaż pakiet aplikacji][UploadAppPkg]

Teraz aplikacja jest teraz przygotowana i można utworzyć szablon Menedżer zasobów, aby wdrożyć aplikację.

### <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

Przykładowa aplikacja zawiera [szablony Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) , których można użyć do wdrożenia aplikacji. Nazwy plików szablonów są *UserApp.jswłączone* i *UserApp.Parameters.js*.

> [!NOTE]
> *UserApp.Parameters.jsw* pliku należy zaktualizować nazwą klastra.
>
>


| Parametr              | Opis                                 | Przykład                                                      | Komentarze                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Nazwa klastra, na którym jest wdrażana | SF-cluster123                                                |                                                              |
| aplikacja            | Nazwa aplikacji                 | Voting (Głosowanie)                                                       |
| applicationTypeName    | Nazwa typu aplikacji           | VotingType                                                   | Muszą być zgodne ApplicationManifest.xml                 |
| applicationTypeVersion | Wersja typu aplikacji         | 1.0.0                                                        | Muszą być zgodne ApplicationManifest.xml                 |
| serviceName            | Nazwa usługi         | Głosowanie ~ VotingWeb                                             | Musi być w formacie ApplicationName ~ Service            |
| Typ ServiceTypeName        | Nazwa typu usługi                | VotingWeb                                                    | Muszą być zgodne ServiceManifest.xml                 |
| appPackageUrl          | Adres URL magazynu obiektów BLOB aplikacji     | https: \/ /servicefabricapps.blob.Core.Windows.NET/Apps/Voting.sfpkg | Adres URL pakietu aplikacji w usłudze BLOB Storage (Procedura ustawiania adresu URL jest opisana w dalszej części artykułu) |

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

Uruchom polecenie cmdlet **New-AzResourceGroupDeployment** , aby wdrożyć aplikację w grupie zasobów zawierającej klaster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Uaktualnij aplikację Service Fabric przy użyciu Menedżer zasobów


> [!IMPORTANT]
> Wszystkie usługi wdrażane za pomocą definicji JSON ARM muszą zostać usunięte z sekcji DefaultServices odpowiedniego pliku ApplicationManifest.xml.


Możesz uaktualnić aplikację, która została już wdrożona w klastrze Service Fabric z jednego z następujących powodów:

* Nowa usługa zostanie dodana do aplikacji. Przed dodaniem usługi do aplikacji należy dodać definicję usługi do plików *service-manifest.xml* i *application-manifest.xml* . Aby odzwierciedlić nową wersję aplikacji, należy również zmienić wersję typu aplikacji z 1.0.0 na 1.0.1 w [UserApp.Parameters.jsna](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

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

* Nowa wersja istniejącej usługi zostanie dodana do aplikacji. Przykładami mogą być zmiany kodu aplikacji i aktualizacje typu aplikacji. W przypadku tego uaktualnienia należy zaktualizować UserApp.Parameters.jsw następujący sposób:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Usuwanie zasobów aplikacji

Aby usunąć aplikację, która została wdrożona przy użyciu modelu zasobów aplikacji w Menedżer zasobów:

1. Użyj polecenia cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) , aby uzyskać identyfikator zasobu dla aplikacji:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Użyj polecenia cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource) , aby usunąć zasoby aplikacji:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Następne kroki

Pobierz informacje o modelu zasobów aplikacji:

* [Modelowanie aplikacji w Service Fabric](service-fabric-application-model.md)
* [Service Fabric manifesty aplikacji i usług](service-fabric-application-and-service-manifests.md)
* [Najlepsze rozwiązania: infrastruktura jako kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Zarządzanie aplikacjami i usługami jako zasobami platformy Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
