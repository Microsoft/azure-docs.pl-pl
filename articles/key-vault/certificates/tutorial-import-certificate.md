---
title: Samouczek — importowanie certyfikatu w magazynie kluczy przy użyciu witryny Azure portal | Dokumenty firmy Microsoft
description: Samouczek przedstawiający importowanie certyfikatu w usłudze Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/03/2020
ms.author: sebansal
ms.openlocfilehash: 754f30f7931f9fad6a95328cbf8ab34f70cb75a0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423110"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Samouczek: Importowanie certyfikatu w usłudze Azure Key Vault

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym samouczku utworzysz magazyn kluczy, a następnie użyj go do zaimportowania certyfikatu. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](../general/overview.md).

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Importowanie certyfikatu w przechowalni kluczy przy użyciu portalu.
> * Importowanie certyfikatu w magazynie kluczy przy użyciu interfejsu wiersza polecenia.


Przed rozpoczęciem przeczytaj [podstawowe pojęcia programu Key Vault](../general/basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Z menu Portalu Platformy Azure lub ze strony **głównej** wybierz pozycję **Utwórz zasób**.
2. W polu wyszukiwania wpisz **Key Vault**.
3. Na liście wyników wybierz pozycję **Key Vault**.
4. W sekcji Key Vault, wybierz przycisk **Utwórz**.
5. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:
    - **Nazwa**: wymagana jest unikatowa nazwa. W tym przewodniku Szybki start używamy **przykładu-Vault**. 
    - **Subskrypcja**: wybierz subskrypcję.
    - W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy** i wprowadź nazwę grupy zasobów.
    - W menu rozwijanym **Lokalizacja** wybierz lokalizację.
    - Dla pozostałych opcji zostaw wartości domyślne.
6. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.

Zanotuj dwie poniższe właściwości:

* **Nazwa przechowalni:** W przykładzie jest to **Przykład-Vault**. Użyjesz tej nazwy w innych krokach.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://example-vault.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importowanie certyfikatu do magazynu kluczy

Aby zaimportować certyfikat do przechowalni, musisz mieć plik certyfikatu PEM lub PFX, aby znajdować się na dysku. W takim przypadku zaimportujemy certyfikat o nazwie **ExampleCertificate**.

> [!IMPORTANT]
> W usłudze Azure Key Vault obsługiwane formaty certyfikatów to PFX i PEM. 
> - Format pliku pem zawiera jeden lub więcej plików certyfikatów X509.
> - Format pliku pfx jest formatem pliku archiwum do przechowywania kilku obiektów kryptograficznych w jednym pliku, czyli certyfikatu serwera (wystawionego dla domeny), pasującego klucza prywatnego i może opcjonalnie zawierać pośredni urząd certyfikacji.  

1. Na stronach właściwości magazynu kluczy wybierz pozycję **Certyfikaty**.
2. Kliknij pozycję **Wygeneruj/zaimportuj**.
3. Na ekranie **Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu:** Import.
    - **Nazwa certyfikatu**: ExampleCertificate.
    - **Przekaż plik certyfikatu**: wybierz plik certyfikatu z dysku
    - Dla pozostałych opcji zostaw wartości domyślne. Kliknij przycisk **Utwórz**.

![Właściwości certyfikatu](../media/certificates/tutorial-import-cert/cert-import.png)

Po otrzymaniu komunikatu o pomyślnym zaimportowaniu certyfikatu można kliknąć go na liście. Następnie można wyświetlić niektóre z jego właściwości. 

![Właściwości certyfikatu](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importowanie certyfikatu przy użyciu interfejsu wiersza polecenia platformy Azure

Importowanie certyfikatu do określonego magazynu kluczy. Aby zaimportować istniejący prawidłowy certyfikat zawierający klucz prywatny do usługi Azure Key Vault, plik do zaimportowania może być w formacie PFX lub PEM. Jeśli certyfikat jest w formacie PEM, plik PEM musi zawierać klucz, a także certyfikaty x509. Ta operacja wymaga certyfikatów/uprawnień importu.

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
Dowiedz się więcej o parametrach [tutaj](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono przechowalnię kluczy i zaimportowano w nim certyfikat. Aby dowiedzieć się więcej o programie Key Vault i integruj go z aplikacjami, przejdź do poniższych artykułów.

- Dowiedz się więcej o [zarządzaniu certyfikatami w usłudze Azure Key Vault](/archive/blogs/kv/manage-certificates-via-azure-key-vault)
- Zobacz przykłady [importowania certyfikatów przy użyciu interfejsów API REST](/rest/api/keyvault/importcertificate/importcertificate)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](../general/best-practices.md)