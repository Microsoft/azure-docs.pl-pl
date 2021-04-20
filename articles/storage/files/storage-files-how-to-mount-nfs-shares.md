---
title: Zainstaluj udział plików NFS platformy Azure — Azure Files
description: Dowiedz się, jak zainstalować udział sieciowego systemu plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4369619cd83dffe36cf156f523a951e1360438db
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717081"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Jak zainstalować udział plików NFS

[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure mogą być instalowane w dystrybucjach systemu Linux przy użyciu protokołu SMB (Server Message Block) lub protokołu NFS (Network File System). Ten artykuł koncentruje się na instalowanie za pomocą systemu plików NFS. Aby uzyskać szczegółowe informacje na temat instalowanie przy użyciu funkcji SMB, zobacz Używanie systemu [Azure Files z systemem Linux.](storage-how-to-use-files-linux.md) Aby uzyskać szczegółowe informacje na temat każdego z dostępnych protokołów, zobacz [Azure file share protocols (Protokoły udziałów](storage-files-compare-protocols.md)plików platformy Azure).

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Dostępność w regionach

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz udział NFS.](storage-files-how-to-create-nfs-shares.md)

    > [!IMPORTANT]
    > Dostęp do udziałów NFS można uzyskać tylko z zaufanych sieci. Połączenia z udziałem NFS muszą pochodzić z jednego z następujących źródeł:

- Użyj jednego z następujących rozwiązań sieciowych:
    - Utwórz [prywatny punkt końcowy](storage-files-networking-endpoints.md#create-a-private-endpoint) (zalecane) lub ogranicz dostęp do publicznego punktu [końcowego.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Skonfiguruj sieć VPN typu punkt-lokacja (P2S)](storage-files-configure-p2s-vpn-linux.md)w systemie Linux do użycia z Azure Files .
    - [Skonfiguruj sieć VPN typu lokacja-lokacja do użycia z Azure Files](storage-files-configure-s2s-vpn.md).
    - Skonfiguruj [usługę ExpressRoute.](../../expressroute/expressroute-introduction.md)

## <a name="disable-secure-transfer"></a>Wyłączanie bezpiecznego transferu

1. Zaloguj się do konta Azure Portal i uzyskaj dostęp do konta magazynu zawierającego utworzony udział NFS.
1. Wybierz pozycję **Konfiguracja**.
1. Dla **opcji Wymagany** bezpieczny transfer wybierz pozycję **Wyłączone.**
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Zrzut ekranu konfiguracji konta magazynu z wyłączonym bezpiecznym transferem.":::

## <a name="mount-an-nfs-share"></a>Zainstaluj udział NFS

1. Po utworzeniu udziału plików wybierz udział i wybierz pozycję **Połącz z systemu Linux.**
1. Wprowadź ścieżkę instalacji, którą chcesz użyć, a następnie skopiuj skrypt.
1. Połącz się z klientem i użyj dostarczonego skryptu montowania.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Zrzut ekranu przedstawiający blok połączenia udziału plików.":::

Udział NFS został zainstalowany.

### <a name="validate-connectivity"></a>Weryfikowanie łączności

Jeśli instalacja nie powiodła się, istnieje możliwość, że prywatny punkt końcowy nie został poprawnie skonfigurowany lub jest niedostępny. Aby uzyskać szczegółowe informacje na temat potwierdzania łączności, zobacz [sekcję Verify connectivity (Weryfikowanie](storage-files-networking-endpoints.md#verify-connectivity) łączności) artykułu networking endpoints (Punkty końcowe sieci).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej Azure Files z naszego artykułu Planowanie [wdrożenia Azure Files wdrożenia.](storage-files-planning.md)
- Jeśli wystąpią jakiekolwiek problemy, zobacz [Troubleshoot Azure NFS file shares (Rozwiązywanie problemów z udziałami plików NFS platformy Azure).](storage-troubleshooting-files-nfs.md)