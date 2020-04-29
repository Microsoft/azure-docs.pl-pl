---
title: 'Samouczek: Używanie maszyny wirtualnej z systemem Linux i aplikacji w języku Python do przechowywania wpisów tajnych w usłudze Azure Key Vault | Microsoft Docs'
description: Z tego samouczka dowiesz się, jak skonfigurować aplikację w języku Python, aby odczytać wpis tajny z usługi Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: df089f0338a177c08f4d9e88d55b501fd12f88f2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81423319"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Samouczek: korzystanie z maszyny wirtualnej z systemem Linux i aplikacji w języku Python do przechowywania wpisów tajnych w programie Azure Key Vault

Usługa Azure Key Vault umożliwia ochronę wpisów tajnych, takich jak klucze interfejsu API i parametry połączenia bazy danych potrzebne do uzyskania dostępu do aplikacji, usługi oraz zasoby informatyczne.

W tym samouczku skonfigurujesz aplikację internetową platformy Azure pod kątem odczytywania informacji z usługi Azure Key Vault za pomocą tożsamości zarządzanych dla zasobów platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy
> * Przechowywanie wpisu tajnego w magazynie kluczy
> * Tworzenie maszyny wirtualnej z systemem Linux
> * Włączanie [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej
> * Przyznawanie wymaganych uprawnień w celu umożliwienia aplikacji konsolowej odczytu danych z magazynu kluczy
> * Pobieranie wpisu tajnego z magazynu kluczy

Zanim przejdziesz dalej, zapoznaj się z [podstawowymi pojęciami dotyczącymi usługi Key Vault](../general/basic-concepts.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Usługi [git](https://git-scm.com/downloads).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lub usługa Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Zapoznanie się z tożsamością usługi zarządzanej

Azure Key Vault mogą bezpiecznie przechowywać poświadczenia, aby nie znajdowały się w kodzie. Aby je pobrać, należy uwierzytelnić się w usłudze Azure Key Vault. Jednak aby uwierzytelniać się w usłudze Key Vault, potrzebne są poświadczenia. Jest to klasyczny problem dotyczący uruchamiania. Dzięki platformie Azure i usłudze Azure Active Directory (Azure AD) tożsamość usługi zarządzanej udostępnia „tożsamość uruchamiania”, która ułatwia rozpoczęcie wykonywania czynności.

Po włączeniu tożsamości usługi zarządzanej dla usługi platformy Azure, takiej jak Virtual Machines, App Service lub Functions, platforma Azure tworzy jednostkę usługi dla wystąpienia usługi w usłudze Azure AD. Wprowadza ona poświadczenia dla jednostki usługi do wystąpienia usługi.

![Tożsamość usługi zarządzanej](../media/MSI.png)

Następnie Twój kod wywołuje lokalną usługę metadanych dostępną w zasobie platformy Azure, aby uzyskać token dostępu. W celu uwierzytelnienia w usłudze Azure Key Vault kod używa tokenu dostępu, który otrzymuje z lokalnego punktu końcowego tożsamości usługi zarządzanej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia `az group create` w lokalizacji Zachodnie stany USA, korzystając z następującego kodu. Zastąp ciąg `YourResourceGroupName` wybraną nazwą.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Możesz używać tej grupy zasobów w całym samouczku.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz magazyn kluczy w grupie zasobów utworzonej w poprzednim kroku. Podaj następujące informacje:

* Nazwa magazynu kluczy: nazwa musi być ciągiem zawierającym 3-24 znaków i może zawierać tylko 0-9, a-z, A-Z i łączniki (-).
* Nazwa grupy zasobów.
* Lokalizacja: **zachodnie stany USA**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Dodajemy wpis tajny, aby zilustrować, jak to działa. Może zapisać parametry połączenia SQL lub dowolne inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie być dostępne dla aplikacji.

Wpisz następujące polecenia, aby utworzyć wpis tajny w magazynie kluczy o nazwie *AppSecret*. Ten wpis tajny będzie przechowywał wartość **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz maszynę wirtualną za pomocą polecenia `az vm create`.

W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie **myVM** i dodane konto użytkownika o nazwie **azureuser**. Parametr `--generate-ssh-keys` automatycznie generuje klucz SSH i umieszcza go w domyślnej lokalizacji klucza (**~/.ssh**). Aby zamiast tego utworzyć określony zestaw kluczy, skorzystaj z opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że tworzenie maszyny wirtualnej zakończyło się pomyślnie:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość elementu `publicIpAddress` z danych wyjściowych maszyny wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w późniejszych krokach.

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej

Utwórz tożsamość przypisaną przez system do maszyny wirtualnej, uruchamiając następujące polecenie:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Dane wyjściowe polecenia wyglądają tak jak poniżej.

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Zanotuj wartość parametru `systemAssignedIdentity`. Możesz użyć go w następnym kroku.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Przyznawanie tożsamości maszyny wirtualnej uprawnienia do usługi Key Vault

Teraz możesz przyznać utworzonej tożsamości uprawnienia do usługi Key Vault. Uruchom następujące polecenie:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logowanie się do maszyny wirtualnej

Zaloguj się do maszyny wirtualnej za pomocą terminalu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instalowanie biblioteki języka Python na maszynie wirtualnej

Pobierz i zainstaluj bibliotekę [requests](https://pypi.org/project/requests/2.7.0/) języka Python służącą do wykonywania wywołań HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Tworzenie, edytowania i uruchamianie przykładowej aplikacji w języku Python

Utwórz plik w języku Python o nazwie **Sample.py**.

Otwórz plik Sample.py i dokonaj jego edycji, aby zawierał następujący kod:

```python
# importing the requests library
import requests

# Step 1: Fetch an access token from an MSI-enabled Azure resource      
# Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
# This request gets an access token from Azure Active Directory by using the local MSI endpoint
data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

Powyższy kod wykonuje dwuetapowy proces:

   1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej. Punkt końcowy pobiera następnie token z usługi Azure Active Directory.
   1. Przekazywanie tokenu do magazynu kluczy i pobieranie wpisu tajnego.

Uruchom następujące polecenie. Powinna zostać wyświetlona wartość wpisu tajnego.

```console
python Sample.py
```

W tym samouczku przedstawiono sposób użycia usługi Azure Key Vault za pomocą aplikacji w języku Python uruchomionej na maszynie wirtualnej z systemem Linux.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie powiązane zasoby nie będą już potrzebne, można je usunąć. W tym celu wybierz grupę zasobów maszyny wirtualnej i kliknij pozycję **Usuń**.

Usuń magazyn kluczy przy użyciu polecenia `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
