---
title: 'Szybki Start: Ustawianie & wyświetlania certyfikatów Azure Key Vault — interfejs wiersza polecenia platformy Azure'
description: Przewodnik Szybki Start przedstawiający sposób ustawiania i pobierania certyfikatu z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 55a25f4f40668172aa8a9c80dd08d9fafcef78af
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516480"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Szybki Start: Ustawianie i pobieranie certyfikatu z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start utworzysz Magazyn kluczy w Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault można zapoznać się z [omówieniem](../general/overview.md). Interfejs wiersza polecenia platformy Azure służy do tworzenia zasobów platformy Azure i zarządzanie nimi za pomocą poleceń lub skryptów. Po zakończeniu tej operacji certyfikat zostanie zapisany.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten przewodnik Szybki Start wymaga wersji 2.0.4 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *ContosoResourceGroup* w lokalizacji *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz usługę Key Vault w grupie zasobów utworzonej w poprzednim kroku. Konieczne będzie podanie pewnych informacji:

- W tym przewodniku Szybki start użyjemy nazwy **Contoso vault2**. W swoim teście musisz podać unikatową nazwę.
- Nazwa grupy zasobów: **ContosoResourceGroup**.
- Lokalizacja: **Wschodnie stany USA**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonej usługi Key Vault. Zanotuj dwie poniższe właściwości:

- **Nazwa magazynu** : w tym przykładzie jest to **Contoso-Vault2**. Ta nazwa będzie używana do innych poleceń usługi Key Vault.
- **Identyfikator URI magazynu** : w tym przykładzie jest to https://contoso-vault2.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-certificate-to-key-vault"></a>Dodawanie certyfikatu do Key Vault

Aby dodać certyfikat do magazynu, wystarczy wykonać kilka dodatkowych kroków. Ten certyfikat może być używany przez aplikację. 

Wpisz poniższe polecenia, aby utworzyć certyfikat z podpisem własnym z zasadami domyślnymi o nazwie **ExampleCertificate** :

```azurecli
az keyvault certificate create --vault-name "Contoso-Vault2" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Teraz można odwołać się do tego certyfikatu, który został dodany do Azure Key Vault przy użyciu identyfikatora URI. Użyj **" https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate "** , aby pobrać bieżącą wersję. 

Aby wyświetlić poprzednio zapisany certyfikat:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "Contoso-Vault2"
```

Teraz utworzono Key Vault, Zapisano certyfikat i pobieramy go.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group). Możesz usunąć zasoby w następujący sposób:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim certyfikat. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz odwołanie do [interfejsu wiersza polecenia platformy Azure AZ](/cli/azure/keyvault?view=azure-cli-latest)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań
