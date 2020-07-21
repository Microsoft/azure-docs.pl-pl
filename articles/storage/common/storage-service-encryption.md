---
title: Szyfrowanie w usłudze Azure Storage dla danych magazynowanych
description: Usługa Azure Storage chroni dane, automatycznie szyfrując je przed ich utrwaleniem do chmury. Możesz polegać na kluczach zarządzanych przez firmę Microsoft, aby szyfrować dane na koncie magazynu, lub możesz zarządzać szyfrowaniem przy użyciu własnych kluczy.
services: storage
author: tamram
ms.service: storage
ms.date: 07/16/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b6244b3ab72f7fa8ea375ff67a08e8d1d241df4a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527901"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Szyfrowanie w usłudze Azure Storage dla danych magazynowanych

Usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Szyfrowanie usługi Azure Storage chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

## <a name="about-azure-storage-encryption"></a>Informacje o szyfrowaniu usługi Azure Storage

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu, w tym kont magazynu Menedżer zasobów i klasycznych. Nie można wyłączyć szyfrowania usługi Azure Storage. Ponieważ dane są zabezpieczone domyślnie, nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania usługi Azure Storage.

Dane na koncie magazynu są szyfrowane niezależnie od warstwy wydajności (standardowa lub Premium), warstwy dostępu (gorąca lub chłodna) lub modelu wdrażania (Azure Resource Manager lub klasycznego). Wszystkie obiekty blob w warstwie archiwum również są szyfrowane. Wszystkie opcje nadmiarowości usługi Azure Storage obsługują szyfrowanie, a wszystkie dane w regionach podstawowym i pomocniczym są szyfrowane po włączeniu replikacji geograficznej. Wszystkie zasoby usługi Azure Storage są szyfrowane, w tym obiektów blob, dysków, plików, kolejek i tabel. Wszystkie metadane obiektu są również szyfrowane. Nie ma dodatkowych opłat za szyfrowanie usługi Azure Storage.

