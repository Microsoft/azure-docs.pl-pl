---
title: Eksportowanie niezidentyfikowanych danych (wersja zapoznawcza) dla usługi Azure API for FHIR
description: W tym artykule opisano sposób konfigurowania i używania wyeksportowanego eksportu
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: 60a2a41a8005e8bd0fbc313c9a177d54df6dac5e
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627167"
---
# <a name="exporting-de-identified-data-preview"></a>Eksportowanie niezidentyfikowanych danych (wersja zapoznawcza)

> [!Note] 
> Wyniki przy użyciu wyznaczonego eksportu różnią się w zależności od czynników, takich jak dane zostały zwrócone, i funkcji wybranych przez klienta. Firma Microsoft nie jest w stanie oszacować wyznaczonych danych wyjściowych eksportu lub określić akceptowalność dla przypadków użycia klienta i wymagań dotyczących zgodności. De-zidentyfikowany eksport nie jest gwarantowany do spełnienia wymogów szczególnych, prawnych lub wymagań dotyczących zgodności.

Polecenia $export można również użyć do eksportowania wyznaczonych danych z serwera FHIR. Używa aparatu zachowywanie anonimowości z [FHIR Tools for zachowywanie anonimowości](https://github.com/microsoft/FHIR-Tools-for-Anonymization)i pobiera szczegóły konfiguracji zachowywanie anonimowości w parametrach zapytania. Można utworzyć własny plik konfiguracji zachowywanie anonimowości lub użyć [przykładowego pliku konfiguracji](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) dla metody HIPAA "Safe przystani" jako punktu początkowego. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> Teraz interfejs API platformy Azure dla usługi FHIR obsługuje tylko niezidentyfikowany eksport na poziomie systemu ($export).

|Parametr zapytania            | Przykład |Opcjonalność| Opis|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsna|Wymagane dla deidentyfikowanego eksportu |Nazwa pliku konfiguracji. W [tym miejscu](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)Sprawdź format pliku konfiguracji. Ten plik powinien być przechowywany w kontenerze o nazwie **zachowywanie anonimowości** w ramach tego samego konta usługi Azure Storage, które jest skonfigurowane jako lokalizacja eksportowania. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Opcjonalne dla deidentyfikowanego eksportu|Jest to element ETag pliku konfiguracji. Element ETag można uzyskać przy użyciu Eksploratora usługi Azure Storage z właściwości obiektu BLOB|

> [!IMPORTANT]
> Zarówno eksport pierwotny, jak i deidentyfikowane zapisy eksportu do tego samego konta usługi Azure Storage określone jako część konfiguracji eksportu. Zalecane jest używanie różnych kontenerów odpowiadających różnym wyznaczonym konfiguracjom konfiguracji i zarządzania dostępem użytkowników na poziomie kontenera.