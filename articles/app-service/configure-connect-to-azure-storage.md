---
title: Dodawanie magazynu niestandardowego (kontener systemu Windows)
description: Dowiedz się, jak dołączyć niestandardowy udział sieciowy w niestandardowym kontenerze systemu Windows w Azure App Service. Udostępnianie plików między aplikacjami, zdalne zarządzanie zawartością statyczną oraz uzyskiwanie dostępu lokalnie itp.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77120673"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurowanie Azure Files w kontenerze systemu Windows na App Service

> [!NOTE]
> Ten artykuł dotyczy niestandardowych kontenerów systemu Windows. Aby wdrożyć program w celu App Service w systemie _Linux_, zobacz temat [obsługiwanie zawartości z usługi Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

W tym przewodniku pokazano, jak uzyskać dostęp do usługi Azure Storage w kontenerach systemu Windows. Obsługiwane są tylko udziały [Azure Files udziały](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) i [pliki w warstwie Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) . W tej metodzie należy używać udziałów Azure Files. Korzyści obejmują bezpieczną zawartość, przenośność zawartości, dostęp do wielu aplikacji i wiele metod transferu.

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.46 lub nowszy).
- [Istniejąca aplikacja kontenera systemu Windows w Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Utwórz udział plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Przekaż pliki do udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files jest magazynem innym niż domyślne i są rozliczane oddzielnie, a nie dołączone do aplikacji sieci Web. Nie obsługuje ona konfigurowania zapory z powodu ograniczeń infrastruktury.
>

## <a name="limitations"></a>Ograniczenia

- Usługa Azure Storage w kontenerach systemu Windows jest **w wersji zapoznawczej** i **nie jest obsługiwana** w **scenariuszach produkcyjnych**.
- Usługa Azure Storage w kontenerach systemu Windows obsługuje instalowanie **kontenerów Azure Files** (tylko do odczytu i zapisu).
- Usługa Azure Storage w kontenerach systemu Windows nie jest obecnie **obsługiwana** w celu przeprowadzenia własnych scenariuszy kodu w planach App Service systemu Windows.
- Usługa Azure Storage w kontenerach systemu Windows **nie obsługuje** korzystania z konfiguracji **zapory magazynu** z powodu ograniczeń infrastruktury.
- Usługa Azure Storage w kontenerach systemu Windows umożliwia określenie **maksymalnie pięciu** punktów instalacji na aplikację.
- Usługa Azure Storage zainstalowana w aplikacji nie jest dostępna za pomocą App Service punktów końcowych FTP/FTPs. Użyj [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
- Usługa Azure Storage jest rozliczana niezależnie i **nie jest uwzględniana** w aplikacji sieci Web. Dowiedz się więcej o [cenach usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Łączenie magazynu z aplikacją internetową (wersja zapoznawcza)

 Aby zainstalować udział Azure Files w katalogu w aplikacji App Service, użyj [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) polecenia. Typ magazynu musi być migracji pamięci.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Należy to zrobić dla wszystkich innych katalogów, które mają być połączone z udziałem Azure Files.

## <a name="verify"></a>Weryfikuj

Gdy udział Azure Files jest połączony z aplikacją sieci Web, możesz to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Następne kroki

- [Migrowanie aplikacji ASP.NET do Azure App Service przy użyciu kontenera systemu Windows (wersja zapoznawcza)](app-service-web-tutorial-windows-containers-custom-fonts.md).
