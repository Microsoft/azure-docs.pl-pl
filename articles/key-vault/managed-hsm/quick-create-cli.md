---
title: Szybki Start — udostępnianie i aktywowanie zarządzanego modułu HSM platformy Azure
description: Przewodnik Szybki Start przedstawiający sposób aprowizacji i aktywowania zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 705e389c83fbab6075c25a3f56e5392fb8cafcd9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91001023"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Szybki Start: udostępnianie zarządzanego modułu HSM i aktywowanie go przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Key Vault zarządzanym modułem HSM jest w pełni zarządzana usługa w chmurze o wysokiej dostępności, która jest zgodna ze standardami, która umożliwia Zabezpieczanie kluczy kryptograficznych dla aplikacji w chmurze przy użyciu zweryfikowanych sprzętowych modułów zabezpieczeń **poziomu 3 w trybie FIPS 140-2** . Aby uzyskać więcej informacji na temat zarządzanego modułu HSM, można zapoznać się z [omówieniem](overview.md). 

W tym przewodniku szybki start utworzysz i uaktywniasz zarządzany moduł HSM przy użyciu interfejsu wiersza polecenia platformy Azure. a następnie umieszczanie w nim wpisu tajnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować następującymi elementami:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzany moduł HSM w ramach subskrypcji. Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu aprowizacji i aktywowania zarządzanego modułu HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *ContosoResourceGroup* w lokalizacji *eastus2* .

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Tworzenie zarządzanego modułu HSM

Tworzenie zarządzanego modułu HSM jest procesem dwuetapowym:
1. Inicjowanie obsługi zarządzanego zasobu modułu HSM.
1. Aktywuj zarządzany moduł HSM, pobierając domenę zabezpieczeń.

### <a name="provision-a-managed-hsm"></a>Obsługa zarządzanego modułu HSM

Użyj `az keyvault create` polecenia, aby utworzyć zarządzany moduł HSM. Ten skrypt ma trzy parametry obowiązkowe: nazwę grupy zasobów, nazwę modułu HSM i lokalizację geograficzną.

Musisz podać następujące dane wejściowe, aby utworzyć zarządzany zasób modułu HSM:
- Grupa zasobów, w której zostanie umieszczona w Twojej subskrypcji.
- Lokalizacja platformy Azure.
- Lista administratorów początkowych.

Poniższy przykład tworzy moduł HSM o nazwie **ContosoMHSM**w grupie zasobów  **ContosoResourceGroup**, znajdujący się w lokalizacji **Wschodnie stany USA 2** , z **obecnie zalogowanym użytkownikiem** jako jedynego administratora.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Polecenie CREATE może potrwać kilka minut. Po pomyślnym powrocie można aktywować moduł HSM.

Dane wyjściowe tego polecenia przedstawiają właściwości zarządzanego modułu HSM, który został utworzony. Dwie najważniejsze właściwości to:

* **Nazwa**: w tym przykładzie nazwa to ContosoMHSM. Ta nazwa będzie używana do innych poleceń Key Vault.
* **hsmUri**: w tym przykładzie identyfikator URI to https://contosohsm.managedhsm.azure.net . Aplikacje korzystające z modułu HSM za pomocą interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym zarządzanym module HSM. Od tej pory nikt nie jest autoryzowany.

### <a name="activate-your-managed-hsm"></a>Aktywuj zarządzany moduł HSM

Wszystkie polecenia płaszczyzny danych są wyłączone do momentu aktywowania modułu HSM. Nie będzie można tworzyć kluczy ani przypisywać ról. Tylko Wyznaczeni Administratorzy przypisani podczas tworzenia polecenia mogą aktywować moduł HSM. Aby aktywować moduł HSM, należy pobrać [domenę zabezpieczeń](security-domain.md).

Aby aktywować moduł HSM, potrzebujesz:
- Minimum 3 pary kluczy RSA (maksymalnie 10)
- Określ minimalną liczbę kluczy potrzebną do odszyfrowania domeny zabezpieczeń (kworum)

Aby aktywować moduł HSM, należy wysłać co najmniej 3 (maksymalnie 10) kluczy publicznych RSA do modułu HSM. Moduł HSM szyfruje domenę zabezpieczeń przy użyciu tych kluczy i wysyła je z powrotem. Po pomyślnym ukończeniu pobierania tej domeny zabezpieczeń moduł HSM jest gotowy do użycia. Należy również określić kworum, czyli minimalną liczbę kluczy prywatnych wymaganych do odszyfrowania domeny zabezpieczeń.

W poniższym przykładzie przedstawiono sposób użycia  `openssl` programu w celu wygenerowania 3-z certyfikatem z podpisem własnym.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Utwórz i Zapisz pary kluczy RSA i plik domeny zabezpieczeń, które zostały wygenerowane w tym kroku bezpiecznie.

Użyj `az keyvault security-domain download` polecenia, aby pobrać domenę zabezpieczeń i aktywować zarządzany moduł HSM. W poniższym przykładzie zastosowano 3 pary kluczy RSA (tylko klucze publiczne są konieczne dla tego polecenia) i ustawia kworum na 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Zapisz plik domeny zabezpieczeń i pary kluczy RSA bezpiecznie. Będą one potrzebne do odzyskiwania po awarii lub tworzenia innego zarządzanego modułu HSM, który ma udział w tej samej domenie zabezpieczeń, dzięki czemu mogą udostępniać klucze.

Po pomyślnym pobraniu domeny zabezpieczeń moduł HSM będzie w stanie aktywny i będzie gotowy do użycia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group). Możesz usunąć zasoby w następujący sposób:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim wpis tajny. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem zarządzanego modułu HSM](overview.md)
- Dowiedz się więcej o [zarządzaniu kluczami w zarządzanym module HSM](key-management.md)
- Przegląd [najlepszych](best-practices.md) rozwiązań modułu HSM