Każdy blokowy obiekt BLOB, dołączany obiekt BLOB lub stronicowy obiekt BLOB, który został zapisany w usłudze Azure Storage po 20 października 2017, jest szyfrowany. Obiekty blob utworzone przed tą datą nadal są szyfrowane przez proces w tle. Aby wymusić szyfrowanie obiektu BLOB, który został utworzony przed 20 października 2017, można ponownie napisać obiekt BLOB. Aby dowiedzieć się, jak sprawdzić stan szyfrowania obiektu BLOB, zobacz [Sprawdzanie stanu szyfrowania obiektu BLOB](../blobs/storage-blob-encryption-status.md).

Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z szyfrowaniem usługi Azure Storage, zobacz [interfejs API kryptografii: Kolejna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

Aby uzyskać informacje na temat szyfrowania i zarządzania kluczami dla usługi Azure Managed disks, zobacz [szyfrowanie po stronie serwera usługi Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) dla maszyn wirtualnych z systemem Windows lub [szyfrowanie po stronie serwera](../../virtual-machines/linux/disk-encryption.md) na potrzeby maszyn wirtualnych z systemem Linux.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Dane na nowym koncie magazynu są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania danych. Możesz też zarządzać szyfrowaniem przy użyciu własnych kluczy. W przypadku wybrania opcji zarządzania szyfrowaniem przy użyciu własnych kluczy dostępne są dwie opcje:

- *Klucz zarządzany przez klienta* można określić przy użyciu Azure Key Vault do szyfrowania i odszyfrowywania danych w usłudze BLOB Storage i w Azure Files. <sup>1, 2</sup> Aby uzyskać więcej informacji o kluczach zarządzanych przez klienta, zobacz [Korzystanie z kluczy zarządzanych przez klienta w usłudze Azure Key Vault do zarządzania szyfrowaniem usługi Azure Storage](encryption-customer-managed-keys.md).
- *Klucz dostarczony przez klienta* można określić w operacjach magazynu obiektów BLOB. Klient wykonujący żądanie odczytu lub zapisu w usłudze BLOB Storage może dołączyć klucz szyfrowania żądania, aby uzyskać szczegółową kontrolę nad sposobem szyfrowania i odszyfrowywania danych obiektów BLOB. Więcej informacji o kluczach dostarczonych przez klienta znajduje się [w temacie zapewnianie klucza szyfrowania w żądaniu usługi BLOB Storage (wersja zapoznawcza)](encryption-customer-provided-keys.md).

Poniższa tabela zawiera porównanie opcji zarządzania kluczami dla szyfrowania usługi Azure Storage.

| Parametr zarządzania kluczami | Klucze zarządzane przez firmę Microsoft | Klucze zarządzane przez klienta | Klucze dostarczone przez klienta |
|--|--|--|--|
| Operacje szyfrowania/odszyfrowywania | Azure | Azure | Azure |
| Obsługiwane usługi Azure Storage | Wszystko | BLOB Storage, Azure Files<sup>1, 2</sup> | Blob Storage |
| Magazyn kluczy | Magazyn kluczy firmy Microsoft | Azure Key Vault | Własny magazyn kluczy klienta |
| Odpowiedzialność za kluczowe rotacje | Microsoft | Klient | Klient |
| Klucz — formant | Microsoft | Klient | Klient |

<sup>1</sup> Aby uzyskać informacje na temat tworzenia konta obsługującego Używanie kluczy zarządzanych przez klienta z usługą queue storage, zobacz [Tworzenie konta, które obsługuje klucze zarządzane przez klienta dla kolejek](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Aby uzyskać informacje na temat tworzenia konta, które obsługuje używanie kluczy zarządzanych przez klienta w usłudze Table Storage, zobacz [Tworzenie konta, które obsługuje klucze zarządzane przez klienta dla tabel](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="encryption-scopes-for-blob-storage-preview"></a>Zakresy szyfrowania dla usługi BLOB Storage (wersja zapoznawcza)

Domyślnie konto magazynu jest szyfrowane przy użyciu klucza, który jest objęty zakresem konta magazynu. Możesz użyć kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta przechowywanych w Azure Key Vault, aby chronić i kontrolować dostęp do klucza, który szyfruje dane.

Zakresy szyfrowania umożliwiają opcjonalne zarządzanie szyfrowaniem na poziomie kontenera lub pojedynczym obiektem BLOB. Zakresy szyfrowania można używać do tworzenia bezpiecznych granic między danymi znajdującymi się na tym samym koncie magazynu, ale należą do różnych klientów.

Można utworzyć co najmniej jeden zakres szyfrowania dla konta magazynu przy użyciu dostawcy zasobów usługi Azure Storage. Podczas tworzenia zakresu szyfrowania należy określić, czy zakres jest chroniony za pomocą klucza zarządzanego przez firmę Microsoft, czy z kluczem zarządzanym przez klienta, który jest przechowywany w Azure Key Vault. Różne zakresy szyfrowania na tym samym koncie magazynu mogą korzystać z kluczy zarządzanych przez firmę Microsoft lub przez klienta.

Po utworzeniu zakresu szyfrowania możesz określić ten zakres szyfrowania dla żądania, aby utworzyć kontener lub obiekt BLOB. Aby uzyskać więcej informacji na temat sposobu tworzenia zakresu szyfrowania, zobacz [Tworzenie zakresów szyfrowania i zarządzanie nimi (wersja zapoznawcza)](../blobs/encryption-scope-manage.md).

> [!NOTE]
> Zakresy szyfrowania nie są obsługiwane z kontami magazynu geograficznie nadmiarowego do odczytu (RA-GRS) w trakcie okresu zapoznawczego.

> [!IMPORTANT]
> Wersja zapoznawcza zakresów szyfrowania jest przeznaczona wyłącznie do użycia w trybie nieprodukcyjnym. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.
>
> Aby uniknąć nieoczekiwanych kosztów, należy wyłączyć wszelkie zakresy szyfrowania, które nie są obecnie potrzebne.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Tworzenie kontenera lub obiektu BLOB z zakresem szyfrowania

Obiekty blob tworzone w ramach zakresu szyfrowania są szyfrowane przy użyciu klucza określonego dla tego zakresu. Możesz określić zakres szyfrowania dla pojedynczego obiektu BLOB podczas tworzenia obiektu BLOB lub określić domyślny zakres szyfrowania podczas tworzenia kontenera. Po określeniu domyślnego zakresu szyfrowania na poziomie kontenera wszystkie obiekty blob w tym kontenerze są szyfrowane przy użyciu klucza skojarzonego z zakresem domyślnym.

Podczas tworzenia obiektu BLOB w kontenerze z domyślnym zakresem szyfrowania można określić zakres szyfrowania, który zastąpi domyślny zakres szyfrowania, jeśli kontener jest skonfigurowany tak, aby zezwalał na przesłonięcia domyślnego zakresu szyfrowania. Aby zapobiec zastępowaniu domyślnego zakresu szyfrowania, należy skonfigurować kontener tak, aby odmówił zastąpień dla pojedynczego obiektu BLOB.

Operacje odczytu na obiekcie blob, który należy do zakresu szyfrowania, są w sposób przezroczysty, tak długo, jak zakres szyfrowania nie jest wyłączony.

### <a name="disable-an-encryption-scope"></a>Wyłącz zakres szyfrowania

Po wyłączeniu zakresu szyfrowania wszelkie kolejne operacje odczytu lub zapisu wykonane z zakresem szyfrowania zakończą się niepowodzeniem z kodem błędu HTTP 403 (dostęp zabroniony). Po ponownym włączeniu zakresu szyfrowania operacje odczytu i zapisu będą normalnie powtarzane.

Po wyłączeniu zakresu szyfrowania nie są już naliczane opłaty. Wyłącz wszelkie zakresy szyfrowania, które nie są potrzebne, aby uniknąć niepotrzebnych opłat.

Jeśli zakres szyfrowania jest chroniony przy użyciu kluczy zarządzanych przez klienta Azure Key Vault, można również usunąć skojarzony klucz w magazynie kluczy, aby wyłączyć zakres szyfrowania. Należy pamiętać, że klucze zarządzane przez klienta w Azure Key Vault są chronione przez programowe usuwanie i przeczyszczanie ochrony, a usunięty klucz podlega zachowaniu zdefiniowanym przez te właściwości. Aby uzyskać więcej informacji, zobacz jeden z następujących tematów w dokumentacji Azure Key Vault:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/soft-delete-powershell.md)
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> Nie można usunąć zakresu szyfrowania.

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)
- [Configure customer-managed keys for Azure Storage encryption from the Azure portal (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage w witrynie Azure Portal)](storage-encryption-keys-portal.md)
- [Configure customer-managed keys for Azure Storage encryption from PowerShell (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą programu PowerShell)](storage-encryption-keys-powershell.md)
- [Configure customer-managed keys for Azure Storage encryption from Azure CLI (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą interfejsu wiersza polecenia platformy Azure)](storage-encryption-keys-cli.md)
