---
title: Tworzenie zasobu czytnika immersyjnego
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak utworzyć nowy zasób czytnika immersyjny z niestandardową domeną poddomeny, a następnie skonfigurować usługę Azure AD w dzierżawie platformy Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: d7a8a598ab2d0c873cf07510cf40a08c22c0f1d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608601"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Utwórz zasób czytnika immersyjny i Skonfiguruj uwierzytelnianie Azure Active Directory

W tym artykule udostępniamy skrypt, który utworzy zasób czytnika immersyjny i skonfiguruje uwierzytelnianie Azure Active Directory (Azure AD). Za każdym razem, gdy tworzony jest zasób czytnika, niezależnie od tego, czy jest to skrypt, czy w portalu, należy również skonfigurować uprawnienia usługi Azure AD. Ten skrypt pomoże Ci tego.

Skrypt jest przeznaczony do tworzenia i konfigurowania wszystkich niezbędnych czytników immersyjny i zasobów usługi Azure AD w jednym kroku. Można jednak po prostu skonfigurować uwierzytelnianie usługi Azure AD dla istniejącego zasobu czytnika szczegółowego, jeśli na przykład nastąpi już utworzenie jednego w Azure Portal.

W przypadku niektórych klientów może być konieczne utworzenie wielu zasobów czytnika, na potrzeby tworzenia i produkcji, lub też dla wielu różnych regionów, w których jest wdrażana usługa. W takich przypadkach można wrócić i wielokrotnie używać skryptu do tworzenia różnych zasobów czytnika szczegółowego i uzyskiwania ich konfiguracji przy użyciu uprawnień usługi Azure AD.

Skrypt został zaprojektowany jako elastyczny. Najpierw szuka istniejącego czytnika immersyjny i zasobów usługi Azure AD w ramach subskrypcji i utworzysz je tylko w razie potrzeby, jeśli jeszcze nie istnieją. Jeśli tworzysz zasób czytnika immersyjny po raz pierwszy, skrypt wykona wszystko, czego potrzebujesz. Jeśli chcesz korzystać z niej tylko w celu skonfigurowania usługi Azure AD dla istniejącego zasobu czytników immersyjny, który został utworzony w portalu, spowoduje to również. Może również służyć do tworzenia i konfigurowania wielu zasobów czytnika.

## <a name="set-up-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

1. Zacznij od otworzenia [Azure Cloud Shell](../../cloud-shell/overview.md). Upewnij się, że Cloud Shell jest ustawiona na program PowerShell w lewym górnym rogu listy rozwijanej lub wpisując `pwsh` .

