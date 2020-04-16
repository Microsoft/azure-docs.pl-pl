---
title: Samouczek — używanie usługi Azure Key Vault z maszyną wirtualną systemu Windows w języku Python | Dokumenty firmy Microsoft
description: W tym samouczku skonfigurujesz ASP.NET podstawową aplikację do odczytu klucza tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 85dc751909f61eff40ad57868bafa7ea2019ed89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422872"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Samouczek: Używanie usługi Azure Key Vault z maszyną wirtualną systemu Windows w języku Python

Usługa Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych, które są potrzebne do uzyskania dostępu do aplikacji, usług i zasobów IT.

W tym samouczku dowiesz się, jak uzyskać aplikację konsoli do odczytu informacji z usługi Azure Key Vault. Aby to zrobić, należy użyć tożsamości zarządzanych dla zasobów platformy Azure. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włącz tożsamość zarządzaną.
> * Przypisywanie uprawnień do tożsamości maszyny Wirtualnej.

Przed rozpoczęciem przeczytaj [podstawowe pojęcia programu Key Vault](basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dla systemów Windows, Mac i Linux:
  * [Git](https://git-scm.com/downloads)
  * Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musi być zainstalowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informacje o tożsamości usługi zarządzanej

Usługa Azure Key Vault bezpiecznie przechowuje poświadczenia, więc nie są one wyświetlane w kodzie. Aby jednak pobrać klucze, musisz uwierzytelnić się w usłudze Azure Key Vault. W tym celu potrzebujesz poświadczenia. To klasyczny przykład dylematu dotyczącego uruchamiania. Tożsamość usługi zarządzanej rozwiązuje ten problem poprzez zapewnienie _tożsamości uruchamiania_, która upraszcza ten proces.

Po włączeniu msi dla usługi platformy Azure, takich jak maszyny wirtualne platformy Azure, usługa azure app service lub usługi Azure Functions, platforma Azure tworzy [jednostkę usługi.](basic-concepts.md) MSI robi to dla wystąpienia usługi w usłudze Azure Active Directory (Azure AD) i wstrzykuje poświadczenia jednostki usługi do tego wystąpienia. 

![Tożsamość usługi zarządzanej](../media/MSI.png)

Następnie, aby uzyskać token dostępu, kod wywołuje lokalną usługę metadanych, która jest dostępna w zasobie platformy Azure. Aby uwierzytelnić się w usłudze Azure Key Vault, kod używa tokenu dostępu, który pobiera z lokalnego punktu końcowego MSI. 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 

Wybierz nazwę grupy zasobów i wypełnij symbol zastępczy. Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji Zachodnie stany USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Nowo utworzonej grupy zasobów w tym samouczku.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby utworzyć magazyn kluczy w grupie zasobów utworzonej w poprzednim kroku, podaj następujące informacje:

* Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)
* Nazwa grupy zasobów
* Lokalizacja: **Zachodnie stany USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
W tym momencie twoje konto platformy Azure jest jedynym, który jest autoryzowany do wykonywania operacji na tym nowym magazynie kluczy.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Dodajemy wpis tajny, aby zilustrować, jak to działa. Klucz tajny może być ciągiem połączenia SQL lub innymi informacjami, które należy zachować zarówno bezpieczne, jak i dostępne dla aplikacji.

Aby utworzyć klucz tajny w magazynie kluczy o nazwie **AppSecret**, wprowadź następujące polecenie:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ten wpis tajny zawiera wartość **MySecret**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Maszynę wirtualną można utworzyć przy użyciu jednej z następujących metod:

* [Narzędzie interfejsu wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Witryna Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
W tym kroku należy utworzyć tożsamość przypisaną do systemu dla maszyny wirtualnej, uruchamiając następujące polecenie w wierszu polecenia platformy Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Zanotuj tożsamość przypisaną do systemu, która jest wyświetlana w poniższym kodzie. Dane wyjściowe poprzedniego polecenia to: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny Wirtualnej
Teraz możesz przypisać wcześniej utworzone uprawnienia tożsamości do magazynu kluczy, uruchamiając następujące polecenie:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami w [connect i zaloguj się na maszynie wirtualnej platformy Azure z systemem Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Tworzenie i uruchamianie przykładowej aplikacji języka Python

W następnej sekcji znajduje się przykładowy plik o nazwie *Sample.py*. Używa biblioteki [żądań](https://2.python-requests.org/en/master/) do wykonywania wywołań HTTP GET.

## <a name="edit-samplepy"></a>Edytowanie pliku Sample.py

Po utworzeniu *Sample.py*otwórz plik, a następnie skopiuj kod w tej sekcji. 

Kod przedstawia proces dwuetapowy:
1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej.  
  W ten sposób pobiera również token z usługi Azure AD.
1. Przekaż token do magazynu kluczy, a następnie pobierz swój klucz tajny. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Wartość tajną można wyświetlić, uruchamiając następujący kod: 

```console
python Sample.py
```

Powyższy kod pokazuje, jak wykonać operacje w usłudze Azure Key Vault na maszynie wirtualnej z systemem Windows. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń maszynę wirtualną i magazyn kluczy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
