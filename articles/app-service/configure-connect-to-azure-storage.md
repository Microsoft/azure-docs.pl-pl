---
title: Dodawanie usługi Azure Storage (kontener)
description: Dowiedz się, jak dołączyć niestandardowy udział sieciowy w konteneryzowanej aplikacji w Azure App Service. Udostępnianie plików między aplikacjami, zdalne zarządzanie zawartością statyczną i uzyskiwanie dostępu lokalnego itp.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777621"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Dostęp do usługi Azure Storage (wersja zapoznawcza) jako udziału sieciowego z poziomu kontenera w usłudze App Service

::: zone pivot="container-windows"

W tym przewodniku pokazano, jak dołączyć pliki usługi Azure Storage jako udział sieciowy do kontenera systemu Windows w App Service. Obsługiwane [są Azure Files udziałów i](../storage/files/storage-how-to-use-files-cli.md) udziałów plików [Premium.](../storage/files/storage-how-to-create-file-share.md) Korzyści obejmują zabezpieczoną zawartość, przenośność zawartości, dostęp do wielu aplikacji i wiele metod transferu.

> [!NOTE]
>Usługa Azure Storage w App Service jest w **wersji zapoznawczej** **i nie jest obsługiwana w scenariuszach** **produkcyjnych.**

::: zone-end

::: zone pivot="container-linux"

W tym przewodniku pokazano, jak dołączyć usługę Azure Storage do kontenera systemu Linux App Service. Korzyści obejmują zabezpieczoną zawartość, przenośność zawartości, trwały magazyn, dostęp do wielu aplikacji i wiele metod transferu.

> [!NOTE]
>Usługa Azure Storage w App Service jest **w wersji zapoznawczej** dla App Service dla systemu Linux i Web App for Containers. Nie jest to **obsługiwane w scenariuszach** **produkcyjnych.**

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="container-windows"

- [Istniejąca aplikacja kontenera systemu Windows w Azure App Service](quickstart-custom-container.md)
- [Tworzenie udziału plików platformy Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Przekazywanie plików do udziału plików platformy Azure](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Istniejąca [App Service dla systemu Linux aplikacji.](index.yml)
- Konto [usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-cli)
- Udział [plików platformy Azure i katalog](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Files jest magazynem nie domyślnym i jest rozliczany oddzielnie, ale nie jest dołączony do aplikacji internetowej. Nie obsługuje ona korzystania z konfiguracji zapory ze względu na ograniczenia infrastruktury.
>

## <a name="limitations"></a>Ograniczenia

::: zone pivot="container-windows"

- Usługa Azure Storage w App Service nie **jest obecnie obsługiwana w** scenariuszach "przynieź własnego kodu" (nie konteneryzowanych aplikacji systemu Windows).
- Usługa Azure Storage App Service **nie obsługuje konfiguracji** zapory magazynu ze względu na ograniczenia infrastruktury. 
- Usługa Azure Storage App Service umożliwia określenie **maksymalnie pięciu punktów** instalacji dla aplikacji.
- Usługa Azure Storage montowana w aplikacji nie jest dostępna za pośrednictwem App Service FTP/FTPs. Użyj [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Usługa Azure Storage w App Service obsługuje **instalowanie Azure Files kontenerów** (do odczytu/zapisu) i **kontenerów obiektów blob** platformy Azure (tylko do odczytu)
- Usługa Azure Storage App Service pozwala określić **maksymalnie pięć punktów** instalacji dla aplikacji.
- Usługa Azure Storage montowana w aplikacji nie jest dostępna za pośrednictwem App Service FTP/FTPs. Użyj [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Łączenie magazynu z aplikacją

::: zone pivot="container-windows"

Po utworzeniu konta usługi [Azure Storage, udziału](#prerequisites)plików i katalogu możesz teraz skonfigurować aplikację przy użyciu usługi Azure Storage.

Aby zainstalować aplikację Azure Files Share w katalogu w App Service aplikacji, użyj [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) polecenia . Typem magazynu musi być AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Należy to zrobić dla wszystkich innych katalogów, które mają być połączone z Azure Files udziału.

::: zone-end

::: zone pivot="container-linux"

Po utworzeniu konta usługi [Azure Storage, udziału](#prerequisites)plików i katalogu możesz teraz skonfigurować aplikację przy użyciu usługi Azure Storage.

Aby zainstalować konto magazynu w katalogu w App Service aplikacji, użyj [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) polecenia . Typ magazynu to AzureBlob lub AzureFiles. W tym przykładzie jest używana usługa AzureFiles. Ustawienie ścieżki instalacji odpowiada folderowi wewnątrz kontenera, który chcesz zainstalować w usłudze Azure Storage. Ustawienie go na "/" umożliwia skonfigurowanie całego kontenera w usłudze Azure Storage.


> [!CAUTION]
> Katalog określony jako ścieżka instalacji w aplikacji internetowej powinien być pusty. Zawartość przechowywana w tym katalogu zostanie usunięta po dodaniu instalacji zewnętrznej. W przypadku migrowania plików dla istniejącej aplikacji przed rozpoczęciem należy utworzyć kopię zapasową aplikacji i jej zawartości.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Należy to zrobić dla wszystkich innych katalogów, które mają zostać połączone z kontem magazynu.

::: zone-end

## <a name="verify-linked-storage"></a>Weryfikowanie połączonego magazynu

Gdy udział zostanie połączony z aplikacją, możesz to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Następne kroki

::: zone pivot="container-windows"

- [Migrowanie niestandardowego oprogramowania do Azure App Service przy użyciu kontenera niestandardowego.](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [Skonfiguruj kontener niestandardowy](configure-custom-container.md?pivots=platform-linux).

::: zone-end