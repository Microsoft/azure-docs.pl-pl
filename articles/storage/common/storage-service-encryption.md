---
title: Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku
description: Usługa Azure Storage chroni dane, automatycznie szyfrując je przed utrwaleniem ich w chmurze. Szyfrowanie można polegać na kluczach zarządzanych przez firmę Microsoft do szyfrowania danych na koncie magazynu lub zarządzać szyfrowaniem za pomocą własnych kluczy.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c737ccf83dae0cc4b198b9cd708a55b988e6593b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457947"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku

Usługa Azure Storage automatycznie szyfruje dane, gdy są one utrwalone w chmurze. Szyfrowanie usługi Azure Storage chroni dane i pomaga w spełnienie zobowiązań dotyczących zabezpieczeń i zgodności organizacji.

## <a name="about-azure-storage-encryption"></a>Szyfrowanie usługi Azure Storage — informacje

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób przezroczysty przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych dostępnych szyfrów blokowych i zgodnego ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcji BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu, w tym zarówno menedżera zasobów, jak i klasycznych kont magazynu. Nie można wyłączyć szyfrowania usługi Azure Storage. Ponieważ dane są domyślnie zabezpieczone, nie trzeba modyfikować kodu lub aplikacji, aby korzystać z szyfrowania usługi Azure Storage.

Dane na koncie magazynu są szyfrowane niezależnie od warstwy wydajności (standardowej lub premium), warstwy dostępu (gorąca lub chłodna) lub modelu wdrażania (usługi Azure Resource Manager lub classic). Wszystkie obiekty BLOB w warstwie archiwum są również szyfrowane. Wszystkie opcje nadmiarowości usługi Azure Storage obsługują szyfrowanie, a wszystkie dane w regionach podstawowych i pomocniczych są szyfrowane po włączeniu replikacji geograficznej. Wszystkie zasoby usługi Azure Storage są szyfrowane, w tym obiekty BLOB, dyski, pliki, kolejki i tabele. Wszystkie metadane obiektu są również szyfrowane. Szyfrowanie usługi Azure Storage nie wiąże się z żadnymi dodatkowymi kosztami.

Każdy blokowy obiekt blob, dołączanie obiektu blob lub stronicowego obiektu blob, który został napisany w usłudze Azure Storage po 20 października 2017 r., jest szyfrowany. Obiekty BLOB utworzone przed tą datą są nadal szyfrowane przez proces w tle. Aby wymusić szyfrowanie obiektu blob, który został utworzony przed 20 października 2017 r., można przepisać obiekt blob. Aby dowiedzieć się, jak sprawdzić stan szyfrowania obiektu blob, zobacz [Sprawdzanie stanu szyfrowania obiektu blob](../blobs/storage-blob-encryption-status.md).

Aby uzyskać więcej informacji na temat modułów kryptograficznych leżących u podstaw szyfrowania usługi Azure Storage, zobacz [Interfejs API kryptografii: następna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Zarządzanie kluczami szyfrowania — informacje

Dane na nowym koncie magazynu są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Szyfrowanie danych można polegać na kluczach zarządzanych przez firmę Microsoft lub zarządzać szyfrowaniem za pomocą własnych kluczy. Jeśli zdecydujesz się zarządzać szyfrowaniem za pomocą własnych kluczy, masz dwie opcje:

- Klucz zarządzany *przez klienta* za pomocą usługi Azure Key Vault można określić do szyfrowania i odszyfrowywania danych w magazynie obiektów Blob i w usłudze Azure Files. <sup>1,2</sup> Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta, zobacz [Zarządzanie szyfrowaniem usługi Azure Storage za pomocą kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault.](encryption-customer-managed-keys.md)
- Można określić *klucz dostarczony przez klienta* w operacjach magazynu obiektów Blob. Klient składający żądanie odczytu lub zapisu dla magazynu obiektów Blob może dołączyć klucz szyfrowania na żądanie szczegółowej kontroli nad tym, jak dane obiektu blob są szyfrowane i odszyfrowywane. Aby uzyskać więcej informacji na temat kluczy dostarczonych przez klienta, zobacz [Podaj klucz szyfrowania na żądanie do magazynu obiektów Blob (wersja zapoznawcza)](encryption-customer-provided-keys.md).

W poniższej tabeli porównano opcje zarządzania kluczami dla szyfrowania usługi Azure Storage.

|                                        |    Klucze zarządzane przez firmę Microsoft                             |    Klucze zarządzane przez klienta                                                                                                                        |    Klucze dostarczone przez klienta                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operacje szyfrowania/odszyfrowywania    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Obsługiwane usługi Usługi Azure Storage    |    Wszystkie                                                |    Magazyn obiektów blob, pliki platformy Azure<sup>1,2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Przechowywanie kluczy                         |    Magazyn kluczy firmy Microsoft    |    W usłudze Azure Key Vault                                                                                                                              |    Własny sklep z kluczami klienta                                                                 |
|    Kluczowa odpowiedzialność za rotację         |    Microsoft                                          |    Klient                                                                                                                                     |    Klient                                                                      |
|    Sterowanie kluczem                          |    Microsoft                                     |    Klient                                                                                                                    |    Klient                                                                 |

<sup>1</sup> Aby uzyskać informacje dotyczące tworzenia konta obsługującego przy użyciu kluczy zarządzanych przez klienta z magazynem kolejek, zobacz [Tworzenie konta obsługującego klucze zarządzane przez klienta dla kolejek](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Aby uzyskać informacje dotyczące tworzenia konta obsługującego przy użyciu kluczy zarządzanych przez klienta z magazynem tabel, zobacz [Tworzenie konta obsługującego klucze zarządzane przez klienta dla tabel](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)
- [Configure customer-managed keys for Azure Storage encryption from the Azure portal (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage w witrynie Azure Portal)](storage-encryption-keys-portal.md)
- [Configure customer-managed keys for Azure Storage encryption from PowerShell (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą programu PowerShell)](storage-encryption-keys-powershell.md)
- [Configure customer-managed keys for Azure Storage encryption from Azure CLI (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą interfejsu wiersza polecenia platformy Azure)](storage-encryption-keys-cli.md)
