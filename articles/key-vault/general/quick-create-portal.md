---
title: Szybki start — tworzenie Azure Key Vault pomocą Azure Portal
description: Przewodnik Szybki start przedstawiający sposób tworzenia aplikacji Azure Key Vault użyciu Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 2d8bfdf3a4ad8b713fb6c937d61fa437fc7977f2
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749676"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>Szybki start: tworzenie magazynu kluczy przy użyciu Azure Portal

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn [kluczy,](../keys/index.yml) [wpisów tajnych](../secrets/index.yml) [i certyfikatów.](../certificates/index.yml) Aby uzyskać więcej informacji na Key Vault, zobacz [Informacje o Azure Key Vault](overview.md); Aby uzyskać więcej informacji na temat tego, co można przechowywać w magazynie kluczy, zobacz About keys, secrets, and certificates (Informacje o [kluczach, wpisach tajnych i certyfikatach).](about-keys-secrets-certificates.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

W tym przewodniku Szybki start utworzysz magazyn kluczy przy użyciu Azure Portal [.](https://portal.azure.com) 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. W menu Azure Portal lub na **stronie** głównej wybierz pozycję Utwórz **zasób.**
2. W polu Wyszukaj wprowadź **Key Vault**.
3. Na liście wyników wybierz pozycję **Key Vault**.
4. W sekcji Key Vault, wybierz przycisk **Utwórz**.
5. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:
    - **Nazwa**: wymagana jest unikatowa nazwa. W tym przewodniku Szybki start **użyjemy magazynu Contoso-vault2.** 
    - **Subskrypcja**: wybierz subskrypcję.
    - W **obszarze Grupa zasobów** wybierz pozycję Utwórz **nową** i wprowadź nazwę grupy zasobów.
    - W menu rozwijanym **Lokalizacja** wybierz lokalizację.
    - Dla pozostałych opcji zostaw wartości domyślne.
6. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.

Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu**: w tym przykładzie jest to **Contoso-Vault2**. Użyjesz tej nazwy w innych krokach.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://contoso-vault2.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](../media/quick-create-portal/vault-properties.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault pomocą Azure Portal. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](overview.md)
- Przejrzyj omówienie [Azure Key Vault zabezpieczeń](security-overview.md)
- Zobacz Azure Key Vault [dewelopera](developers-guide.md)
