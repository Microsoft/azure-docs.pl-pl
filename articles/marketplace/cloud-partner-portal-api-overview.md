---
title: Dokumentacja interfejsu API portal Cloud Partner — Microsoft Commercial Marketplace
description: Opis, wymagania wstępne do użycia oraz lista operacji interfejsu API portalu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: bfb6e9cb510f5fe887f108dfdea5932406aafe0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292930"
---
# <a name="cloud-partner-portal-api-reference"></a>Dokumentacja interfejsu API portalu Cloud Partner

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj [zmiany w interfejsie API programu CPP](#changes-to-cpp-apis-after-the-migration-to-partner-center) wymienione w tym dokumencie, aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Interfejsy API REST portal Cloud Partner umożliwiają pobieranie programistyczne i manipulowanie obciążeniami, ofertami i profilami wydawcy. Interfejsy API używają kontroli dostępu opartej na rolach (RBAC) do wymuszania prawidłowych uprawnień w czasie przetwarzania.

Ta dokumentacja zawiera szczegółowe informacje techniczne dotyczące portal Cloud Partner interfejsów API REST. Próbki ładunku w tym dokumencie są przeznaczone tylko do celów informacyjnych i mogą ulec zmianie po dodaniu nowych funkcji.

## <a name="prerequisites-and-considerations"></a>Wymagania wstępne i zagadnienia

Przed użyciem interfejsów API należy zapoznać się z tematem:

- W artykule dotyczącym [wymagań wstępnych](./cloud-partner-portal-api-prerequisites.md) dowiesz się, jak dodać jednostkę usługi do konta i uzyskać token dostępu Azure Active Directory (Azure AD) na potrzeby uwierzytelniania.
- Dwie strategie [kontroli współbieżności](./cloud-partner-portal-api-concurrency-control.md) dostępne do wywoływania tych interfejsów API.
- Dodatkowe [zagadnienia dotyczące](./cloud-partner-portal-api-considerations.md)interfejsów API, takie jak przechowywanie wersji i obsługa błędów.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Zmiany w interfejsach API programu CPP po migracji do Centrum partnerskiego

| **Interfejs API** | **Zmień opis** | **Wpływ** |
| ------- | ---------------------- | ---------- |
| Publikuj publikowanie, GoLive, Anuluj | W przypadku zmigrowanych ofert nagłówek odpowiedzi będzie miał inny format, ale będzie nadal działał w taki sam sposób, co oznacza ścieżkę względną do pobrania stanu operacji. | Podczas wysyłania dowolnego z odpowiednich żądań POST dla oferty nagłówek lokalizacji będzie miał jeden z dwóch formatów w zależności od stanu migracji oferty:<ul><li>Oferty niemigrowane<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Zmigrowane oferty<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Pobierz operację | W przypadku typów ofert, które wcześniej obsługiwały pole "notification-email" w odpowiedzi, to pole będzie przestarzałe i nie będzie już zwracane dla zmigrowanych ofert. | W przypadku zmigrowanych ofert nie będą już wysyłane powiadomienia do listy wiadomości e-mail określonych w żądaniach. Zamiast tego usługa API zostanie wyrównuje z procesem powiadomień e-mail w centrum partnerskim w celu wysyłania wiadomości e-mail. W każdym przypadku powiadomienia będą wysyłane na adres e-mail ustawiony w sekcji informacje kontaktowe sprzedawcy ustawień konta w centrum partnerskim w celu powiadomienia o postępie operacji.<br><br>Zapoznaj się z informacjami o adresie e-mail w sekcji informacje kontaktowe sprzedawcy [ustawień konta](https://partner.microsoft.com/dashboard/account/management) w centrum partnerskim, aby upewnić się, że dla powiadomień została podana poprawna wiadomość e-mail.  |

## <a name="common-tasks"></a>Typowe zadania

Te szczegółowe informacje dotyczące interfejsów API do wykonywania następujących typowych zadań.

### <a name="offers"></a>Oferty

- [Pobierz wszystkie oferty](./cloud-partner-portal-api-retrieve-offers.md)
- [Pobierz konkretną ofertę](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Pobieranie stanu oferty](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Tworzenie oferty](./cloud-partner-portal-api-creating-offer.md)
- [Publikowanie oferty](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operacje

- [Pobieranie operacji](./cloud-partner-portal-api-retrieve-operations.md)
- [Anulowanie operacji](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikowanie aplikacji

- [Udostępnianie](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Inne zadania

- [Ustawianie cen dla ofert maszyn wirtualnych](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

- [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
