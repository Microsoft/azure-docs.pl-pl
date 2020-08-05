---
title: Rozwiązywanie problemów Azure Data Factory UX
description: Dowiedz się, jak rozwiązywać problemy dotyczące Azure Data Factory środowiska użytkownika.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87568009"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Rozwiązywanie problemów dotyczących Azure Data Factory środowiska użytkownika
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów dotyczących środowiska Azure Data Factory.

## <a name="adf-ux-not-loading"></a>Nie załadowanie środowiska ADF

> [!NOTE]
> Azure Data Factory środowisko użytkownika oficjalnie obsługuje przeglądarki Microsoft Edge i Google Chrome. Korzystanie z innych przeglądarek sieci Web może prowadzić do nieoczekiwanego lub nieudokumentowanego zachowania.

### <a name="third-party-cookies-blocked"></a>Zablokowane pliki cookie innych firm

ŚRODOWISKO APD ADF używa plików cookie przeglądarki do utrwalania sesji użytkownika i włączania interaktywnych środowisk programistycznych i monitorowania. Możliwe, że przeglądarka zablokuje pliki cookie innych firm, ponieważ używasz sesji incognito lub włączono blokadę AD. Zablokowanie plików cookie innych firm może powodować problemy podczas ładowania portalu, takie jak przekierowywanie do pustej strony https://adf.azure.com/accesstoken.html lub otrzymywanie komunikatu ostrzegawczego informującego, że pliki cookie innych firm są zablokowane. Aby rozwiązać ten problem, Włącz opcje plików cookie innych firm w przeglądarce, wykonując następujące czynności:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Zezwalaj na wszystkie pliki cookie

1. Odwiedź stronę **Chrome://Settings/cookies** w przeglądarce.
1. Wybierz pozycję **Zezwalaj na wszystkie pliki cookie** opcja ![ Chrome-Allow-All-cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Odśwież środowisko ADF i spróbuj ponownie.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Zezwalaj tylko na korzystanie z plików cookie przez środowisko ADF
Jeśli nie chcesz zezwalać na wszystkie pliki cookie, możesz opcjonalnie zezwolić na środowisko ADF:
1. Odwiedź stronę **Chrome://Settings/cookies**.
1. Kliknij przycisk **Dodaj** w obszarze **Lokacje, które mogą zawsze używać opcji plików cookie** ![ Dodaj środowisko ADF do dozwolonych witryn](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Dodaj witrynę **ADF.Azure.com** , zaznacz opcję **wszystkie pliki cookie** i Zapisz. ![Zezwalaj na wszystkie pliki cookie z witryny środowiska APD usługi ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Odśwież środowisko ADF i spróbuj ponownie.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Odwiedź stronę **Edge://Settings/Content/cookies** w przeglądarce.
1. Upewnij się, że **Lokacje do zapisu i odczytu danych cookie** są włączone i że opcja **Blokuj pliki cookie innych firm** jest wyłączona ![ Zezwalaj na wszystkie pliki cookie w przeglądarce Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Odśwież środowisko ADF i spróbuj ponownie.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Zezwalaj tylko na korzystanie z plików cookie przez środowisko ADF

Jeśli nie chcesz zezwalać na wszystkie pliki cookie, możesz opcjonalnie zezwolić na środowisko ADF:

1. Odwiedź stronę **Edge://Settings/Content/cookies**.
1. W obszarze **Zezwól** kliknij pozycję **Dodaj** i Dodaj witrynę **ADF.Azure.com** . ![Dodawanie środowiska ADF z dozwolonymi lokacjami](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Odśwież środowisko ADF i spróbuj ponownie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
* [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/)
* [Strona pytania&pytań i odpowiedzi](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
