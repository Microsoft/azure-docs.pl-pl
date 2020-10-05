---
title: Przewodnik Szybki Start platformy Azure — tworzenie zarządzanego modułu HSM przy użyciu szablonu Azure Resource Manager
description: Przewodnik Szybki Start przedstawiający sposób tworzenia usługi Azure Azure Key Vault zarządzanego modułu HSM przy użyciu szablonu Menedżer zasobów
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 4d1488d6dd2e5d08ae774ca88b7ab41b2020efe5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91000959"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Szybki Start: Tworzenie Key Vault zarządzanego modułu HSM przy użyciu szablonu Azure Resource Manager

Zarządzany moduł HSM to w pełni zarządzana usługa w chmurze o wysokiej dostępności, która jest zgodna ze standardami, która umożliwia Zabezpieczanie kluczy kryptograficznych dla aplikacji w chmurze przy użyciu zweryfikowanych sprzętowych modułów zabezpieczeń **poziomu 3 FIPS 140-2** .  

Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Menedżer zasobów w celu utworzenia zarządzanego modułu HSM.  [Szablon usługi Resource Manager](../../azure-resource-manager/templates/overview.md) to plik w formacie JavaScript Object Notation (JSON) definiujący infrastrukturę i konfigurację projektu. Szablon używa składni deklaratywnej, która pozwala określić, co zamierzasz wdrożyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. Aby dowiedzieć się więcej na temat opracowywania szablonów usługi Resource Manager, zobacz [dokumentację usługi Resource Manager](../../azure-resource-manager/index.yml) i [dokumentację szablonu](/azure/templates/microsoft.keyvault/allversions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować następującymi elementami:

- Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
- Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli musisz zainstalować lub uaktualnić program, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-manage-hsm"></a>Tworzenie modułu HSM zarządzania

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

Zasób platformy Azure zdefiniowany w szablonie:

* **Microsoft./Magazyn kluczy/managedHSMs**: Tworzenie Azure Key Vault zarządzanego modułu HSM.

Więcej przykładów szablonów Azure Key Vault można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

Szablon wymaga identyfikatora obiektu skojarzonego z Twoim kontem. Aby go znaleźć, użyj interfejsu wiersza polecenia platformy Azure [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest&preserve-view=true#az_ad_user_show) , przekazując swój adres e-mail do `--id` parametru. Dane wyjściowe można ograniczyć do identyfikatora obiektu tylko przy użyciu `--query` parametru.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Może być również potrzebny identyfikator dzierżawy. Aby go znaleźć, użyj interfejsu wiersza polecenia platformy Azure [AZ AD User show](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_show) . Dane wyjściowe można ograniczyć do identyfikatora dzierżawy tylko przy użyciu `--query` parametru.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.

    O ile nie jest określona, użyj wartości domyślnej, aby utworzyć magazyn kluczy i klucz tajny.

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    - **Lokalizacja**: Wybierz lokalizację. Na przykład **Południowo-środkowe stany USA**.
    - **managedHSMName**: Wprowadź nazwę zarządzanego modułu HSM.
    - **Jednostka SKU**: Podaj nazwę i rodzinę zarządzanego modułu HSM, który chcesz utworzyć.  W tym przewodniku szybki start wprowadź "Standard_B1" dla nazwy i "B" dla rodziny.
    - **Identyfikator dzierżawy**: Funkcja szablonu automatycznie pobiera swój identyfikator dzierżawy; nie zmieniaj wartości domyślnej.  Jeśli nie ma wartości, wprowadź identyfikator dzierżawy, który został pobrany w [wymaganiach wstępnych](#prerequisites).
    * **initialAdminObjectIds**: Wprowadź identyfikator obiektu, który został pobrany w [wymaganiach wstępnych](#prerequisites).

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu magazynu kluczy otrzymujesz powiadomienie:

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zarządzany moduł HSM. Ten zarządzany moduł HSM nie będzie w pełni funkcjonalny, dopóki nie zostanie aktywowany. Zobacz sekcję [Aktywowanie zarządzanego modułu HSM](quick-create-cli.md#activate-your-managed-hsm) , aby dowiedzieć się, jak aktywować moduł HSM.

- Zapoznaj się [z omówieniem zarządzanego modułu HSM](overview.md)
- Dowiedz się więcej o [zarządzaniu kluczami w zarządzanym module HSM](key-management.md)
- Przegląd [najlepszych](best-practices.md) rozwiązań modułu HSM