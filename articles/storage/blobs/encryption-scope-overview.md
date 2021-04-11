---
title: Zakresy szyfrowania dla usługi BLOB Storage
description: Zakresy szyfrowania zapewniają możliwość zarządzania szyfrowaniem na poziomie kontenera lub pojedynczym obiektem BLOB. Zakresy szyfrowania można używać do tworzenia bezpiecznych granic między danymi znajdującymi się na tym samym koncie magazynu, ale należą do różnych klientów.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640465"
---
# <a name="encryption-scopes-for-blob-storage"></a>Zakresy szyfrowania dla usługi BLOB Storage

Zakresy szyfrowania umożliwiają zarządzanie szyfrowaniem przy użyciu klucza, który jest objęty zakresem kontenera lub pojedynczym obiektem BLOB. Zakresy szyfrowania można używać do tworzenia bezpiecznych granic między danymi znajdującymi się na tym samym koncie magazynu, ale należą do różnych klientów.

Aby uzyskać więcej informacji na temat pracy z zakresami szyfrowania, zobacz [Tworzenie zakresów szyfrowania i zarządzanie nimi](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Jak działają zakresy szyfrowania

Domyślnie konto magazynu jest szyfrowane przy użyciu klucza, który jest objęty zakresem całego konta magazynu. Podczas definiowania zakresu szyfrowania należy określić klucz, który może być objęty zakresem kontenera lub pojedynczym obiektem BLOB. Gdy zakres szyfrowania jest stosowany do obiektu BLOB, obiekt BLOB jest szyfrowany przy użyciu tego klucza. Gdy zakres szyfrowania jest stosowany do kontenera, pełni rolę domyślnego zakresu dla obiektów BLOB w tym kontenerze, tak aby wszystkie obiekty blob przekazane do tego kontenera mogły być szyfrowane przy użyciu tego samego klucza. Kontener można skonfigurować w celu wymuszenia domyślnego zakresu szyfrowania dla wszystkich obiektów BLOB w kontenerze lub zezwalania na przekazywanie poszczególnych obiektów BLOB do kontenera z zakresem szyfrowania innym niż domyślny.

Operacje odczytu na obiekcie blob, który został utworzony z zakresem szyfrowania, są w sposób przezroczysty, dopóki zakres szyfrowania nie jest wyłączony.

### <a name="key-management"></a>Zarządzanie kluczami

Podczas definiowania zakresu szyfrowania można określić, czy zakres jest chroniony za pomocą klucza zarządzanego przez firmę Microsoft, czy z kluczem zarządzanym przez klienta, który jest przechowywany w Azure Key Vault. Różne zakresy szyfrowania na tym samym koncie magazynu mogą korzystać z kluczy zarządzanych przez firmę Microsoft lub przez klienta. Możesz również przełączyć typ klucza używanego do ochrony zakresu szyfrowania z klucza zarządzanego przez klienta do klucza zarządzanego przez firmę Microsoft lub odwrotnie, w dowolnym momencie. Aby uzyskać więcej informacji o kluczach zarządzanych przez klienta, zobacz [klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](../common/customer-managed-keys-overview.md). Aby uzyskać więcej informacji na temat kluczy zarządzanych przez firmę Microsoft, zobacz [Informacje o zarządzaniu kluczami szyfrowania](../common/storage-service-encryption.md#about-encryption-key-management).

Jeśli zdefiniujesz zakres szyfrowania z kluczem zarządzanym przez klienta, możesz wybrać opcję automatycznej aktualizacji wersji klucza lub ręcznie. W przypadku wybrania opcji automatycznej aktualizacji wersji klucza usługa Azure Storage sprawdza Magazyn kluczy lub moduł modułu HSM, który jest codziennie przeznaczony dla nowej wersji klucza zarządzanego przez klienta i automatycznie aktualizuje klucz do najnowszej wersji. Aby uzyskać więcej informacji na temat aktualizowania wersji klucza dla klucza zarządzanego przez klienta, zobacz [Aktualizacja wersji klucza](../common/customer-managed-keys-overview.md#update-the-key-version).

Konto magazynu może mieć do 10 000 zakresów szyfrowania, które są chronione za pomocą kluczy zarządzanych przez klienta, dla których zostanie automatycznie zaktualizowana wersja klucza. Jeśli konto magazynu ma już 10 000 zakresy szyfrowania chronione przez klienta, które są automatycznie aktualizowane, należy ręcznie zaktualizować wersję klucza dla wszelkich dodatkowych zakresów szyfrowania chronionych przez klienta.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Zakresy szyfrowania dla kontenerów i obiektów BLOB

Podczas tworzenia kontenera można określić domyślny zakres szyfrowania dla obiektów blob, które są następnie przekazywane do tego kontenera. W przypadku określenia domyślnego zakresu szyfrowania dla kontenera można zdecydować, jak ma być wymuszany domyślny zakres szyfrowania:

- Można wymagać, aby wszystkie obiekty blob przekazane do kontenera używały domyślnego zakresu szyfrowania. W takim przypadku każdy obiekt BLOB w kontenerze jest szyfrowany przy użyciu tego samego klucza.
- Można zezwolić klientowi na zastąpienie domyślnego zakresu szyfrowania dla kontenera, aby obiekt BLOB mógł zostać przekazany z zakresem szyfrowania innym niż zakres domyślny. W takim przypadku obiekty blob w kontenerze mogą być szyfrowane przy użyciu różnych kluczy.

W poniższej tabeli zestawiono zachowanie operacji przekazywania obiektów blob, w zależności od tego, jak domyślny zakres szyfrowania został skonfigurowany dla kontenera:

| Zakres szyfrowania zdefiniowany w kontenerze to... | Trwa przekazywanie obiektu BLOB z domyślnym zakresem szyfrowania... | Przekazywanie obiektu BLOB z zakresem szyfrowania innym niż zakres domyślny... |
|--|--|--|
| Domyślny zakres szyfrowania z dozwolonymi zastąpieniami | Powiedzie się | Powiedzie się |
| Domyślny zakres szyfrowania z zabronionami zastąpieniami | Powiedzie się | Ulega |

W momencie tworzenia kontenera należy określić domyślny zakres szyfrowania dla kontenera.

Jeśli dla kontenera nie określono żadnego domyślnego zakresu szyfrowania, można przekazać obiekt BLOB przy użyciu dowolnego zakresu szyfrowania zdefiniowanego dla konta magazynu. Należy określić zakres szyfrowania w chwili, gdy obiekt BLOB zostanie przekazany.

## <a name="disabling-an-encryption-scope"></a>Wyłączanie zakresu szyfrowania

Po wyłączeniu zakresu szyfrowania wszelkie kolejne operacje odczytu lub zapisu wykonane z zakresem szyfrowania zakończą się niepowodzeniem z kodem błędu HTTP 403 (dostęp zabroniony). Po ponownym włączeniu zakresu szyfrowania operacje odczytu i zapisu będą normalnie powtarzane.

Po wyłączeniu zakresu szyfrowania nie są już naliczane opłaty. Wyłącz wszelkie zakresy szyfrowania, które nie są potrzebne, aby uniknąć niepotrzebnych opłat.

Jeśli zakres szyfrowania jest chroniony za pomocą klucza zarządzanego przez klienta, a klucz jest usuwany z magazynu kluczy, dane staną się niedostępne. Należy również wyłączyć zakres szyfrowania, aby uniknąć naliczania opłat.

Należy pamiętać, że klucze zarządzane przez klienta są chronione przez nietrwałe usuwanie i przeczyszczanie ochrony w magazynie kluczy, a usunięty klucz podlega zachowaniem zdefiniowanym przez te właściwości. Aby uzyskać więcej informacji, zobacz jeden z następujących tematów w dokumentacji Azure Key Vault:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Nie można usunąć zakresu szyfrowania.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../common/storage-service-encryption.md)
- [Tworzenie zakresów szyfrowania i zarządzanie nimi](encryption-scope-manage.md)
- [Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](../common/customer-managed-keys-overview.md)
- [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)