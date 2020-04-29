---
title: Usługa mowy — szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Usługa mowy — szyfrowanie danych magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372360"
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

Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [co to są tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Przenoszenie własnego magazynu (BYOS) na potrzeby dostosowywania i rejestrowania

Aby zażądać dostępu w celu przeniesienia własnego magazynu, Wypełnij i prześlij [usługę mowy — Wstaw własny formularz żądania magazynu (BYOS)](https://aka.ms/cogsvc-cmk). Po zatwierdzeniu należy utworzyć własne konto magazynu do przechowywania danych wymaganych do dostosowania i rejestrowania. W przypadku dodawania konta magazynu zasób usługi mowy spowoduje włączenie tożsamości zarządzanej przypisanej do systemu. Po włączeniu tożsamości zarządzanej przypisanej do systemu ten zasób zostanie zarejestrowany w Azure Active Directory (AAD). Po zarejestrowaniu tożsamość zarządzana będzie mieć dostęp do konta magazynu. Więcej informacji o tożsamościach zarządzanych można znaleźć tutaj. Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [co to są tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Jeśli wyłączysz tożsamości zarządzane przypisane przez system, dostęp do konta magazynu zostanie usunięty. Spowoduje to, że części usługi mowy, które wymagają dostępu do konta magazynu, przestaną działać.  

## <a name="regional-availability"></a>Dostępność regionalna

BYOS jest obecnie dostępna w następujących regionach:

* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA

## <a name="next-steps"></a>Następne kroki

* [Usługa mowy — formularz żądania własnego magazynu (BYOS)](https://aka.ms/cogsvc-cmk)
* [Co to są tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
