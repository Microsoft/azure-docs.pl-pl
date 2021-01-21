---
title: Rozwiązywanie problemów z Azure Monitor Application Insights dla języka Java
description: Dowiedz się, jak rozwiązywać problemy z agentem Java dla Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 788eea17cabbea46578d0f59919ae95a59f2223f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625351"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Przewodnik rozwiązywania problemów: Azure Monitor Application Insights dla języka Java

W tym artykule omówiono niektóre typowe problemy, które można napotkać podczas Instrumentacji aplikacji Java przy użyciu agenta Java dla Application Insights. Omówiono również procedurę rozwiązywania tych problemów. Application Insights to funkcja usługi Azure Monitor platform.

## <a name="check-the-self-diagnostic-log-file"></a>Sprawdź plik dziennika samoobsługowego

Domyślnie agent Java 3,0 dla Application Insights tworzy plik dziennika o nazwie `applicationinsights.log` w tym samym katalogu, w którym znajduje się `applicationinsights-agent-3.0.2.jar` plik.

Ten plik dziennika jest pierwszym miejscem do sprawdzenia, czy występują wskazówki dotyczące wszelkich problemów, które mogą wystąpić.

## <a name="jvm-fails-to-start"></a>Nie można uruchomić JVM

Jeśli JVM nie powiedzie się z "błąd podczas otwierania pliku zip lub manifestu JAR", spróbuj ponownie pobrać plik JAR agenta, ponieważ mógł zostać uszkodzony podczas transferu plików.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Uaktualnianie z Application Insights zestawu SDK Java 2. x

Jeśli używasz już Application Insights zestawu SDK języka Java 2. x w aplikacji, możesz go nadal używać. Program Java 3,0 Agent wykryje go. Aby uzyskać więcej informacji, zobacz [Uaktualnianie z zestawu SDK Java 2. x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Uaktualnianie z programu Application Insights Java 3,0 Preview

Jeśli uaktualniasz agenta programu Java 3,0 w wersji zapoznawczej, uważnie Przejrzyj wszystkie [Opcje konfiguracji](./java-standalone-config.md) . Struktura JSON została całkowicie zmieniona w wydaniu ogólnie dostępnej wersji 3,0.

Te zmiany obejmują:

-  Nazwa pliku konfiguracji została zmieniona z `ApplicationInsights.json` na `applicationinsights.json` .
-  `instrumentationSettings`Węzeł nie jest już obecny. Cała zawartość w programie `instrumentationSettings` jest przenoszona do poziomu głównego. 
-  Węzły konfiguracji, takie jak `sampling` ,, `jmxMetrics` `instrumentation` i `heartbeat` są przenoszone z `preview` do poziomu głównego.

## <a name="some-logging-is-not-auto-collected"></a>Niektóre funkcje rejestrowania nie są zbierane w sposób autozbierany

Rejestrowanie jest przechwytywane tylko wtedy, gdy najpierw spełni skonfigurowany próg dla struktur rejestrowania, a druga również spełnia wartość Application Insights skonfigurowany próg.

Najlepszym sposobem, aby sprawdzić, czy określone oświadczenie rejestrowania spełnia warunki skonfigurowanej progu rejestrowania, ma potwierdzić, że jest on wyświetlany w normalnym dzienniku aplikacji (np. pliku lub konsoli).

Aby uzyskać więcej informacji, zobacz [konfigurację rejestrowania z autozbieraniem](./java-standalone-config.md#auto-collected-logging) .

## <a name="import-ssl-certificates"></a>Importuj certyfikaty SSL

Jeśli używasz domyślnego magazynu kluczy języka Java, będzie on miał już wszystkie certyfikaty główne urzędu certyfikacji. Nie trzeba importować więcej certyfikatów SSL.

Jeśli używasz niestandardowego magazynu kluczy Java, może być konieczne zaimportowanie do niego certyfikatów protokołu SSL Application Insights Endpoint.

### <a name="key-terminology"></a>Terminologia klucza
*Magazyn* kluczy jest repozytorium certyfikatów, kluczy publicznych i kluczy prywatnych. Zwykle dystrybucje zestawu Java Development Kit mają plik wykonywalny do zarządzania nimi: `keytool` .

Poniższy przykład to proste polecenie do zaimportowania certyfikatu SSL do magazynu kluczy:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Procedura pobierania i dodawania certyfikatu SSL

1.  Otwórz ulubioną przeglądarkę i przejdź do adresu URL znajdującego się `IngestionEndpoint` w parametrach połączenia używanych do Instrumentacji aplikacji.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Zrzut ekranu, który pokazuje Application Insights parametry połączenia.":::

2.  Wybierz ikonę **Wyświetl informacje o witrynie** (blokada) w przeglądarce, a następnie wybierz opcję **certyfikat** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Zrzut ekranu przedstawiający opcję certyfikatu w informacjach o lokacji.":::

3.  Przejdź do karty **szczegóły** i wybierz pozycję **Kopiuj do pliku**.
4.  Wybierz przycisk **dalej** , a następnie wybierz pozycję **Base-64 encoded X. 509 (. CER)** , a następnie ponownie wybierz pozycję **dalej** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Zrzut ekranu Kreatora eksportu certyfikatów z wybranym formatem.":::

5.  Określ plik, w którym chcesz zapisać certyfikat SSL. Następnie wybierz pozycję **dalej**  >  **Zakończ**. Powinien zostać wyświetlony komunikat "Eksport zakończył się pomyślnie".
6.  Po uzyskaniu certyfikatu czas na zaimportowanie certyfikatu do magazynu kluczy języka Java. Użyj [poprzedniego polecenia](#key-terminology) , aby zaimportować certyfikaty.

> [!WARNING]
> Należy powtórzyć te kroki, aby pobrać nowy certyfikat przed wygaśnięciem bieżącego certyfikatu. Informacje o wygaśnięciu można znaleźć na karcie **szczegóły** w oknie dialogowym **certyfikat** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Zrzut ekranu pokazujący szczegóły certyfikatu protokołu SSL.":::
