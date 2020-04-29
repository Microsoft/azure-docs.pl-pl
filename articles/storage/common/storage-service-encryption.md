---
title: Szyfrowanie usługi Azure Storage dla danych magazynowanych
description: Usługa Azure Storage chroni dane, automatycznie szyfrując je przed ich utrwaleniem do chmury. Możesz polegać na kluczach zarządzanych przez firmę Microsoft, aby szyfrować dane na koncie magazynu, lub możesz zarządzać szyfrowaniem przy użyciu własnych kluczy.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c737ccf83dae0cc4b198b9cd708a55b988e6593b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457947"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych magazynowanych

Usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Szyfrowanie usługi Azure Storage chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

## <a name="about-azure-storage-encryption"></a>Informacje o szyfrowaniu usługi Azure Storage

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu, w tym kont magazynu Menedżer zasobów i klasycznych. Nie można wyłączyć szyfrowania usługi Azure Storage. Ponieważ dane są zabezpieczone domyślnie, nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania usługi Azure Storage.

Dane na koncie magazynu są szyfrowane niezależnie od warstwy wydajności (standardowa lub Premium), warstwy dostępu (gorąca lub chłodna) lub modelu wdrażania (Azure Resource Manager lub klasycznego). Wszystkie obiekty blob w warstwie archiwum również są szyfrowane. Wszystkie opcje nadmiarowości usługi Azure Storage obsługują szyfrowanie, a wszystkie dane w regionach podstawowym i pomocniczym są szyfrowane po włączeniu replikacji geograficznej. Wszystkie zasoby usługi Azure Storage są szyfrowane, w tym obiektów blob, dysków, plików, kolejek i tabel. Wszystkie metadane obiektu są również szyfrowane. Nie ma dodatkowych opłat za szyfrowanie usługi Azure Storage.

Każdy blokowy obiekt BLOB, dołączany obiekt BLOB lub stronicowy obiekt BLOB, który został zapisany w usłudze Azure Storage po 20 października 2017, jest szyfrowany. Obiekty blob utworzone przed tą datą nadal są szyfrowane przez proces w tle. Aby wymusić szyfrowanie obiektu BLOB, który został utworzony przed 20 października 2017, można ponownie napisać obiekt BLOB. Aby dowiedzieć się, jak sprawdzić stan szyfrowania obiektu BLOB, zobacz [Sprawdzanie stanu szyfrowania obiektu BLOB](../blobs/storage-blob-encryption-status.md).

Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z szyfrowaniem usługi Azure Storage, zobacz [interfejs API kryptografii: Kolejna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Dane na nowym koncie magazynu są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania danych. Możesz też zarządzać szyfrowaniem przy użyciu własnych kluczy. W przypadku wybrania opcji zarządzania szyfrowaniem przy użyciu własnych kluczy dostępne są dwie opcje:

- *Klucz zarządzany przez klienta* można określić przy użyciu Azure Key Vault do szyfrowania i odszyfrowywania danych w usłudze BLOB Storage i w Azure Files. <sup>1, 2</sup> Aby uzyskać więcej informacji o kluczach zarządzanych przez klienta, zobacz [Korzystanie z kluczy zarządzanych przez klienta w usłudze Azure Key Vault do zarządzania szyfrowaniem usługi Azure Storage](encryption-customer-managed-keys.md).
- *Klucz dostarczony przez klienta* można określić w operacjach magazynu obiektów BLOB. Klient wykonujący żądanie odczytu lub zapisu w usłudze BLOB Storage może dołączyć klucz szyfrowania żądania, aby uzyskać szczegółową kontrolę nad sposobem szyfrowania i odszyfrowywania danych obiektów BLOB. Więcej informacji o kluczach dostarczonych przez klienta znajduje się [w temacie zapewnianie klucza szyfrowania w żądaniu usługi BLOB Storage (wersja zapoznawcza)](encryption-customer-provided-keys.md).

Poniższa tabela zawiera porównanie opcji zarządzania kluczami dla szyfrowania usługi Azure Storage.

|                                        |    Klucze zarządzane przez firmę Microsoft                             |    Klucze zarządzane przez klienta                                                                                                                        |    Klucze dostarczone przez klienta                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operacje szyfrowania/odszyfrowywania    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Obsługiwane usługi Azure Storage    |    Wszystkie                                                |    BLOB Storage, Azure Files<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Magazyn kluczy                         |    Magazyn kluczy firmy Microsoft    |    W usłudze Azure Key Vault                                                                                                                              |    Własny magazyn kluczy klienta                                                                 |
|    Odpowiedzialność za kluczowe rotacje         |    Microsoft                                          |    Klient                                                                                                                                     |    Klient                                                                      |
|    Klucz — formant                          |    Microsoft                                     |    Klient                                                                                                                    |    Klient                                                                 |

<sup>1</sup> Aby uzyskać informacje na temat tworzenia konta obsługującego Używanie kluczy zarządzanych przez klienta z usługą queue storage, zobacz [Tworzenie konta, które obsługuje klucze zarządzane przez klienta dla kolejek](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Aby uzyskać informacje na temat tworzenia konta, które obsługuje używanie kluczy zarządzanych przez klienta w usłudze Table Storage, zobacz [Tworzenie konta, które obsługuje klucze zarządzane przez klienta dla tabel](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)
- [Configure customer-managed keys for Azure Storage encryption from the Azure portal (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage w witrynie Azure Portal)](storage-encryption-keys-portal.md)
- [Configure customer-managed keys for Azure Storage encryption from PowerShell (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą programu PowerShell)](storage-encryption-keys-powershell.md)
- [Configure customer-managed keys for Azure Storage encryption from Azure CLI (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą interfejsu wiersza polecenia platformy Azure)](storage-encryption-keys-cli.md)
