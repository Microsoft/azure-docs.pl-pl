---
title: Rozwiązywanie problemów — Azure Monitor Application Insights Java
description: Rozwiązywanie problemów z Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855661"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Rozwiązywanie problemów z Azure Monitor Application Insights Java

W tym artykule omówiono niektóre typowe problemy, które użytkownik może określić podczas Instrumentacji aplikacji Java przy użyciu agenta Java, a także kroki umożliwiające rozwiązanie tych problemów.

## <a name="self-diagnostic-log-file"></a>Plik dziennika samodiagnostyki

Domyślnie Application Insights Java 3,0 będzie generować plik dziennika o nazwie w tym `applicationinsights.log` samym katalogu, w którym `applicationinsights-agent-3.0.0.jar` znajduje się plik.

Ten plik dziennika jest pierwszym miejscem do sprawdzenia, czy występują wskazówki dotyczące wszelkich problemów, które mogą wystąpić.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Uaktualnianie z programu Application Insights Java 2. x SDK

Zobacz [Uaktualnianie z zestawu SDK 2. x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Uaktualnianie z wersji zapoznawczej 3,0

W przypadku uaktualniania z wersji zapoznawczej programu 3,0 należy uważnie przejrzeć wszystkie [Opcje konfiguracji](./java-standalone-config.md) , ponieważ struktura JSON została całkowicie zmieniona w wersji 3,0 ga.

Te zmiany obejmują:

1.  Sama nazwa pliku konfiguracji została zmieniona z `ApplicationInsights.json` na `applicationinsights.json` .
2.  `instrumentationSettings`Węzeł nie jest już obecny. Cała zawartość w programie `instrumentationSettings` jest przenoszona do poziomu głównego. 
3.  Węzły konfiguracji, takie jak `sampling` , `jmxMetrics` `instrumentation` i `heartbeat` są przenoszone z `preview` do poziomu głównego.

## <a name="ssl-certificate-issues"></a>Problemy z certyfikatem SSL

Jeśli używasz domyślnego magazynu kluczy języka Java, będzie on miał już wszystkie certyfikaty główne urzędu certyfikacji i nie należy importować żadnych dalszych certyfikatów SSL.

Jeśli używasz niestandardowego magazynu kluczy Java, może być konieczne zaimportowanie do niego certyfikatów protokołu SSL programu Application Insights Endpoint.

### <a name="some-key-terminology"></a>Część najważniejszej terminologii:
*Magazyn* kluczy to repozytorium certyfikatów, klucze publiczne i prywatne. Zazwyczaj dystrybucje JDK mają plik wykonywalny do zarządzania nimi — `keytool` .

Poniższy przykład to proste polecenie do zaimportowania certyfikatu SSL do magazynu kluczy:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Procedura pobierania i dodawania certyfikatu SSL:

1.  Otwórz ulubioną przeglądarkę i przejdź do adresu URL znajdującego się `IngestionEndpoint` w parametrach połączenia używanych do Instrumentacji aplikacji, jak pokazano poniżej

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights parametry połączenia":::

2.  Kliknij ikonę "Wyświetl informacje o witrynie" (Zablokuj) w przeglądarce i kliknij opcję "certyfikat", jak pokazano poniżej

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Przechwytywanie certyfikatu SSL":::

3.  Przejdź do karty szczegóły, a następnie kliknij pozycję Kopiuj do pliku.
4.  Kliknij przycisk Dalej i wybierz pozycję "Base-64 encoded X. 509 (. CER) "format i wybierz Dalej.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="ExportWizard certyfikatu SSL":::

5.  Określ plik, dla którego chcesz zapisać certyfikat protokołu SSL. Na koniec kliknij przycisk Dalej i Zakończ. Powinien zostać wyświetlony komunikat "Eksport zakończył się pomyślnie".
6.  Po otrzymaniu certyfikatu zaimportuj certyfikat do magazynu kluczy języka Java. Użyj powyższego [polecenia](#some-key-terminology) , aby zaimportować certyfikaty.

> [!WARNING]
> Należy powtórzyć te kroki, aby pobrać nowy certyfikat przed wygaśnięciem bieżącego certyfikatu. Informacje o wygaśnięciu można znaleźć na karcie "Szczegóły" okna podręcznego certyfikatu, jak pokazano poniżej.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Szczegóły certyfikatu SSL":::
