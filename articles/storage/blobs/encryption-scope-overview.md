---
title: Zakresy szyfrowania dla usługi BLOB Storage (wersja zapoznawcza)
description: Zakresy szyfrowania zapewniają możliwość zarządzania szyfrowaniem na poziomie kontenera lub pojedynczym obiektem BLOB. Zakresy szyfrowania można używać do tworzenia bezpiecznych granic między danymi znajdującymi się na tym samym koncie magazynu, ale należą do różnych klientów.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 35395a30f7d58b9edb3aa7622a35e8c4a62dc76f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211366"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Zakresy szyfrowania dla usługi BLOB Storage (wersja zapoznawcza)

Zakresy szyfrowania zapewniają możliwość zarządzania szyfrowaniem na poziomie kontenera lub pojedynczym obiektem BLOB. Zakresy szyfrowania można używać do tworzenia bezpiecznych granic między danymi znajdującymi się na tym samym koncie magazynu, ale należą do różnych klientów.

Domyślnie konto magazynu jest szyfrowane przy użyciu klucza, który jest objęty zakresem całego konta magazynu. Mając zakres szyfrowania, można określić, że co najmniej jeden kontener jest szyfrowany przy użyciu klucza, który jest objęty zakresem tylko tych kontenerów.

Możesz użyć kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta przechowywanych w Azure Key Vault, aby chronić i kontrolować dostęp do klucza, który szyfruje dane. Różne zakresy szyfrowania na tym samym koncie magazynu mogą korzystać z kluczy zarządzanych przez firmę Microsoft lub przez klienta.

Po utworzeniu zakresu szyfrowania możesz określić ten zakres szyfrowania dla żądania, aby utworzyć kontener lub obiekt BLOB. Aby uzyskać więcej informacji na temat sposobu tworzenia zakresu szyfrowania, zobacz [Tworzenie zakresów szyfrowania i zarządzanie nimi (wersja zapoznawcza)](encryption-scope-manage.md).

> [!IMPORTANT]
> Zakresy szyfrowania są obecnie w **wersji zapoznawczej**. Zapoznaj się z [dodatkowymi postanowieniami dotyczącymi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych w Microsoft Azure wersjach zapoznawczych, które mają zastosowanie do funkcji platformy Azure w wersjach beta, Preview lub innych, które nie zostały jeszcze ogólnie udostępnione.
>
> Aby uniknąć nieoczekiwanych kosztów, należy wyłączyć wszelkie zakresy szyfrowania, które nie są obecnie potrzebne.
>
> Zakresy szyfrowania nie są obsługiwane z magazynem geograficznie nadmiarowym z dostępem do odczytu (RA-GRS) lub z dostępem do odczytu strefy geograficznie nadmiarowego (RA-GZRS) w ramach wersji zapoznawczej.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Tworzenie kontenera lub obiektu BLOB z zakresem szyfrowania

Obiekty blob tworzone w ramach zakresu szyfrowania są szyfrowane przy użyciu klucza określonego dla tego zakresu. Możesz określić zakres szyfrowania dla pojedynczego obiektu BLOB podczas tworzenia obiektu BLOB lub określić domyślny zakres szyfrowania podczas tworzenia kontenera. Po określeniu domyślnego zakresu szyfrowania na poziomie kontenera wszystkie obiekty blob w tym kontenerze są szyfrowane przy użyciu klucza skojarzonego z zakresem domyślnym.

Podczas tworzenia obiektu BLOB w kontenerze z domyślnym zakresem szyfrowania można określić zakres szyfrowania, który zastąpi domyślny zakres szyfrowania, jeśli kontener jest skonfigurowany tak, aby zezwalał na przesłonięcia domyślnego zakresu szyfrowania. Aby zapobiec zastępowaniu domyślnego zakresu szyfrowania, należy skonfigurować kontener tak, aby odmówił zastąpień dla pojedynczego obiektu BLOB.

Operacje odczytu na obiekcie blob, który należy do zakresu szyfrowania, są w sposób przezroczysty, tak długo, jak zakres szyfrowania nie jest wyłączony.

## <a name="disable-an-encryption-scope"></a>Wyłącz zakres szyfrowania

Po wyłączeniu zakresu szyfrowania wszelkie kolejne operacje odczytu lub zapisu wykonane z zakresem szyfrowania zakończą się niepowodzeniem z kodem błędu HTTP 403 (dostęp zabroniony). Po ponownym włączeniu zakresu szyfrowania operacje odczytu i zapisu będą normalnie powtarzane.

Po wyłączeniu zakresu szyfrowania nie są już naliczane opłaty. Wyłącz wszelkie zakresy szyfrowania, które nie są potrzebne, aby uniknąć niepotrzebnych opłat.

Jeśli zakres szyfrowania jest chroniony przy użyciu kluczy zarządzanych przez klienta, można również usunąć skojarzony klucz w magazynie kluczy, aby wyłączyć zakres szyfrowania. Należy pamiętać, że klucze zarządzane przez klienta są chronione przez nietrwałe usuwanie i przeczyszczanie ochrony w magazynie kluczy, a usunięty klucz podlega zachowaniem zdefiniowanym przez te właściwości. Aby uzyskać więcej informacji, zobacz jeden z następujących tematów w dokumentacji Azure Key Vault:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Nie można usunąć zakresu szyfrowania.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../common/storage-service-encryption.md)
- [Tworzenie zakresów szyfrowania i zarządzanie nimi (wersja zapoznawcza)](encryption-scope-manage.md)
- [Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](../common/customer-managed-keys-overview.md)
- [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)