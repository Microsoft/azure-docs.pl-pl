---
title: Poznaj pojęcia dotyczące narzędzia do przeglądu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat narzędzia do przeglądu Content Moderator, witryny sieci Web, która koordynuje związany z tym, że nastąpi łagodzenie połączonych plików AI i
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76169503"
---
# <a name="content-moderator-review-tool"></a>Narzędzie do przeglądu Content Moderator

Usługa Azure Content Moderator zapewnia usługi do łączenia moderowania zawartości usługi Machine Learning z recenzjami ludzkimi, a witryna internetowa [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com) jest przyjaznym frontonem, który zapewnia szczegółowy dostęp do tych usług.

![Pulpit nawigacyjny narzędzia przeglądu w przeglądarce](./images/0-dashboard.png)

## <a name="what-it-does"></a>Wyniki działania

[Narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com), używane w połączeniu z interfejsami API moderowanymi maszynowo, umożliwia wykonywanie następujących zadań w procesie moderowania zawartości:

- Użyj jednego zestawu narzędzi do umiarkowanej zawartości w wielu formatach (tekst, obraz i wideo).
- Automatyzuj tworzenie [przeglądów](../review-api.md#reviews) ludzkich, gdy są dostępne wyniki interfejsu API moderowania.
- Przypisywać lub eskalować przeglądy zawartości do wielu zespołów przeglądów zorganizowanych według kategorii zawartości lub poziomu doświadczenia.
- Użyj domyślnych lub niestandardowych filtrów logiki,[workflows](../review-api.md#workflows)aby sortować i śledzić zawartość, bez konieczności pisania kodu.
- Korzystając z [łączników](./configure.md#connectors) , można przetwarzać zawartość przy użyciu usług Microsoft PhotoDNA, analiza tekstu i twarz, a także interfejsów API Content Moderator.
- Utwórz własny łącznik, aby utworzyć przepływy pracy dla dowolnego interfejsu API lub procesu biznesowego.
- Pobierz kluczowe metryki wydajności procesów moderowania zawartości.

## <a name="review-tool-dashboard"></a>Przejrzyj pulpit nawigacyjny narzędzi

Na karcie **pulpit nawigacyjny** można zobaczyć kluczowe metryki dla przeglądów zawartości wykonywane w ramach tego narzędzia. Zobacz, ile całkowitej, pełnej i oczekującej oceny zawartości obrazu, tekstu i wideo. Można także zobaczyć podział użytkowników i zespołów, którzy ukończyli przeglądy, a także Tagi moderowania, które zostały zastosowane.

![Wyświetl pulpit nawigacyjny](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Zapoznaj się z poświadczeniami narzędzia

Po zarejestrowaniu się za pomocą [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com)zostanie wyświetlony monit o wybranie regionu platformy Azure dla konta. Wynika to z faktu, że [Narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) generuje bezpłatny klucz wersji próbnej dla usług Azure Content moderator Services. Ten klucz będzie potrzebny do uzyskiwania dostępu do dowolnych usług z poziomu usługi REST lub zestawu SDK klienta. Możesz wyświetlić klucz i adres URL punktu końcowego interfejsu API, wybierając pozycję **Ustawienia**  >  **poświadczenia**.

![Poświadczenia Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Następne kroki

Zobacz [Konfigurowanie narzędzia do przeglądu](./configure.md) , aby dowiedzieć się, jak uzyskać dostęp do zasobów narzędzia do przeglądu i zmienić ustawienia.