---
title: Obsługa systemu plików NFS 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza) | Microsoft Docs
description: Magazyn obiektów BLOB obsługuje teraz Protokół 3,0 systemu plików NFS. Ta obsługa umożliwia klientom z systemami Windows i Linux Instalowanie kontenera w usłudze BLOB Storage z maszyny wirtualnej platformy Azure lub z komputera działającego lokalnie.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: c48a7f4b5b1512ec5bcd289e4fb8bd7884ca7394
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922561"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Obsługa protokołu sieciowego systemu plików (NFS) 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza)

Magazyn obiektów BLOB obsługuje teraz Protokół 3,0 systemu plików NFS. Ta obsługa umożliwia klientom z systemem Windows lub Linux zainstalowanie kontenera w usłudze BLOB Storage z maszyny wirtualnej platformy Azure lub lokalnego komputera. 

> [!NOTE]
> Obsługa protokołu NFS 3,0 w usłudze Azure Blob Storage jest w publicznej wersji zapoznawczej. Obsługuje ona konta magazynu GPV2 z wydajnością warstwy Standardowa w następujących regionach: Australia Wschodnia, Korea Środkowa i Południowo-środkowe stany USA. Wersja zapoznawcza obsługuje również blokowy obiekt BLOB z warstwą wydajności Premium we wszystkich regionach publicznych.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Ogólny przepływ pracy: Instalowanie kontenera konta magazynu

Aby zainstalować kontener konta magazynu, należy wykonać te czynności.

1. Zarejestruj funkcję protokołu NFS 3,0 w ramach subskrypcji.

2. Sprawdź, czy funkcja jest zarejestrowana.

3. Utwórz Virtual Network platformy Azure (VNet).

4. Skonfiguruj zabezpieczenia sieciowe.

5. Utwórz i skonfiguruj konto magazynu akceptujące tylko ruch z sieci wirtualnej.

6. Utwórz kontener na koncie magazynu.

7. Zainstaluj kontener.

Aby uzyskać wskazówki krok po kroku, zobacz [Instalowanie usługi BLOB Storage przy użyciu protokołu NFS (Network File System) 3,0 (wersja zapoznawcza)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Ważne jest, aby wykonać te zadania w kolejności. Nie można zainstalować kontenerów utworzonych przed włączeniem protokołu NFS 3,0 na Twoim koncie. Ponadto po włączeniu protokołu NFS 3,0 na Twoim koncie nie można go wyłączyć.

## <a name="network-security"></a>Bezpieczeństwo sieci

Konto magazynu musi być zawarte w sieci wirtualnej. Sieć wirtualna umożliwia klientom bezpieczne łączenie się z kontem magazynu. Jedynym sposobem zabezpieczenia danych na koncie jest użycie sieci wirtualnej i innych ustawień zabezpieczeń sieci. Wszystkie inne narzędzia służące do zabezpieczania danych, w tym autoryzacja klucza konta, zabezpieczenia Azure Active Directory (AD) i listy kontroli dostępu (ACL) nie są jeszcze obsługiwane na kontach, na których włączono obsługę protokołu NFS 3,0. 

Aby dowiedzieć się więcej, zobacz [zalecenia dotyczące zabezpieczeń sieci dla usługi BLOB Storage](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Obsługiwane połączenia sieciowe

Klient może połączyć się za pośrednictwem publicznego lub [prywatnego punktu końcowego](../common/storage-private-endpoints.md)i może nawiązać połączenie z dowolnej z następujących lokalizacji sieciowych:

- Sieć wirtualna skonfigurowana dla konta magazynu. 

  Na potrzeby tego artykułu odwołujemy się do tej sieci wirtualnej jako podstawowej sieci *wirtualnej*. Aby dowiedzieć się więcej, zobacz [udzielanie dostępu z sieci wirtualnej](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Równorzędna Sieć wirtualna znajdująca się w tym samym regionie co podstawowa Sieć wirtualna.

  Musisz skonfigurować konto magazynu, aby zezwolić na dostęp do tej równorzędnej sieci wirtualnej. Aby dowiedzieć się więcej, zobacz [udzielanie dostępu z sieci wirtualnej](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Sieć lokalna, która jest połączona z podstawową siecią wirtualną, przy użyciu [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [bramy ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Aby dowiedzieć się więcej, zobacz [Konfigurowanie dostępu z sieci lokalnych](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Sieć lokalna, która jest połączona z siecią równorzędną.

  Można to zrobić za pomocą [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [bramy ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) wraz z [tranzytem bramy](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> W przypadku łączenia się z siecią lokalną upewnij się, że klient zezwala na komunikację wychodzącą za pośrednictwem portów 111 i 2048. Protokół NFS 3,0 używa tych portów.

## <a name="azure-storage-features-not-yet-supported"></a>Funkcje usługi Azure Storage nie są jeszcze obsługiwane

Następujące funkcje usługi Azure Storage nie są obsługiwane po włączeniu na koncie protokołu NFS 3,0. 

- Zabezpieczenia Azure Active Directory (AD)

- Takie jak listy kontroli dostępu (ACL) na POSIX

- Możliwość włączenia obsługi systemu plików NFS 3,0 na istniejących kontach magazynu

- Możliwość wyłączenia obsługi systemu plików NFS 3,0 na koncie magazynu (po jej włączeniu)

- Możliwość zapisu w obiektach Blob przy użyciu interfejsów API REST lub zestawów SDK. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Funkcje systemu plików NFS 3,0 nie są jeszcze obsługiwane

Następujące funkcje systemu plików NFS 3,0 nie są jeszcze obsługiwane w Azure Data Lake Storage Gen2.

- NFS 3,0 za pośrednictwem protokołu UDP. Obsługiwany jest tylko system plików NFS 3,0 przez TCP.

- Blokowanie plików przy użyciu Menedżera blokad sieciowych (NLM). Polecenia instalacji muszą zawierać `-o nolock` parametr.

- Instalowanie katalogów podrzędnych. Można zainstalować tylko katalog główny (kontener).

- Wyświetlanie listy instalacji (na przykład przy użyciu polecenia `showmount -a` )

- Lista eksportów (na przykład: przy użyciu polecenia `showmount -e` )

- Eksportowanie kontenera jako tylko do odczytu

## <a name="pricing"></a>Cennik

W trakcie okresu zapoznawczego dane przechowywane na koncie magazynu są rozliczane według tej samej stawki wydajności, która obciąża magazyn obiektów BLOB za GB miesięcznie. 

Transakcja nie jest naliczana w ramach wersji zapoznawczej. Cennik transakcji może ulec zmianie i zostanie określony, gdy jest ona ogólnie dostępna.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz [Instalowanie usługi BLOB Storage przy użyciu protokołu NFS (Network File System) 3,0 (wersja zapoznawcza)](network-file-system-protocol-support-how-to.md).
