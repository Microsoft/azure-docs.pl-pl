---
title: Wprowadzenie — translator
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak zarejestrować się w usłudze Azure Cognitive Services translator i uzyskać klucz subskrypcji.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: a9050b85c974a3c04ca717e8fe3d5188c1bf7a40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88244316"
---
# <a name="how-to-sign-up-for-translator"></a>Jak zarejestrować się w usłudze translator

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

- Nie masz konta? Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services), aby eksperymentować bez opłat.
- Masz już konto? [Zaloguj się](https://ms.portal.azure.com/)

## <a name="create-a-subscription-for-translator"></a>Tworzenie subskrypcji usługi translator

Po zalogowaniu się do portalu można utworzyć subskrypcję usługi Translator w następujący sposób:

1. Wybierz pozycję **+ Utwórz zasób**.
1. W polu Wyszukaj w **portalu Marketplace wprowadź wartość** **translator** , a następnie wybierz ją z wyników.
1. Wybierz pozycję **Utwórz** , aby zdefiniować szczegóły dla subskrypcji.
1. Z listy **warstwa cenowa** wybierz warstwę cenową, która najlepiej odpowiada Twoim potrzebom.
    1. Każda subskrypcja ma bezpłatną warstwę. Warstwa Bezpłatna ma takie same funkcje, jak plany płatne i nie wygasa.
    1. Dla Twojego konta może istnieć tylko jedna bezpłatna subskrypcja.
1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie subskrypcji.

## <a name="authentication-key"></a>Klucz uwierzytelniania

Po zarejestrowaniu się w usłudze translator otrzymujesz spersonalizowany klucz dostępu unikatowy dla Twojej subskrypcji. Ten klucz jest wymagany dla każdego wywołania translatora.

1. Pobierz klucz uwierzytelniania, wybierając odpowiednią subskrypcję.
1. Wybierz pozycję **klucze** w sekcji **Zarządzanie zasobami** w obszarze szczegóły subskrypcji.
1. Skopiuj jeden z kluczy wymienionych dla Twojej subskrypcji.

## <a name="learn-test-and-get-support"></a>Uczenie się, testowanie i uzyskiwanie pomocy technicznej

- [Przykłady kodu w witrynie GitHub](https://github.com/MicrosoftTranslator)
- [Forum pomocy technicznej usługi Microsoft Translator](https://www.aka.ms/TranslatorForum)

Usługi Microsoft Translator zazwyczaj umożliwią przekazanie pierwszych kilku żądań przed zweryfikowaniem stanu konta subskrypcji. Jeśli pierwsze kilka żądań translatora zakończyło się pomyślnie, wywołania zakończą się niepowodzeniem, odpowiedź na błąd będzie wskazywać na problem. Zarejestruj odpowiedź interfejsu API, aby zobaczyć przyczynę.

## <a name="pricing-options"></a>Opcje cennika

- [Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Dostosowywanie

Użyj translatora niestandardowego, aby dostosować tłumaczenia i utworzyć system tłumaczenia dostosowany do własnej terminologii i stylu, rozpoczynając od ogólnego systemu tłumaczenia usługi Microsoft Translator neuronowych Machine. [Dowiedz się więcej](customization.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Wprowadzenie do platformy Azure (wideo 3-minutowy)](https://azure.microsoft.com/get-started/?b=16.24)
- [Jak uregulować fakturę](https://azure.microsoft.com/pricing/invoicing/)
