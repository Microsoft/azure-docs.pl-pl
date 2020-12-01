---
title: Co to jest interfejsy API wyszukiwania Bing?
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego artykułu, aby dowiedzieć się więcej na temat interfejsy API wyszukiwania Bing i jak można włączyć poznawcze wyszukiwanie w Internecie w aplikacjach i usługach.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349541"
---
# <a name="what-are-the-bing-search-apis"></a>Co to jest interfejsy API wyszukiwania Bing?

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejsy API wyszukiwania Bing pozwalają tworzyć aplikacje i usługi połączone z Internetem, które znajdują strony internetowe, obrazy, wiadomości, lokalizacje i inne dane bez reklam. Wysyłając żądania wyszukiwania przy użyciu Wyszukiwanie Bing interfejsów API REST lub zestawów SDK, możesz uzyskać odpowiednie informacje i zawartość dla wyszukiwania w sieci Web. Skorzystaj z tego artykułu, aby dowiedzieć się więcej na temat różnych interfejsów API wyszukiwania Bing i sposobu integrowania funkcji wyszukiwania poznawczego z aplikacjami i usługami. Limity cen i szybkości mogą się różnić w zależności od interfejsów API.

## <a name="the-bing-web-search-api"></a>interfejs API wyszukiwania w sieci Web Bing

[Interfejs API wyszukiwania w sieci Web Bing](../Bing-Web-Search/overview.md) zwraca strony sieci Web, obrazy, wideo, wiadomości i inne. Można filtrować zapytania wyszukiwania wysyłane do tego interfejsu API w celu uwzględnienia lub wykluczenia niektórych typów zawartości.

Rozważ użycie interfejs API wyszukiwania w sieci Web Bing w aplikacjach, które mogą wymagać wyszukania wszystkich typów odpowiedniej zawartości sieci Web. Jeśli aplikacja szuka określonego typu zawartości online, należy wziąć pod uwagę jeden z poniższych interfejsów API wyszukiwania:

## <a name="content-specific-bing-search-apis"></a>Interfejsy API wyszukiwania Bing specyficzne dla zawartości

Poniższe interfejsy API wyszukiwania Bing zwracają określoną zawartość z sieci Web, takie jak obrazy, wiadomości, lokalne firmy i wideo.

| Interfejs API Bing | Opis |
| -- | -- |
| [wyszukiwanie jednostek](../Bing-Entities-Search/overview.md) | Interfejs API wyszukiwania jednostek Bing zwraca wyniki wyszukiwania zawierające jednostki, które mogą być osobami, miejscami lub elementami. W zależności od zapytania interfejs API zwróci co najmniej jedną jednostkę, która spełnia zapytanie wyszukiwania. Zapytanie wyszukiwania może obejmować pojedyncze osoby, lokalne firmy, punkty orientacyjne i inne. |
| [Wyszukiwanie obrazów](../Bing-Image-Search/overview.md) | Interfejs API wyszukiwania obrazów Bing pozwala wyszukiwać i znajdować wysokiej jakości obrazy statyczne i animowane podobne do [Bing.com/images](https://www.Bing.com/images). Możesz udoskonalać wyszukiwanie, aby uwzględnić lub wykluczyć obrazy według atrybutów, w tym rozmiaru, koloru, licencji i aktualności. Możesz również wyszukać popularne obrazy, przekazać obrazy, aby uzyskać szczegółowe informacje o nich i wyświetlić podglądy miniatur. |
| [Wyszukiwanie wiadomości](../Bing-News-Search/search-the-web.md) | Interfejs API wyszukiwania wiadomości Bing pozwala znaleźć historie Aktualności podobne do [Bing.com/news](https://www.Bing.com/news). Interfejs API zwraca artykuły z wiadomości z wielu źródeł lub określonych domen. Możesz przeszukiwać w różnych kategoriach, aby uzyskać popularne artykuły, najważniejsze historie i nagłówki. |
| [Wyszukiwanie wideo](../Bing-Video-Search/overview.md) | Interfejs API wyszukiwania wideo Bing umożliwia znalezienie filmów wideo w sieci Web. Uzyskaj popularne wideo, powiązaną zawartość i podglądy miniatur. |
| [wyszukiwanie wizualne](../Bing-visual-search/overview.md) | Przekaż obraz lub użyj adresu URL, aby uzyskać szczegółowe informacje na jego temat, takie jak produkty podobne do wizualizacji, obrazy i wyszukiwania pokrewne. |
 [Lokalne wyszukiwanie biznesowe](../bing-local-business-search/overview.md) | Interfejs API wyszukiwania lokalnego usługi Bing umożliwia aplikacjom Znajdowanie informacji o kontakcie i lokalizacji na temat lokalnych firm w oparciu o zapytania wyszukiwania. |

## <a name="the-bing-custom-search-api"></a>interfejs API wyszukiwania niestandardowego Bing

Tworzenie niestandardowego wystąpienia wyszukiwania za pomocą interfejsu API [wyszukiwanie niestandardowe Bing](../Bing-Custom-Search/overview.md) umożliwia tworzenie środowiska wyszukiwania ukierunkowanego tylko na zawartość i tematy, które Cię interesują. Na przykład po określeniu domen, witryn sieci Web i określonych stron internetowych, które będą wyszukiwane przez usługę Bing, wyszukiwanie niestandardowe Bing dostosowuje wyniki do tej konkretnej zawartości. Możesz dołączyć interfejsy API niestandardowego automatycznego sugerowania, obrazu i wyszukiwanie wideo Bing w celu dodatkowego dostosowania środowiska wyszukiwania.

## <a name="additional-bing-search-apis"></a>Dodatkowe interfejsy API wyszukiwania Bing

Poniższe interfejsy API wyszukiwania Bing pozwalają ulepszyć środowisko wyszukiwania, łącząc je z innymi interfejsami API wyszukiwania Bing.

| Interfejs API | Opis |
| -- | -- |
| [Automatyczne sugerowanie Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Zwiększ możliwości wyszukiwania aplikacji za pomocą interfejs API automatycznego sugerowania Bing, zwracając sugerowane wyszukiwania w czasie rzeczywistym.  |
| [Statystyka Bing](bing-web-stats.md) | Statystyka Bing zawiera analizę dla interfejsy API wyszukiwania Bing używanej przez aplikację. Niektóre z dostępnych analiz obejmują wolumin wywołań, najpopularniejsze ciągi zapytań i dystrybucję geograficzną. |

## <a name="next-steps"></a>Następne kroki

* [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) interfejsu API wyszukiwanie Bing
* [Wymagania dotyczące użycia i wyświetlania Bing](./use-display-requirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.