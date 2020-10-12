---
title: Instalowanie udziału plików NFS systemu Azure — Azure Files
description: Dowiedz się, jak zainstalować udział systemu plików NFS.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 530ae82720e6b4eb6a3e4d1021c0b37b9f4dbf5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707445"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Jak zainstalować udział plików NFS

[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu protokołu bloku komunikatów serwera (SMB) lub protokołu sieciowego systemu plików (NFS). Ten artykuł koncentruje się na instalowaniu w systemie plików NFS, aby uzyskać szczegółowe informacje na temat instalowania przy użyciu protokołu SMB, zobacz [używanie Azure Files z systemem Linux](storage-how-to-use-files-linux.md). Aby uzyskać szczegółowe informacje na temat każdego z dostępnych protokołów, zobacz [Protokoły udziałów plików platformy Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Dostępność regionalna

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz udział NFS](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > Dostęp do udziałów NFS można uzyskać tylko z zaufanych sieci. Połączenia z udziałem NFS muszą pochodzić z jednego z następujących źródeł:

- Użyj jednego z następujących rozwiązań sieciowych:
    - [Utwórz prywatny punkt końcowy](storage-files-networking-endpoints.md#create-a-private-endpoint) (zalecane) lub [Ogranicz dostęp do publicznego punktu końcowego](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Skonfiguruj sieć VPN typu lokacja-lokacja do użycia z Azure Files](storage-files-configure-s2s-vpn.md).
    - Skonfiguruj [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Wyłącz bezpieczny transfer

1. Zaloguj się do Azure Portal i uzyskaj dostęp do konta magazynu zawierającego utworzony udział NFS.
1. Wybierz pozycję **Konfiguracja**.
1. Wybierz pozycję **wyłączone** na potrzeby **bezpiecznego transferu**.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Zrzut ekranu przedstawiający ekran Konfiguracja konta magazynu z wyłączonym bezpiecznym transferem.":::

## <a name="mount-an-nfs-share"></a>Instalowanie udziału NFS

1. Po utworzeniu udziału plików wybierz udział i wybierz pozycję **Połącz z systemu Linux**.
1. Wprowadź ścieżkę instalacji, której chcesz użyć, a następnie skopiuj skrypt.
1. Połącz się z klientem i użyj dostarczonego skryptu instalacji.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Zrzut ekranu przedstawiający ekran Konfiguracja konta magazynu z wyłączonym bezpiecznym transferem.":::

Udział w systemie plików NFS został zainstalowany.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat Azure Files z naszym artykułem [Planowanie wdrożenia Azure Files](storage-files-planning.md).
- Jeśli występują jakieś problemy, zobacz [Rozwiązywanie problemów z udziałami plików NFS systemu Azure](storage-troubleshooting-files-nfs.md).