---
title: Samouczek — Importowanie certyfikatu w Key Vault przy użyciu Azure Portal | Microsoft Docs
description: Samouczek przedstawiający sposób importowania certyfikatu w programie Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: b7b15c1c4952f6e0a606b1ed46f1588c536b4ae2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204086"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Samouczek: Importowanie certyfikatu w Azure Key Vault

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym samouczku utworzysz Magazyn kluczy, a następnie użyjesz go do zaimportowania certyfikatu. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](../general/overview.md).

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz magazyn kluczy.
> * Zaimportuj certyfikat w Key Vault przy użyciu portalu.
> * Zaimportuj certyfikat w Key Vault przy użyciu interfejsu wiersza polecenia.
> * Zaimportuj certyfikat w Key Vault przy użyciu programu PowerShell.


Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](../general/basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.
2. W polu wyszukiwania wprowadź **Key Vault**.
3. Na liście wyników wybierz pozycję **Key Vault**.
4. W sekcji Key Vault, wybierz przycisk **Utwórz**.
5. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:
    - **Nazwa**: wymagana jest unikatowa nazwa. W tym przewodniku szybki start użyjemy **przykładowego magazynu**. 
    - **Subskrypcja**: wybierz subskrypcję.
    - W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    - W menu rozwijanym **Lokalizacja** wybierz lokalizację.
    - Dla pozostałych opcji zostaw wartości domyślne.
6. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.

Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu**: w tym przykładzie **przykład-magazyn**. Użyjesz tej nazwy w innych krokach.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://example-vault.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importuj certyfikat do Key Vault

Aby zaimportować certyfikat do magazynu, należy mieć plik certyfikatu PEM lub PFX na dysku. W takim przypadku zostanie zaimportowany certyfikat z nazwą pliku o nazwie **ExampleCertificate**.

> [!IMPORTANT]
> W usłudze Azure Key Vault obsługiwane są certyfikaty w formatach PFX i PEM. 
> - Format pliku PEM zawiera co najmniej jeden plik certyfikatu x509.
> - Format pliku PFX to format pliku archiwum służący do przechowywania kilku obiektów kryptograficznych w jednym pliku, tj. certyfikat serwera (wystawiony dla Twojej domeny), odpowiadający mu klucz prywatny i opcjonalnie może zawierać pośredni urząd certyfikacji.  

1. Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
2. Kliknij pozycję **Wygeneruj/zaimportuj**.
3. Na ekranie **Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu**: import.
    - **Nazwa certyfikatu**: ExampleCertificate.
    - **Przekaż plik certyfikatu**: Wybierz plik certyfikatu z dysku
    - **Hasło** : w przypadku przekazywania pliku certyfikatu chronionego hasłem Podaj tutaj hasło. W przeciwnym razie pozostaw to pole puste. Po pomyślnym zaimportowaniu pliku certyfikatu Magazyn kluczy usunie to hasło.
4. Kliknij pozycję **Utwórz**.

![Właściwości certyfikatu](../media/certificates/tutorial-import-cert/cert-import.png)

Dodanie certyfikatu przy użyciu metody **Import** spowoduje, że magazyn kluczy Azure automatycznie wypełni parametry certyfikatu (tj. okres ważności, nazwę wystawcy, datę aktywacji itp.).

Po otrzymaniu komunikatu o pomyślnym zaimportowaniu certyfikatu można kliknąć go na liście, aby wyświetlić jego właściwości. 

![Zrzut ekranu pokazujący, gdzie wyświetlić właściwości certyfikatu.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importowanie certyfikatu przy użyciu interfejsu wiersza polecenia platformy Azure

Zaimportuj certyfikat do określonego magazynu kluczy. Aby zaimportować istniejący prawidłowy certyfikat zawierający klucz prywatny do Azure Key Vault, plik do zaimportowania może być w formacie PFX lub PEM. Jeśli certyfikat jest w formacie PEM, plik PEM musi zawierać klucz oraz certyfikaty x509. Ta operacja wymaga uprawnień do certyfikatów/importu.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

Dowiedz się więcej o [parametrach](/cli/azure/keyvault/certificate#az-keyvault-certificate-import).

Po zaimportowaniu certyfikatu można wyświetlić certyfikat przy użyciu polecenia [Pokaż certyfikat](/cli/azure/keyvault/certificate#az-keyvault-certificate-show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Teraz utworzono Magazyn kluczy, zaimportowano certyfikat i Wyświetlono właściwości certyfikatu.

## <a name="import-a-certificate-using-azure-powershell"></a>Importowanie certyfikatu przy użyciu Azure PowerShell

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

Dowiedz się więcej o [parametrach](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono Key Vault i zaimportowano do niego certyfikat. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Przeczytaj więcej [na temat zarządzania tworzeniem certyfikatów w Azure Key Vault](./create-certificate-scenarios.md)
- Zobacz przykłady [importowania certyfikatów przy użyciu interfejsów API REST](/rest/api/keyvault/importcertificate/importcertificate)
- Zapoznaj się z [omówieniem zabezpieczeń Key Vault](../general/security-overview.md)