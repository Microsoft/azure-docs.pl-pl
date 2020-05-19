---
title: Cognitive Services proces kontroli
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak stosować do wczesnego dostępu do nowych kontenerów i interfejsów API Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599514"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Proces kontroli dla platformy Azure Cognitive Services

> [!NOTE]
> Gdy usługa zostanie ukończona w wersji zapoznawczej, przejdzie do publicznej wersji zapoznawczej, która nie wymaga dostępu do aplikacji. Po zakończeniu procesu w wersji zapoznawczej jest on publikowany jako ogólnie dostępny (GA).

Po wprowadzeniu nowych ofert w usłudze Azure Cognitive Services są one przechodzą przez bramę w wersji zapoznawczej, w której klienci muszą zażądać dostępu za pomocą aplikacji. Ten proces kontroli pomaga identyfikować możliwości ulepszeń usług, zanim są one szeroko dostępne. 

Ten artykuł przeprowadzi Cię przez proces aplikacji dla ofert Cognitive Servicesch warunkowych.

## <a name="eligibility-and-approval-process"></a>Proces kwalifikowania i zatwierdzania

Proces kontroli służy do oceny zainteresowania i lepszego zrozumienia potrzeb klientów. Zespół firmy Microsoft akceptuje [aplikacje](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) od komercyjnych klientów firmy Microsoft z prawidłową subskrypcją platformy Azure i prawidłowym scenariuszem biznesowym. Klienci będą mogli korzystać z aplikacji, gdy:

 - Nie są one skojarzone z żadną organizacją
 - Nie mają one ważnej subskrypcji platformy Azure
 - Aplikacja została przesłana za pośrednictwem osobistej poczty e-mail ( @hotmail.com , @gmail.com , @yahoo.com )
 - Nie ma odpowiedniego uzasadnienia ani scenariusza biznesowego

Na żądanie z różnych segmentów klientów próbujemy przyspieszyć proces zatwierdzania. Nie można jednak przekazać do osi czasu. Po podjęciu decyzji zespół firmy Microsoft skontaktuje się z Tobą i zespołem zarządzania kontami w celu wykonania następnych kroków. Doceniamy Twoją wiedzę i cierpliwość.

Jeśli aplikacja zostanie zatwierdzona, zespół firmy Microsoft wyśle wiadomość e-mail z informacjami, dokumentacją i wskazówkami. Cognitive Services szczegóły cennika są dostępne [tutaj](https://azure.microsoft.com/pricing/details/cognitive-services/).


Obecnie poniższe usługi są oferowane przez proces kontroli:

## <a name="gated-web-apis"></a>Interfejsy API sieci Web z bramą

|Usługa  |
|---------|
|Wykrywacz anomalii v2     | 

## <a name="gated-online-containers"></a>Kontenery z bramą online

| Usługa                             | Kontenery                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Przetwarzanie obrazów][cv-containers]    | Odczyt                                                                          |
| [Rozpoznawanie twarzy][fa-containers]               | Rozpoznawanie twarzy                                                                          |
| [Rozpoznawanie formularzy][fr-containers]    | Rozpoznawanie formularzy                                                               |
| [Interfejs API usługi rozpoznawania mowy][sp-containers] | Zamiana mowy na tekst (niestandardowe i standardowe) i zamiana tekstu na mowę (niestandardowe i standardowe) |
| [Tłumaczenie tekstu w usłudze Translator][tt-containers]    | Tłumaczenie tekstu w usłudze Translator                                                               |

> [!IMPORTANT]
> Jeśli nie ma na liście oferty usługi lub kontenera, nie jest ona brama lub jest niedostępna.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarejestruj się w usłudze Gated](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
