---
title: Samouczek — używanie Azure Key Vault z maszyną wirtualną w języku Python | Microsoft Docs
description: W tym samouczku skonfigurujesz aplikację ASP.NET Core w celu odczytu wpisu tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101655"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Samouczek: używanie Azure Key Vault z maszyną wirtualną w języku Python

Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych potrzebne do uzyskiwania dostępu do aplikacji, usług i zasobów IT.

W ramach tego samouczka nauczysz się, jak uzyskać aplikację konsolową do odczytu informacji z Azure Key Vault. W tym celu należy użyć zarządzanych tożsamości dla zasobów platformy Azure. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz magazyn kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włącz zarządzaną tożsamość.
> * Przypisz uprawnienia do tożsamości maszyny wirtualnej.

Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dla systemów Windows, Mac i Linux:
  * [Narzędzia](https://git-scm.com/downloads)
  * Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musisz mieć zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Dodajemy wpis tajny, aby zilustrować, jak to działa. Wpis tajny może być parametrami połączenia SQL lub innymi informacjami, które są potrzebne do zapewnienia bezpiecznego i dostępnego dla aplikacji.

Aby utworzyć wpis tajny w magazynie kluczy o nazwie **AppSecret**, wprowadź następujące polecenie:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ten wpis tajny zawiera wartość **MySecret**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Maszynę wirtualną można utworzyć za pomocą jednej z następujących metod:

* [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md)
* [Program PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
W tym kroku utworzysz tożsamość przypisaną do systemu dla maszyny wirtualnej, uruchamiając następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Zanotuj tożsamość przypisaną przez system, która jest wyświetlana w poniższym kodzie. Dane wyjściowe poprzedniego polecenia byłyby następujące: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny wirtualnej
Teraz można przypisać wcześniej utworzone uprawnienia tożsamości do magazynu kluczy, uruchamiając następujące polecenie:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami podanymi w temacie [łączenie i logowanie do maszyny wirtualnej platformy Azure z systemem Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Tworzenie i uruchamianie przykładowej aplikacji w języku Python

W następnej sekcji znajduje się przykładowy plik o nazwie *Sample.py*. Używa biblioteki [żądań](https://2.python-requests.org/en/master/) do wykonywania wywołań http.

## <a name="edit-samplepy"></a>Edytowanie pliku Sample.py

Po utworzeniu *Sample.py*Otwórz plik, a następnie skopiuj kod w tej sekcji. 

Kod przedstawia proces dwuetapowy:
1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej.  
  Spowoduje to również pobranie tokenu z usługi Azure AD.
1. Przekaż token do magazynu kluczy, a następnie Pobierz wpis tajny. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Wartość klucza tajnego można wyświetlić, uruchamiając następujący kod: 

```console
python Sample.py
```

Powyższy kod pokazuje, jak wykonać operacje w usłudze Azure Key Vault na maszynie wirtualnej z systemem Windows. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń maszynę wirtualną i Magazyn kluczy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
