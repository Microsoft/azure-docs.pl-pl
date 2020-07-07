---
title: Obsługuj zawartość z usługi Azure Storage w kontenerach systemu Linux
description: Dowiedz się, jak dołączyć niestandardowy udział sieciowy do kontenera systemu Linux w Azure App Service. Udostępnianie plików między aplikacjami, zdalne zarządzanie zawartością statyczną oraz uzyskiwanie dostępu lokalnie itp.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 9a5a38ea32d927f50fb9ddbebe3e1c3533e6fcc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82625327"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Obsługuj zawartość z usługi Azure Storage w App Service w systemie Linux

> [!NOTE]
> Ten artykuł ma zastosowanie do kontenerów systemu Linux. Aby wdrożyć niestandardowe kontenery systemu Windows, zobacz [konfigurowanie Azure Files w kontenerze systemu Windows na App Service](../configure-connect-to-azure-storage.md). Usługa Azure Storage w App Service w systemie Linux jest funkcją w **wersji zapoznawczej** . Ta funkcja **nie jest obsługiwana w scenariuszach produkcyjnych**.
>

W tym przewodniku przedstawiono sposób dołączania usługi Azure Storage do App Service w systemie Linux. Korzyści obejmują bezpieczną zawartość, przenośność zawartości, magazyn trwały, dostęp do wielu aplikacji i wiele metod transferu.

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.46 lub nowszy).
- Istniejąca [App Service w aplikacji systemu Linux](https://docs.microsoft.com/azure/app-service/containers/).
- [Konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Udział i katalog plików platformy Azure](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Ograniczenia dotyczące usługi Azure Storage za pomocą App Service

- Usługa Azure Storage z App Service jest **w wersji zapoznawczej** dla App Service w systemie Linux i Web App for Containers. Nie jest to **obsługiwane** w **scenariuszach produkcyjnych**.
- Usługa Azure Storage z App Service obsługuje instalowanie **kontenerów Azure Files** (odczyt/zapis) i **kontenery obiektów blob platformy Azure** (tylko do odczytu)
- Usługa Azure Storage z App Service **nie obsługuje** korzystania z konfiguracji **zapory magazynu** z powodu ograniczeń infrastruktury.
- Usługa Azure Storage z App Service umożliwia określenie **maksymalnie pięciu** punktów instalacji na aplikację.
- Usługa Azure Storage zainstalowana w aplikacji nie jest dostępna za pomocą App Service punktów końcowych FTP/FTPs. Użyj [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
- Usługa Azure Storage **nie jest dołączona** do Twojej aplikacji sieci Web i rozliczana osobno. Dowiedz się więcej o [cenach usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Konfiguracje App Service przy użyciu usługi Azure Blob Storage staną się tylko do odczytu w lutym 2020. [Dowiedz się więcej](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Konfigurowanie aplikacji za pomocą usługi Azure Storage

Po utworzeniu [konta usługi Azure Storage, udziału plików i katalogu](#prerequisites)można teraz skonfigurować swoją aplikację w usłudze Azure Storage.

Aby zainstalować konto magazynu w katalogu w aplikacji App Service, użyj [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) polecenia. Typem magazynu może być AzureBlob lub migracji pamięci. W tym przykładzie jest używana migracji pamięci. Ustawienie ścieżka instalacji odnosi się do folderu, który ma zostać zainstalowany z usługi Azure Storage. Ustawienie opcji "/" powoduje zainstalowanie całego magazynu platformy Azure.


> [!CAUTION]
> Katalog określony jako ścieżka instalacji w aplikacji sieci Web powinien być pusty. Każda zawartość przechowywana w tym katalogu zostanie usunięta po dodaniu instalacji zewnętrznej. Jeśli migrujesz pliki dla istniejącej aplikacji, Utwórz kopię zapasową aplikacji i jej zawartość przed rozpoczęciem.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Należy to zrobić dla wszystkich innych katalogów, które mają być połączone z kontem magazynu.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Weryfikowanie linku usługi Azure Storage do aplikacji sieci Web

Po połączeniu kontenera magazynu z aplikacją internetową można to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Korzystanie z usługi Azure Storage w Docker Compose

Usługę Azure Storage można zainstalować za pomocą aplikacji wielokontenerowych przy użyciu identyfikatora niestandardowego. Aby wyświetlić nazwę niestandardowego ID, uruchom polecenie [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list) .

W pliku *Docker-Compose. yml* zamapuj `volumes` opcję na `custom-id` . Na przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj aplikacje sieci Web w Azure App Service](../configure-common.md).

