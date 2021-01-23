---
title: Dodawanie usługi Azure Storage (kontener)
description: Dowiedz się, jak dołączyć niestandardowy udział sieciowy do aplikacji w kontenerze w Azure App Service. Udostępnianie plików między aplikacjami, zdalne zarządzanie zawartością statyczną oraz uzyskiwanie dostępu lokalnie itp.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: ab27de9274043439f790a8fecd443223e5f26b08
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736208"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Dostęp do usługi Azure Storage (wersja zapoznawcza) jako udziału sieciowego z kontenera w App Service

::: zone pivot="container-windows"

W tym przewodniku przedstawiono sposób dołączania plików usługi Azure Storage jako udziału sieciowego do kontenera systemu Windows w App Service. Obsługiwane są tylko udziały [Azure Files udziały](../storage/files/storage-how-to-use-files-cli.md) i [pliki w warstwie Premium](../storage/files/storage-how-to-create-premium-fileshare.md) . Korzyści obejmują bezpieczną zawartość, przenośność zawartości, dostęp do wielu aplikacji i wiele metod transferu.

> [!NOTE]
>Usługa Azure Storage w App Service jest **w wersji zapoznawczej** i **nie jest obsługiwana** w **scenariuszach produkcyjnych**.

::: zone-end

::: zone pivot="container-linux"

W tym przewodniku przedstawiono sposób dołączania usługi Azure Storage do App Service kontenera systemu Linux. Korzyści obejmują bezpieczną zawartość, przenośność zawartości, magazyn trwały, dostęp do wielu aplikacji i wiele metod transferu.

> [!NOTE]
>Usługa Azure Storage w App Service jest **w wersji zapoznawczej** dla App Service w systemie Linux i Web App for Containers. Nie jest to **obsługiwane** w **scenariuszach produkcyjnych**.

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="container-windows"

- [Istniejąca aplikacja kontenera systemu Windows w Azure App Service](quickstart-custom-container.md)
- [Utwórz udział plików platformy Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Przekaż pliki do udziału plików platformy Azure](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Istniejąca [App Service w aplikacji systemu Linux](index.yml).
- [Konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Udział i katalog plików platformy Azure](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Files jest magazynem innym niż domyślne i są rozliczane oddzielnie, a nie dołączone do aplikacji sieci Web. Nie obsługuje ona konfigurowania zapory z powodu ograniczeń infrastruktury.
>

## <a name="limitations"></a>Ograniczenia

::: zone pivot="container-windows"

- Usługa Azure Storage w App Service nie jest obecnie **obsługiwana** w przypadku używania własnych scenariuszy kodu (niekontenerowe aplikacje systemu Windows).
- Usługa Azure Storage w App Service **nie obsługuje** korzystania z konfiguracji **zapory magazynu** z powodu ograniczeń infrastruktury.
- Usługa Azure Storage z App Service umożliwia określenie **maksymalnie pięciu** punktów instalacji na aplikację.
- Usługa Azure Storage zainstalowana w aplikacji nie jest dostępna za pomocą App Service punktów końcowych FTP/FTPs. Użyj [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Usługa Azure Storage w App Service obsługuje instalowanie **kontenerów Azure Files** (odczyt/zapis) i **kontenery obiektów blob platformy Azure** (tylko do odczytu)
- Usługa Azure Storage w App Service umożliwia określenie **maksymalnie pięciu** punktów instalacji na aplikację.
- Usługa Azure Storage zainstalowana w aplikacji nie jest dostępna za pomocą App Service punktów końcowych FTP/FTPs. Użyj [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Łączenie magazynu z aplikacją

::: zone pivot="container-windows"

Po utworzeniu [konta usługi Azure Storage, udziału plików i katalogu](#prerequisites)można teraz skonfigurować swoją aplikację w usłudze Azure Storage.

Aby zainstalować udział Azure Files w katalogu w aplikacji App Service, użyj [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) polecenia. Typ magazynu musi być migracji pamięci.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Należy to zrobić dla wszystkich innych katalogów, które mają być połączone z udziałem Azure Files.

::: zone-end

::: zone pivot="container-linux"

Po utworzeniu [konta usługi Azure Storage, udziału plików i katalogu](#prerequisites)można teraz skonfigurować swoją aplikację w usłudze Azure Storage.

Aby zainstalować konto magazynu w katalogu w aplikacji App Service, użyj [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) polecenia. Typem magazynu może być AzureBlob lub migracji pamięci. W tym przykładzie jest używana migracji pamięci. Ustawienie ścieżka instalacji odnosi się do folderu znajdującego się w kontenerze, który ma zostać zainstalowany w usłudze Azure Storage. Ustawienie opcji "/" powoduje zainstalowanie całego kontenera w usłudze Azure Storage.


> [!CAUTION]
> Katalog określony jako ścieżka instalacji w aplikacji sieci Web powinien być pusty. Każda zawartość przechowywana w tym katalogu zostanie usunięta po dodaniu instalacji zewnętrznej. Jeśli migrujesz pliki dla istniejącej aplikacji, Utwórz kopię zapasową aplikacji i jej zawartość przed rozpoczęciem.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Należy to zrobić dla wszystkich innych katalogów, które mają być połączone z kontem magazynu.

::: zone-end

## <a name="verify-linked-storage"></a>Weryfikowanie połączonego magazynu

Gdy udział zostanie połączony z aplikacją, możesz to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Następne kroki

::: zone pivot="container-windows"

- [Migruj niestandardowe oprogramowanie do Azure App Service przy użyciu niestandardowego kontenera](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Skonfiguruj kontener niestandardowy](configure-custom-container.md?pivots=platform-linux).

::: zone-end