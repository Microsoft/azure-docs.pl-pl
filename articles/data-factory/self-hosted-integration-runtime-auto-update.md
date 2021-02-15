---
title: Powiadomienia dotyczące automatycznej aktualizacji i wygasania własnego środowiska Integration Runtime
description: Informacje na temat samodzielnej aktualizacji i powiadomień o wygaśnięciu środowiska Integration Runtime
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376262"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Powiadomienia dotyczące automatycznej aktualizacji i wygasania własnego środowiska Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak umożliwić samodzielne aktualizowanie środowiska Integration Runtime do najnowszej wersji oraz jak moduł ADF zarządza wersjami własnego środowiska Integration Runtime.

## <a name="self-hosted-integration-runtime-auto-update"></a>Samoobsługowa automatyczna aktualizacja Integration Runtime
Ogólnie rzecz biorąc, gdy instalujesz własne środowisko Integration Runtime na komputerze lokalnym lub maszynie wirtualnej platformy Azure, masz dwie opcje zarządzania wersją własnego środowiska Integration Runtime: automatyczna aktualizacja lub konserwacja ręcznie. Zwykle w usłudze ADF są dostępne dwie nowe wersje środowiska Integration Runtime w każdym miesiącu, w tym nowe wydanie funkcji, poprawki błędów lub ulepszenia. Zalecamy, aby użytkownicy mogli zaktualizować do najnowszej wersji, aby uzyskać najnowsze funkcje i ulepszenia.

Najbardziej wygodnym sposobem jest włączenie automatycznej aktualizacji podczas tworzenia lub edytowania własnego środowiska Integration Runtime. Następnie zostanie automatycznie zaktualizowana do najnowszej wersji. Możesz również zaplanować aktualizację w najbardziej odpowiednim gnieździe czasowym.

![Włącz funkcję autoaktualizowania](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Możesz sprawdzić ostatnią datę i godzinę aktualizacji w kliencie środowiska Integration Runtime.

![Zrzut ekranu przedstawiający sprawdzanie czasu aktualizacji](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Aby zapewnić stabilność środowiska Integration Runtime (własne środowisko), mimo że firma Microsoft udostępnia dwie wersje, firma Microsoft będzie automatycznie aktualizować ją co miesiąc. Czasami zobaczysz, że aktualizowana na bieżąco wersja jest wcześniejszą wersją rzeczywistej najnowszej wersji. Jeśli chcesz uzyskać najnowszą wersję, możesz przejść do [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Powiadomienie dotyczące samodzielnej obsługi Integration Runtime wygasa
Jeśli chcesz ręcznie kontrolować wersję środowiska Integration Runtime (samodzielne), możesz wyłączyć ustawienie automatycznej aktualizacji i zainstalować je ręcznie. Każda wersja środowiska Integration Runtime (własne środowisko) zostanie wygasła w ciągu roku. Komunikat o wygaśnięciu jest wyświetlany w portalu ADF i na kliencie Integration Runtime (samodzielne) **90 dni** przed wygaśnięciem.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj [się z tematami dotyczącymi środowiska Integration Runtime w Azure Data Factory](./concepts-integration-runtime.md).

- Dowiedz się [, jak utworzyć własne środowisko Integration Runtime w Azure Portal](./create-self-hosted-integration-runtime.md).
