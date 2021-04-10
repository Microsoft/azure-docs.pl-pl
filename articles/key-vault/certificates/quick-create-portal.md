---
title: Przewodnik Szybki Start platformy Azure — Ustawianie i pobieranie certyfikatu z Key Vault przy użyciu Azure Portal | Microsoft Docs
description: Przewodnik Szybki Start przedstawiający sposób ustawiania i pobierania certyfikatu z Azure Key Vault przy użyciu Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: e55c0832638105ad681f74cbeb6429a6704b7fb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935142"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Szybki Start: Ustawianie i pobieranie certyfikatu z Azure Key Vault przy użyciu Azure Portal

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym przewodniku szybki start utworzysz Magazyn kluczy, a następnie użyjesz go do przechowywania certyfikatu. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](../general/overview.md).

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
* **Identyfikator URI magazynu**: w tym przykładzie jest to `https://example-vault.vault.azure.net/`. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>Dodawanie certyfikatu do Key Vault

Aby dodać certyfikat do magazynu, wystarczy wykonać kilka dodatkowych kroków. W tym przypadku dodamy certyfikat z podpisem własnym, który może być używany przez aplikację. Certyfikat ma nazwę **ExampleCertificate**.

1. Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
2. Kliknij pozycję **Wygeneruj/zaimportuj**.
3. Na ekranie **Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu**: generate.
    - **Nazwa certyfikatu**: ExampleCertificate.
    - **Podmiot**: CN = ExampleDomain
    - Dla pozostałych opcji zostaw wartości domyślne. Kliknij pozycję **Utwórz**.

Po otrzymaniu komunikatu o pomyślnym utworzeniu certyfikatu można go kliknąć na liście. Zostaną wtedy wyświetlone niektóre jego właściwości. Jeśli klikniesz bieżącą wersję, zostanie wyświetlona wartość określona w poprzednim kroku.

![Właściwości certyfikatu](../media/certificates/quick-create-portal/current-version-hidden.png)

## <a name="export-certificate-from-key-vault"></a>Eksportuj certyfikat z Key Vault
Klikając przycisk "Pobierz w formacie CER" lub "Pobierz w formacie PFX/PEM", możesz pobrać certyfikat. 

![Pobieranie certyfikatu](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim certyfikat. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [omówieniem zabezpieczeń Key Vault](../general/security-overview.md)
