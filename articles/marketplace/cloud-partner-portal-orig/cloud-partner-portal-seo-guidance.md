---
title: Wskazówki dotyczące optymalizacji Azure Marketplace
description: Zawiera wskazówki dotyczące maksymalizowania optymalizacji aparatu wyszukiwania (wyszukiwarka).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280154"
---
# <a name="azure-marketplace-seo-guidance"></a>Wskazówki dotyczące optymalizacji Azure Marketplace

W tym artykule wyjaśniono, jak zmaksymalizować możliwości odnajdywania oferty za pomocą funkcji wyszukiwania w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Ogólne wyjaśnienie algorytmu

Witryny Microsoft Marketplace wykorzystują Wyszukiwanie poznawcze platformy Azure do obsługi możliwości wyszukiwania w witrynie. Algorytm jest oparty na częstotliwości okresów — częstotliwość odwracania dokumentu ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Używany jest standardowy [Analizator Lucene](https://lucene.apache.org/core/) .

Ogólnie rzecz biorąc, wszystkie pola tekstowe, kategorie i branże oraz uwzględnienie wagi istotności. Wyspecjalizowane terminy, które są często używane przez aplikacje, ale często w aplikacji, będą generować wyższy wynik dopasowania z wyszukiwaniem. W związku z tym takie jak "maszyna wirtualna" może przynieść niewielkie korzyści, a "usługa Azure Search" będzie znacznie bardziej wyspecjalizowana.
Poniżej znajdują się najbardziej odpowiednie pola, które należy wziąć pod uwagę.

 
|  Pole                   | Ważność | Wskazówki                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Offer Name               |  Wysoka      | Dokładne lub blisko całkowitego dopasowania z kwerendą wyszukiwania spowoduje przeprowadzenie wysokiej klasyfikacji.                       |
| Publisher Name           |  Wysoka      | Dokładne lub blisko całkowitego dopasowania z kwerendą wyszukiwania spowoduje przeprowadzenie wysokiej klasyfikacji.                       |
| Krótki opis        |  Medium    | Podawanie nazwy aplikacji i nazw wydawcy niemal gwarantuje wysoką klasyfikację, ale może nie być najbardziej odpowiednie. W tym przypadku Krótki opis ma krytyczne znaczenie. Zachowaj zwięzły tekst i do punktu. W celu uzyskania najlepszego wyniku należy uwzględnić słowa kluczowe i oczekiwane terminy wyszukiwania.  Na przykład "jest to Najlepsza wersja detaliczna w pełni oparta na systemie Dynamics 365" jest niższa od "sprzedaży detalicznej (punkt sprzedaży) dla usługi Dynamics 365".  | 
| Długi opis         |  Małe       | Opis oferuje sposób przechodzenia do większej głębokości. Najbardziej efektywne opisy mają uzasadnione długość i są używane słowa kluczowe.  Opisy do punktów, które używają słów kluczowych, będą korzystać z dłuższego, długiego tekstu. Upewnij się, że najważniejsze terminy, takie jak "IoT", są obecne w opisie.  |
| Kategorie produktu       | Medium     |  Kategorie produktów są określane przez kombinację opcji wydawcy i firmy Microsoft. Wybierz odpowiednie kategorie odpowiednio, aby użytkownicy mogli łatwo znaleźć aplikacje w odpowiedniej kategorii. |
|  |  |  |


## <a name="other-tips"></a>Inne porady

-   Sugestie wyszukiwania sugerują aktywność użytkownika. Priorytety są zgodne z nazwą aplikacji/wydawcą. Krótki opis to pole klucza, dla którego termin wyszukiwania nie jest dokładnym odpowiednikiem nazwy wydawcy/aplikacji.
-   Dokumenty do pobrania nie są uwzględnione w wyszukiwaniu.
-   Twoje aplikacje rzeczywiste pozyskiwanie i użycie wpłyną również na klasyfikację wyszukiwania. Na przykład dwie równoważne aplikacje, na których jeden ma znacznie większą liczbę użytkowników, będą mieć wyższą klasyfikację.
