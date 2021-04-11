---
title: Rejestrowanie modułu HSM zarządzanego przez platformę Azure
description: Skorzystaj z tego samouczka, aby rozpocząć pracę z zarządzanym rejestrowaniem modułu HSM.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: mbaldwin
ms.openlocfilehash: 0d5749894fd277ff6a2f77e3db9721e6989d72ac
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109241"
---
# <a name="managed-hsm-logging"></a>Rejestrowanie zarządzanego modułu HSM 

Po utworzeniu co najmniej jednego zarządzanego sprzętowych modułów zabezpieczeń prawdopodobnie zechcesz monitorować sposób i czas uzyskiwania dostępu do HSMss oraz tego, kto. Można to zrobić przez włączenie rejestrowania, które zapisuje informacje na koncie usługi Azure Storage, które podano. Nowy kontener o nazwie **Insights-Logs-auditevent** jest tworzony automatycznie dla określonego konta magazynu. Tego samego konta magazynu można używać do zbierania dzienników dla wielu zarządzanych sprzętowych modułów zabezpieczeń.

Możesz uzyskać dostęp do informacji o rejestrowaniu 10 minut (maksymalnie) po operacji zarządzanego modułu HSM. W większości przypadków czas będzie krótszy.  Zarządzanie dziennikami na Twoim koncie magazynu zależy od Ciebie:

* Aby zabezpieczyć dzienniki poprzez ograniczenie dostępu do nich, użyj standardowych metod kontroli dostępu platformy Azure.
* Usuń dzienniki, których już nie chcesz przechowywać na koncie magazynu.

Skorzystaj z tego samouczka, aby rozpocząć pracę z zarządzanym rejestrowaniem modułu HSM. Utworzysz konto magazynu, włączysz rejestrowanie i interpretujesz zebrane informacje dziennika.  

> [!NOTE]
> Ten samouczek nie zawiera instrukcji dotyczących sposobu tworzenia zarządzanych sprzętowych modułów zabezpieczeń lub kluczy. Ten artykuł zawiera instrukcje interfejsu wiersza polecenia platformy Azure dotyczące aktualizowania rejestrowania diagnostycznego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować następującymi elementami:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzany moduł HSM w ramach subskrypcji. Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu aprowizacji i aktywowania zarządzanego modułu HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

Pierwszym krokiem podczas konfigurowania rejestrowania kluczy jest wskazanie interfejsu wiersza polecenia platformy Azure do zarządzanego modułu HSM, który ma zostać zarejestrowana.

```azurecli-interactive
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)

Być może trzeba będzie określić subskrypcję, która została użyta do utworzenia zarządzanego modułu HSM. Wprowadź następujące polecenie, aby wyświetlić subskrypcje dla konta:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Identyfikowanie zarządzanego modułu HSM i konta magazynu

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie dla zarządzanego modułu HSM, użyj polecenia **AZ monitor Diagnostic-Settings Create** wraz ze zmiennymi utworzonymi dla nowego konta magazynu i zarządzanym modułem HSM. Ustawimy również flagę **-Enabled** na **$true** i ustawimy kategorię na **AuditEvent** (jedyna kategoria dla rejestrowania zarządzanego modułu HSM):

Dane wyjściowe potwierdzają, że rejestrowanie jest teraz włączone dla zarządzanego modułu HSM i zapisuje informacje na koncie magazynu.

Opcjonalnie możesz ustawić zasady przechowywania dla dzienników, tak aby starsze dzienniki były automatycznie usuwane. Na przykład ustaw zasady przechowywania przez ustawienie flagi **-retentionenable** na **$true** i ustaw parametr **-RetentionInDays** na **90** , aby dzienniki starsze niż 90 dni zostały automatycznie usunięte.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Co jest rejestrowane:

* Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądania zakończone niepowodzeniem w wyniku uprawnień dostępu, błędów systemu lub nieudanych żądań.
* Operacje zarządzanej płaszczyzny na zarządzanym urządzeniu modułu HSM, w tym tworzenie, usuwanie i aktualizowanie atrybutów, takich jak Tagi.
* Operacje związane z domeną zabezpieczeń, takie jak inicjowanie & pobieranie, inicjowanie odzyskiwania i przekazywanie
* Pełna kopia zapasowa modułu HSM, operacje przywracania i selektywnego przywracania
* Operacje zarządzania rolami, takie jak tworzenie/wyświetlanie/usuwanie przypisań ról oraz tworzenie/wyświetlanie/usuwanie niestandardowych definicji ról
* Operacje na kluczach, w tym:
  * Tworzenie, modyfikowanie lub usuwanie kluczy.
  * Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijanie i odpakowanie kluczy, Lista kluczy.
  * Kopia zapasowa klucza, przywracanie, przeczyszczanie
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykłady to żądania, które nie mają tokenu okaziciela, które są źle sformułowane lub wygasłe lub które mają nieprawidłowy token.  

## <a name="access-your-logs"></a>Uzyskiwanie dostępu do dzienników

Zarządzane dzienniki modułu HSM są przechowywane w kontenerze **Insights-Logs-auditevent** na podanym koncie magazynu. Aby wyświetlić dzienniki, należy pobrać obiekty blob. Aby uzyskać informacje na temat usługi Azure Storage, zobacz [Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

Poszczególne obiekty blob są przechowywane jako tekst, sformatowane jako dane JSON. Przyjrzyjmy się przykładowym wpisowi dziennika. W poniższym przykładzie przedstawiono wpis dziennika, gdy żądanie utworzenia pełnej kopii zapasowej jest wysyłane do zarządzanego modułu HSM.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```



## <a name="use-azure-monitor-logs"></a>Korzystanie z dzienników usługi Azure Monitor

Możesz użyć rozwiązania Key Vault w dziennikach Azure Monitor do przeglądania dzienników **AuditEvent** modułu HSM. W dziennikach Azure Monitor są używane zapytania dzienników do analizowania danych i uzyskiwania potrzebnych informacji.

Aby uzyskać więcej informacji, w tym o sposobie konfigurowania tego elementu, zobacz [Azure Key Vault w Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [najlepszych rozwiązaniach](best-practices.md) dotyczących udostępniania zarządzanego modułu HSM i korzystania z niego
- Informacje o [sposobie tworzenia kopii zapasowej i przywracania](backup-restore.md) zarządzanego modułu HSM
