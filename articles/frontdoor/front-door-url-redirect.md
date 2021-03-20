---
title: Moje drzwi platformy Azure — przekierowanie adresu URL | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, w jaki sposób platforma Azure front-drzwi obsługuje przekierowywanie adresów URL dla reguł routingu.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91442057"
---
# <a name="url-redirect"></a>Przekierowywanie adresów URL
Drzwi frontonu platformy Azure mogą przekierowywać ruch na każdym z następujących poziomów: protokołu, nazwy hosta, ścieżki, ciągu zapytania. Te funkcje można skonfigurować dla poszczególnych mikrousług, ponieważ przekierowanie jest oparte na ścieżce. Może to uprościć konfigurację aplikacji przez optymalizację użycia zasobów i obsługuje nowe scenariusze przekierowania, w tym globalne i oparte na ścieżkach przekierowywanie.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Przekierowanie adresu URL usługi Azure front-drzwi":::

## <a name="redirection-types"></a>Typy przekierowań
Typ przekierowania ustawia kod stanu odpowiedzi dla klientów, aby zrozumieć przeznaczenie przekierowania. Obsługiwane są następujące typy przekierowań:

- **301 (trwale przeniesiono)**: wskazuje, że do zasobu docelowego przypisano nowy stały identyfikator URI. Wszystkie przyszłe odwołania do tego zasobu będą używać jednego z załączonych identyfikatorów URI. Użyj kodu stanu 301 do przekierowania HTTP do HTTPS. 
- **302 (znaleziono)**: wskazuje, że zasób docelowy jest tymczasowo pod innym identyfikatorem URI. Ponieważ przekierowanie może ulec zmianie, klient powinien nadal używać obowiązującego identyfikatora URI żądania dla przyszłych żądań.
- **307 (tymczasowe przekierowanie)**: wskazuje, że zasób docelowy jest tymczasowo pod innym identyfikatorem URI. Agent użytkownika nie może zmienić metody żądania, jeśli wykonuje automatyczne przekierowanie do tego identyfikatora URI. Ponieważ przekierowanie może ulec zmianie z upływem czasu, klient powinien kontynuować korzystanie z oryginalnego identyfikatora URI żądania dla przyszłych żądań.
- **308 (stałe przekierowanie)**: wskazuje, że do zasobu docelowego przypisano nowy stały identyfikator URI. Wszystkie przyszłe odwołania do tego zasobu powinny używać jednego z załączonych identyfikatorów URI.

## <a name="redirection-protocol"></a>Protokół przekierowania
Można ustawić protokół, który będzie używany do przekierowywania. Najczęstszym przypadkiem użycia funkcji redirect jest ustawienie przekierowania HTTP na HTTPS.

- **Tylko https**: Ustaw tylko protokół https, jeśli chcesz przekierować ruch z protokołu HTTP do protokołu HTTPS. Przed drzwiami platformy Azure zaleca się, aby zawsze ustawiać przekierowanie tylko do protokołu HTTPS.
- **Tylko http**: przekierowuje żądanie przychodzące do protokołu HTTP. Użyj tej wartości tylko wtedy, gdy chcesz zachować ruch HTTP, który jest nieszyfrowany.
- **Żądanie dopasowania**: Ta opcja utrzymuje protokół używany przez żądanie przychodzące. W związku z tym żądanie HTTP pozostaje HTTP, a żądanie HTTPS pozostaje przekierowaniem post HTTPS.

## <a name="destination-host"></a>Host docelowy
W ramach konfigurowania routingu przekierowywania można także zmienić nazwę hosta lub domenę dla żądania przekierowania. Można ustawić to pole, aby zmienić nazwę hosta w adresie URL przekierowania lub w przeciwnym razie zachować nazwę hosta z żądania przychodzącego. Dlatego przy użyciu tego pola można przekierować wszystkie żądania wysłane `https://www.contoso.com/*` do usługi `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Ścieżka docelowa
W przypadku, gdy chcesz zastąpić segment ścieżki adresu URL w ramach przekierowania, możesz ustawić to pole z nową wartością ścieżki. W przeciwnym razie można zachować wartość ścieżki w ramach przekierowania. Dlatego przy użyciu tego pola można przekierować wszystkie żądania wysyłane do `https://www.contoso.com/\*` programu  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Parametry ciągu zapytania
Parametry ciągu zapytania można również zastąpić w przekierowanym adresie URL. Aby zastąpić wszystkie istniejące parametry zapytania z przychodzącego adresu URL żądania, należy ustawić to pole na wartość "Zamień", a następnie ustawić odpowiednie wartości. W przeciwnym razie można zachować oryginalny zestaw ciągów zapytania, ustawiając pole na wartość "preserve". Na przykład przy użyciu tego pola można przekierować cały ruch wysyłany do usługi `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Fragment docelowy
Fragment docelowy jest częścią adresu URL po "#", który jest używany przez przeglądarkę do gruntów w określonej sekcji strony sieci Web. Można ustawić to pole, aby dodać fragment do adresu URL przekierowania.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
