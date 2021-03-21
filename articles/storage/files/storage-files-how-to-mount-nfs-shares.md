---
title: Instalowanie udziału plików NFS systemu Azure — Azure Files
description: Dowiedz się, jak zainstalować udział systemu plików NFS.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621301"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Jak zainstalować udział plików NFS

[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu protokołu bloku komunikatów serwera (SMB) lub protokołu sieciowego systemu plików (NFS). Ten artykuł koncentruje się na instalowaniu w systemie plików NFS, aby uzyskać szczegółowe informacje na temat instalowania przy użyciu protokołu SMB, zobacz [używanie Azure Files z systemem Linux](storage-how-to-use-files-linux.md). Aby uzyskać szczegółowe informacje na temat każdego z dostępnych protokołów, zobacz [Protokoły udziałów plików platformy Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Dostępność w regionach

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

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Zrzut ekranu przedstawiający blok łączenie z udziałem plików":::

Udział w systemie plików NFS został zainstalowany.

### <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Jeśli instalacja nie powiodła się, istnieje możliwość, że prywatny punkt końcowy nie został poprawnie skonfigurowany lub nie jest dostępny. Aby uzyskać szczegółowe informacje na temat potwierdzania łączności, zobacz sekcję [Weryfikowanie łączności](storage-files-networking-endpoints.md#verify-connectivity) w artykule punkty końcowe sieci.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat Azure Files z naszym artykułem [Planowanie wdrożenia Azure Files](storage-files-planning.md).
- Jeśli występują jakieś problemy, zobacz [Rozwiązywanie problemów z udziałami plików NFS systemu Azure](storage-troubleshooting-files-nfs.md).