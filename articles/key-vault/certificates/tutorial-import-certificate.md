---
title: Samouczek — importowanie certyfikatu w Key Vault przy użyciu Azure Portal | Microsoft Docs
description: Samouczek przedstawiający sposób importowania certyfikatu w Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: aebb0fa5da4cef655acc424c5ac90b628a4ba3af
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814446"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Samouczek: importowanie certyfikatu w Azure Key Vault

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym samouczku utworzysz magazyn kluczy, a następnie użyjemy go do zaimportowania certyfikatu. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](../general/overview.md).

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz magazyn kluczy.
> * Zaimportuj certyfikat Key Vault portalu.
> * Zaimportuj certyfikat w Key Vault przy użyciu interfejsu wiersza polecenia.
> * Zaimportuj certyfikat w Key Vault przy użyciu programu PowerShell.


Przed rozpoczęciem zapoznaj się [z Key Vault podstawowymi pojęciami.](../general/basic-concepts.md) 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. W menu Azure Portal lub na **stronie** głównej wybierz pozycję Utwórz **zasób.**
2. W polu Wyszukaj wprowadź **Key Vault**.
3. Na liście wyników wybierz pozycję **Key Vault**.
4. W sekcji Key Vault, wybierz przycisk **Utwórz**.
5. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:
    - **Nazwa**: wymagana jest unikatowa nazwa. W tym przewodniku Szybki start **użyjemy example-vault**. 
    - **Subskrypcja**: wybierz subskrypcję.
    - W **obszarze Grupa zasobów** wybierz pozycję Utwórz **nową** i wprowadź nazwę grupy zasobów.
    - W menu rozwijanym **Lokalizacja** wybierz lokalizację.
    - Dla pozostałych opcji zostaw wartości domyślne.
6. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.

Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu:** w tym przykładzie jest to **example-vault**. Użyjesz tej nazwy w innych krokach.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://example-vault.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importowanie certyfikatu do Key Vault

Aby zaimportować certyfikat do magazynu, plik certyfikatu PEM lub PFX musi być na dysku. W tym przypadku zaimportujemy certyfikat o nazwie pliku **o nazwie ExampleCertificate**.

> [!IMPORTANT]
> W usłudze Azure Key Vault obsługiwane są certyfikaty w formatach PFX i PEM. 
> - Format pliku PEM zawiera co najmniej jeden plik certyfikatu X509.
> - Format pliku pfx to format pliku archiwum do przechowywania kilku obiektów kryptograficznych w jednym pliku, tj. certyfikat serwera (wystawiony dla domeny), pasujący klucz prywatny i opcjonalnie może zawierać pośredni urząd certyfikacji.  

1. Na Key Vault właściwości wybierz pozycję **Certyfikaty.**
2. Kliknij pozycję **Wygeneruj/zaimportuj**.
3. Na **ekranie Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu:** Importuj.
    - **Nazwa certyfikatu:** ExampleCertificate.
    - **Przekaż plik certyfikatu:** wybierz plik certyfikatu z dysku
    - **Hasło:** w przypadku przekazywania pliku certyfikatu chronionego hasłem podaj to hasło w tym miejscu. W przeciwnym razie pozostaw to pole puste. Po pomyślnym zaimportowaniu pliku certyfikatu magazyn kluczy usunie to hasło.
4. Kliknij pozycję **Utwórz**.

![Właściwości certyfikatu](../media/certificates/tutorial-import-cert/cert-import.png)

Dodając certyfikat przy użyciu metody **importu,** usługa Azure Key Vault automatycznie wypełni parametry certyfikatu (tj. okres ważności, nazwę wystawcy, datę aktywacji itp.).

Po otrzymaniu komunikatu, że certyfikat został pomyślnie zaimportowany, możesz kliknąć go na liście, aby wyświetlić jego właściwości. 

![Zrzut ekranu przedstawiający miejsce wyświetlania właściwości certyfikatu.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

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

Dowiedz się więcej o [parametrach](/cli/azure/keyvault/certificate#az_keyvault_certificate_import).

Po zaimportowaniu certyfikatu można wyświetlić certyfikat przy użyciu funkcji [Pokaż certyfikat](/cli/azure/keyvault/certificate#az_keyvault_certificate_show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Teraz utworzono magazyn kluczy, zaimportowano certyfikat i przejrzeno właściwości certyfikatu.

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

Dowiedz się więcej o [parametrach](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?).


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono Key Vault zaimportowano w nim certyfikat. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj więcej na [temat zarządzania tworzeniem certyfikatów w Azure Key Vault](./create-certificate-scenarios.md)
- Zobacz przykłady [importowania certyfikatów przy użyciu interfejsów API REST](/rest/api/keyvault/importcertificate/importcertificate)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)