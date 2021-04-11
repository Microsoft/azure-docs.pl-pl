---
title: Rozwiązywanie problemów z Azure Monitor Application Insights dla języka Java
description: Dowiedz się, jak rozwiązywać problemy z agentem Java dla Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9bcd0ead2516b040a5a5aee4a7fae042a5f678a2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449991"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Przewodnik rozwiązywania problemów: Azure Monitor Application Insights dla języka Java

W tym artykule omówiono niektóre typowe problemy, które można napotkać podczas Instrumentacji aplikacji Java przy użyciu agenta Java dla Application Insights. Omówiono również procedurę rozwiązywania tych problemów. Application Insights to funkcja usługi Azure Monitor platform.

## <a name="check-the-self-diagnostic-log-file"></a>Sprawdź plik dziennika samoobsługowego

Domyślnie agent Java 3,0 dla Application Insights tworzy plik dziennika o nazwie `applicationinsights.log` w tym samym katalogu, w którym znajduje się `applicationinsights-agent-3.0.3.jar` plik.

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

Rejestrowanie jest przechwytywane tylko wtedy, gdy jest ono najpierw zgodne z poziomem skonfigurowanym dla struktury rejestrowania, a drugi, również spełnia poziom skonfigurowany dla Application Insights.

Na przykład jeśli struktura rejestrowania jest skonfigurowana do rejestrowania `WARN` (i powyżej) z pakietu `com.example` , a Application Insights jest skonfigurowana do przechwytywania `INFO` (i powyżej), wówczas Application Insights będzie przechwytywać tylko `WARN` (i powyżej) z pakietu `com.example` .

Najlepszym sposobem, aby sprawdzić, czy określone oświadczenie rejestrowania spełnia warunki skonfigurowanej progu rejestrowania, ma potwierdzić, że jest on wyświetlany w normalnym dzienniku aplikacji (np. pliku lub konsoli).

Należy również zauważyć, że jeśli obiekt wyjątku jest przekazana do rejestratora, komunikat dziennika (i szczegóły obiektu wyjątku) będzie wyświetlany w Azure Portal w `exceptions` tabeli zamiast `traces` tabeli.

Aby uzyskać więcej informacji, zobacz [konfigurację rejestrowania z autozbieraniem](./java-standalone-config.md#auto-collected-logging) .

## <a name="import-ssl-certificates"></a>Importuj certyfikaty SSL

Ta sekcja pomaga w rozwiązywaniu problemów i ewentualnym usunięciu wyjątków związanych z certyfikatami SSL podczas korzystania z agenta Java.

Poniżej znajdują się dwie różne ścieżki umożliwiające rozwiązanie tego problemu:
* Jeśli używany jest domyślny magazyn kluczy języka Java
* W przypadku korzystania z niestandardowego magazynu kluczy języka Java

Jeśli nie masz pewności, która ścieżka ma być zgodna, sprawdź, czy masz JVM ARG `-Djavax.net.ssl.trustStore=...` .
Jeśli _nie_ masz takiego JVMego argumentu, prawdopodobnie używasz domyślnego magazynu kluczy języka Java.
Jeśli jest _to_ JVM ARG, prawdopodobnie używasz niestandardowego magazynu kluczy, a argument JVM wskazuje na niestandardowy Magazyn kluczy.

### <a name="if-using-the-default-java-keystore"></a>Jeśli używany jest domyślny magazyn kluczy języka Java:

Zazwyczaj domyślnym magazynem kluczy języka Java będzie już wszystkie certyfikaty główne urzędu certyfikacji. Mogą jednak wystąpić pewne wyjątki, takie jak certyfikat punktu końcowego pozyskiwania może być podpisany przez inny certyfikat główny. Zalecamy wykonanie następujących trzech kroków w celu rozwiązania tego problemu:

1.  Sprawdź, czy certyfikat główny, który został użyty do podpisania Application Insights punktu końcowego, znajduje się już w domyślnym magazynie kluczy. Certyfikaty zaufanych urzędów certyfikacji domyślnie są przechowywane w programie `$JAVA_HOME/jre/lib/security/cacerts` . Aby wyświetlić listę certyfikatów w magazynie kluczy Java, użyj następującego polecenia:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Możesz przekierować dane wyjściowe do pliku tymczasowego, jak to (będzie można łatwo przeszukiwać później)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Po wyświetleniu listy certyfikatów wykonaj następujące [kroki](#steps-to-download-ssl-certificate) , aby pobrać certyfikat główny, który został użyty do podpisania Application Insights punktu końcowego.

    Po pobraniu certyfikatu Wygeneruj skrót SHA-1 dla certyfikatu przy użyciu poniższego polecenia:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Skopiuj wartość SHA-1 i sprawdź, czy ta wartość jest obecna w pliku "temp.txt" zapisanym wcześniej.  Jeśli nie można znaleźć wartości SHA-1 w pliku tymczasowym, oznacza to, że w domyślnym magazynie kluczy języka Java brakuje pobranego certyfikatu głównego.


3. Zaimportuj certyfikat główny do domyślnego magazynu kluczy języka Java przy użyciu następującego polecenia:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    W takim przypadku będzie
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Jeśli używasz niestandardowego magazynu kluczy Java:

Jeśli używasz niestandardowego magazynu kluczy Java, może być konieczne zaimportowanie głównych certyfikatów SSL (-y) Application Insights punktów końcowych.
Zalecamy wykonanie następujących dwóch kroków w celu rozwiązania tego problemu:
1. Wykonaj następujące [kroki](#steps-to-download-ssl-certificate) , aby pobrać certyfikat główny z punktu końcowego Application Insights.
2. Użyj następującego polecenia, aby zaimportować główny certyfikat SSL do niestandardowego magazynu kluczy Java:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Procedura pobierania certyfikatu SSL

1.  Otwórz ulubioną przeglądarkę i przejdź do adresu URL znajdującego się `IngestionEndpoint` w parametrach połączenia używanych do Instrumentacji aplikacji.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Zrzut ekranu, który pokazuje Application Insights parametry połączenia." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Wybierz ikonę **Wyświetl informacje o witrynie** (blokada) w przeglądarce, a następnie wybierz opcję **certyfikat** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Zrzut ekranu przedstawiający opcję certyfikatu w informacjach o lokacji." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  Zamiast pobierania certyfikatu "Leaf" należy pobrać certyfikat "root", jak pokazano poniżej. Później trzeba kliknąć "ścieżka certyfikatu" — > wybrać certyfikat główny — > kliknij pozycję "Wyświetl certyfikat". Spowoduje to wyświetlenie nowego menu certyfikatów i pobranie certyfikatu z menu Nowy.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania certyfikatu głównego." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Przejdź do karty **szczegóły** i wybierz pozycję **Kopiuj do pliku**.
5.  Wybierz przycisk **dalej** , a następnie wybierz pozycję **Base-64 encoded X. 509 (. CER)** , a następnie ponownie wybierz pozycję **dalej** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Zrzut ekranu Kreatora eksportu certyfikatów z wybranym formatem." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Określ plik, w którym chcesz zapisać certyfikat SSL. Następnie wybierz pozycję **dalej**  >  **Zakończ**. Powinien zostać wyświetlony komunikat "Eksport zakończył się pomyślnie".

> [!WARNING]
> Należy powtórzyć te kroki, aby pobrać nowy certyfikat przed wygaśnięciem bieżącego certyfikatu. Informacje o wygaśnięciu można znaleźć na karcie **szczegóły** w oknie dialogowym **certyfikat** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Zrzut ekranu pokazujący szczegóły certyfikatu protokołu SSL." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
