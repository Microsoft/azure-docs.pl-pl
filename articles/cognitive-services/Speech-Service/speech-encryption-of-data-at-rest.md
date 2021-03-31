---
title: Usługa mowy — szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule opisano szyfrowanie danych w usłudze REST dla usługi Speech.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: aa0fe33dff0161767b74546aad49003d8fc70c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015260"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Usługa mowy — szyfrowanie danych magazynowanych

Usługa Speech automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Szyfrowanie usługi mowy chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

## <a name="about-cognitive-services-encryption"></a>Informacje o szyfrowaniu Cognitive Services

Dane są szyfrowane i odszyfrowywane przy użyciu zgodności ze standardem [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitowego szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) . Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane przez Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

W przypadku korzystania z Custom Speech i niestandardowego głosu usługa mowy może przechowywać następujące dane w chmurze:  

* Dane śledzenia mowy — tylko po włączeniu śledzenia dla niestandardowego punktu końcowego
* Przekazane dane szkoleniowe i testowe

Domyślnie dane są przechowywane w magazynie firmy Microsoft, a Twoja subskrypcja korzysta z kluczy szyfrowania zarządzanych przez firmę Microsoft. Dostępna jest również opcja przygotowania własnego konta magazynu. Dostęp do sklepu jest zarządzany przez zarządzaną tożsamość, a usługa mowy nie może bezpośrednio uzyskać dostępu do własnych danych, takich jak dane śledzenia mowy, dane szkoleniowe dotyczące dostosowywania i modele niestandardowe.

Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [co to są tożsamości zarządzane](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Przenoszenie własnego magazynu (BYOS) na potrzeby dostosowywania i rejestrowania

Aby zażądać dostępu w celu przeniesienia własnego magazynu, Wypełnij i prześlij [usługę mowy — Wstaw własny formularz żądania magazynu (BYOS)](https://aka.ms/cogsvc-cmk). Po zatwierdzeniu należy utworzyć własne konto magazynu do przechowywania danych wymaganych do dostosowania i rejestrowania. W przypadku dodawania konta magazynu zasób usługi mowy spowoduje włączenie tożsamości zarządzanej przypisanej do systemu. Po włączeniu tożsamości zarządzanej przypisanej do systemu ten zasób zostanie zarejestrowany w Azure Active Directory (AAD). Po zarejestrowaniu tożsamość zarządzana będzie mieć dostęp do konta magazynu. Więcej informacji o tożsamościach zarządzanych można znaleźć tutaj. Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [co to są tożsamości zarządzane](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Jeśli wyłączysz tożsamości zarządzane przypisane przez system, dostęp do konta magazynu zostanie usunięty. Spowoduje to, że części usługi mowy, które wymagają dostępu do konta magazynu, przestaną działać.  

Usługa mowy nie obsługuje obecnie Skrytka klienta. Dane klienta mogą jednak być przechowywane przy użyciu BYOS, co pozwala na uzyskanie podobnych kontroli danych do [skrytka klienta](../../security/fundamentals/customer-lockbox-overview.md). Należy pamiętać, że dane usługi mowy pozostają i są przetwarzane w regionie, w którym został utworzony zasób mowy. Dotyczy to danych przechowywanych w czasie spoczynku i przesyłania danych. W przypadku korzystania z funkcji dostosowywania, takich jak Custom Speech i Niestandardowa obsługa głosu, wszystkie dane klienta są przesyłane, przechowywane i przetwarzane w tym samym regionie, w którym znajduje się zasób usługi BYOS (jeśli jest używany) i usługa Speech.

> [!IMPORTANT]
> Firma Microsoft **nie** używa danych klienta w celu ulepszania modeli mowy. Ponadto jeśli rejestrowanie punktów końcowych jest wyłączone i nie są używane żadne dostosowania, dane klienta nie są przechowywane. 

## <a name="next-steps"></a>Następne kroki

* [Usługa mowy — formularz żądania własnego magazynu (BYOS)](https://aka.ms/cogsvc-cmk)
* [Co to są tożsamości zarządzane](../../active-directory/managed-identities-azure-resources/overview.md).