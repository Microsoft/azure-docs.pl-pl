---
title: Dostępne protokoły Azure Files — NFS i SMB
description: Więcej informacji na temat dostępnych protokołów przed utworzeniem udziału plików platformy Azure, w tym blok komunikatów serwera (SMB) i sieciowy system plików (NFS).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: c4cbbc437b633232ea65dd202b7531a1a5ac2c58
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "96022596"
---
# <a name="azure-file-share-protocols"></a>Protokoły udziałów plików platformy Azure

Azure Files oferuje dwa protokoły do łączenia i instalowania udziałów plików platformy Azure. [Protokół SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) i [sieciowy system plików (NFS)](https://en.wikipedia.org/wiki/Network_File_System) (wersja zapoznawcza). Azure Files nie obsługuje obecnie dostępu do protokołu wieloprotokołowego, więc udział może być tylko udziałem NFS lub udziałem SMB. Z tego powodu zalecamy określenie protokołu, który najlepiej odpowiada Twoim potrzebom przed utworzeniem udziałów plików platformy Azure.

## <a name="differences-at-a-glance"></a>Różnice w skrócie

|Cechy  |NFS (wersja zapoznawcza)  |SMB  |
|---------|---------|---------|
|Protokoły dostępu     |SYSTEM PLIKÓW NFS 4,1         |SMB 2,1, SMB 3,0         |
|Obsługiwane systemy operacyjne     |Jądro systemu Linux w wersji 4.3 +         |Windows 2008 R2 +, jądro systemu Linux w wersji 4.11 +         |
|[Dostępne warstwy](storage-files-planning.md#storage-tiers)     |Premium Storage         |Usługa Premium Storage, zoptymalizowana pod kątem transakcji, gorąca, chłodna         |
|[Nadmiarowość](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Authentication     |Tylko uwierzytelnianie oparte na hoście        |Uwierzytelnianie oparte na tożsamościach, uwierzytelnianie na podstawie użytkownika         |
|Uprawnienia     |Uprawnienia w stylu systemu UNIX         |Uprawnienia w stylu systemu plików NTFS         |
|Semantyka systemu plików     |Zgodne ze standardem POSIX         |Nie jest zgodny ze standardem POSIX         |
|Rozróżnianie wielkości liter     |Uwzględnianie wielkości liter         |Bez uwzględniania wielkości liter         |
|Obsługa linków twardych     |Obsługiwane         |Nieobsługiwane         |
|Obsługa linków symbolicznych     |Obsługiwane         |Nieobsługiwane         |
|Usuwanie lub modyfikowanie otwartych plików     |Obsługiwane         |Nieobsługiwane         |
|Blokowanie     |Poradnik dotyczący zakresu bajtów dyrektor blokady sieci         |Obsługiwane         |
|Publiczna Lista bezpiecznych adresów IP | Nieobsługiwane | Obsługiwane|
|Współdziałanie protokołu| Nieobsługiwane | FileREST|

## <a name="nfs-shares-preview"></a>Udziały NFS (wersja zapoznawcza)

Instalowanie udziałów plików platformy Azure w systemie plików NFS 4,1 jest obecnie w wersji zapoznawczej. Oferuje ściślejszą integrację z systemem Linux. Jest to w pełni zgodna z systemem POSIX oferta, która jest standardem dla różnych wariantów systemów operacyjnych UNIX i innych * Nix. Ta usługa magazynu plików klasy korporacyjnej skaluje się w celu spełnienia wymagań dotyczących magazynu i jest dostępna jednocześnie przez tysiące wystąpień obliczeniowych.

### <a name="limitations"></a>Ograniczenia

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Dostępność regionalna

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Najlepiej dopasowane

System plików NFS z Azure Files jest idealnym rozwiązaniem dla:

- Obciążenia wymagające udziałów plików zgodnych ze standardem POSIX, czułość wielkości liter lub uprawnienia do stylu systemu UNIX (UID/GID).
- Obciążenia skoncentrowane na systemie Linux, które nie wymagają dostępu do systemu Windows.

### <a name="security"></a>Zabezpieczenia

Wszystkie dane Azure Files są szyfrowane w stanie spoczynku. W celu szyfrowania podczas przesyłania, platforma Azure udostępnia warstwę szyfrowania dla wszystkich danych przesyłanych między centrami datasites za pomocą [MACsec](https://en.wikipedia.org/wiki/IEEE_802.1AE). W tym przypadku szyfrowanie istnieje, gdy dane są przesyłane między centrami danych platformy Azure. W przeciwieństwie do Azure Files przy użyciu protokołu SMB udziały plików korzystające z protokołu NFS nie oferują uwierzytelniania opartego na użytkownikach. Uwierzytelnianie dla udziałów NFS odbywa się na podstawie skonfigurowanych reguł zabezpieczeń sieciowych. W związku z tym, aby zapewnić, że tylko bezpieczne połączenia są nawiązywane z udziałem NFS, należy użyć punktów końcowych usługi lub prywatnych punktów końcowych. Jeśli chcesz uzyskać dostęp do udziałów z zasobów lokalnych, oprócz prywatnego punktu końcowego, musisz skonfigurować sieć VPN lub ExpressRoute. Żądania, które nie pochodzą z następujących źródeł, zostaną odrzucone:

- [Prywatny punkt końcowy](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [Sieć VPN typu punkt-lokacja (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [Lokacja-lokacja](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Publiczny punkt końcowy z ograniczeniami](storage-files-networking-overview.md#storage-account-firewall-settings)

Aby uzyskać więcej informacji na temat dostępnych opcji sieciowych, zobacz [Azure Files zagadnienia dotyczące sieci](storage-files-networking-overview.md).

## <a name="smb-shares"></a>Udziały SMB

Udziały plików platformy Azure zainstalowane z usługą SMB oferują więcej Azure Files funkcji i nie mają ograniczeń dotyczących funkcji Azure Files, ponieważ są one ogólnie dostępne.

### <a name="features"></a>Funkcje

- Usługa Azure File Sync
- Uwierzytelnianie oparte na tożsamościach
- Obsługa Azure Backup
- Migawki
- Usuwanie nietrwałe
- Szyfrowanie w trakcie przesyłania i szyfrowanie — w czasie spoczynku

### <a name="best-suited"></a>Najlepiej dopasowane

Protokół SMB z Azure Files jest idealny dla:

- Środowiska produkcyjne
- Klienci, którzy wymagają żadnych funkcji wymienionych w [funkcjach](#features)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie udziału plików NFS](storage-files-how-to-create-nfs-shares.md)
- [Tworzenie udziału plików SMB](storage-how-to-create-file-share.md)