1. Skopiuj i wklej poniższy fragment kodu w powłoce.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Uruchom funkcję `Create-ImmersiveReaderResource` , podając odpowiednio symbole zastępcze "<PARAMETER_VALUES>" z własnymi wartościami.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource -SubscriptionName '<SUBSCRIPTION_NAME>' -ResourceName '<RESOURCE_NAME>' -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' -ResourceSKU '<RESOURCE_SKU>' -ResourceLocation '<RESOURCE_LOCATION>' -ResourceGroupName '<RESOURCE_GROUP_NAME>' -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>' -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    Pełne polecenie będzie wyglądać podobnie do poniższego. W tym miejscu umieszczamy każdy parametr we własnym wierszu dla jasności, więc można zobaczyć całe polecenie. Nie należy kopiować ani używać tego polecenia jako-is. Skopiuj i użyj powyższego polecenia z własnymi wartościami. Ten przykład ma wartości fikcyjne dla "<PARAMETER_VALUES>" powyżej. Twoje dane będą się różnić, ponieważ będziesz mieć własne nazwy dla tych wartości.

    ```
    Create-ImmersiveReaderResource
        -SubscriptionName 'MyOrganizationSubscriptionName'
        -ResourceName 'MyOrganizationImmersiveReader'
        -ResourceSubdomain 'MyOrganizationImmersiveReader'
        -ResourceSKU 'S0'
        -ResourceLocation 'westus2'
        -ResourceGroupName 'MyResourceGroupName'
        -ResourceGroupLocation 'westus2'
        -AADAppDisplayName 'MyOrganizationImmersiveReaderAADApp'
        -AADAppIdentifierUri 'https://MyOrganizationImmersiveReaderAADApp'
        -AADAppClientSecret 'SomeStrongPassword'
        -AADAppClientSecretExpiration '2021-12-31'
    ```

    | Parametr | Komentarze |
    | --- | --- |
    | SubscriptionName |Nazwa subskrypcji platformy Azure, która ma być używana dla zasobu czytnika danych. Aby można było utworzyć zasób, trzeba mieć subskrypcję. |
    | ResourceName |  Musi być alfanumeryczne i może zawierać znak "-", tak długo, jak "-" nie jest pierwszy ani ostatni. Długość nie może przekraczać 63 znaków.|
    | ResourceSubdomain |Niestandardowa poddomena jest wymagana dla zasobu czytnika danych. Poddomena jest używana przez zestaw SDK podczas wywoływania usługi czytnika immersyjny w celu uruchomienia czytnika. Poddomena musi być unikatowa w skali globalnej. Poddomena musi być alfanumeryczna i może zawierać znaki "-", o ile nie jest to pierwszy ani ostatni znak. Długość nie może przekraczać 63 znaków. Ten parametr jest opcjonalny, jeśli zasób już istnieje. |
    | ResourceSKU |Opcje: `S0` (warstwa standardowa) lub `S1` (organizacje edukacyjne/niedochodowe). Odwiedź [stronę cennika Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) , aby dowiedzieć się więcej na temat każdej dostępnej jednostki SKU. Ten parametr jest opcjonalny, jeśli zasób już istnieje. |
    | ResourceLocation |Opcje:,,,,,,,,,, `eastus` `eastus2` `southcentralus` `westus` `westus2` `australiaeast` `southeastasia` `centralindia` `japaneast` `northeurope` `uksouth` `westeurope` . Ten parametr jest opcjonalny, jeśli zasób już istnieje. |
    | ResourceGroupName |Zasoby są tworzone w grupach zasobów w ramach subskrypcji. Podaj nazwę istniejącej grupy zasobów. Jeśli grupa zasobów jeszcze nie istnieje, zostanie utworzona nowa nazwa o tej nazwie. |
    | ResourceGroupLocation |Jeśli grupa zasobów nie istnieje, należy podać lokalizację, w której ma zostać utworzona grupa. Aby znaleźć listę lokalizacji, uruchom polecenie `az account list-locations` . Użyj właściwości *name* (bez spacji) zwracanego wyniku. Ten parametr jest opcjonalny, jeśli grupa zasobów już istnieje. |
    | AADAppDisplayName |Nazwa wyświetlana aplikacji Azure Active Directory. Jeśli istniejąca aplikacja usługi Azure AD nie zostanie znaleziona, zostanie utworzona nowa nazwa o tej nazwie. Ten parametr jest opcjonalny, jeśli aplikacja usługi Azure AD już istnieje. |
    | AADAppIdentifierUri |Identyfikator URI aplikacji usługi Azure AD. Jeśli istniejąca aplikacja usługi Azure AD nie zostanie znaleziona, zostanie utworzona nowa z tym identyfikatorem URI. Na przykład `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Utworzone hasło, które będzie używane później do uwierzytelniania podczas uzyskiwania tokenu w celu uruchomienia czytnika immersyjny. Hasło musi składać się z co najmniej 16 znaków, zawierać co najmniej 1 znak specjalny i zawierać co najmniej 1 znak liczbowy. Aby zarządzać wpisami tajnymi klienta aplikacji usługi Azure AD po utworzeniu tego zasobu https://portal.azure.com , odwiedź stronę główną > Azure Active Directory-> > Registration- `[AADAppDisplayName]` > Certificates and Secret-> (jak pokazano na poniższym zrzucie ekranu "Zarządzanie kluczami tajnymi aplikacji usługi Azure AD"). |
    | AADAppClientSecretExpiration |Data lub Data i godzina `[AADAppClientSecret]` wygaśnięcia (np. "2020-12-31T11:59:59 + 00:00" lub "2020-12-31"). |

    Zarządzanie wpisami tajnymi aplikacji usługi Azure AD

    ![Blok certyfikatów i wpisów tajnych w witrynie Azure Portal](./media/client-secrets-blade.png)

1. Skopiuj dane wyjściowe JSON do pliku tekstowego do późniejszego użycia. Dane wyjściowe powinny wyglądać podobnie do poniższych.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Następne kroki

* Wyświetl [Node.js przewodnika Szybki Start](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK "immersyjny" przy użyciu Node.js
* Obejrzyj [Samouczek systemu Android](./tutorial-android.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK "immersyjny" przy użyciu języka Java lub Kotlin dla systemu Android
* Wyświetl [Samouczek systemu iOS](./tutorial-ios.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK "immersyjny" przy użyciu SWIFT dla systemu iOS
* Obejrzyj [samouczek języka Python](./tutorial-python.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK dla czytnika immersyjny przy użyciu języka Python
* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md)