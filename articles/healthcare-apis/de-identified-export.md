---
title: Eksportowanie niezidentyfikowanych danych (wersja zapoznawcza) dla usługi Azure API for FHIR
description: W tym artykule opisano sposób konfigurowania i używania wyeksportowanego eksportu
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: bdbab0e032764d07119402686051d391376cb913
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843999"
---
# <a name="exporting-de-identified-data-preview"></a>Eksportowanie niezidentyfikowanych danych (wersja zapoznawcza)

> [!Note] 
> Wyniki przy użyciu wyznaczonego eksportu różnią się w zależności od czynników, takich jak dane zostały zwrócone, i funkcji wybranych przez klienta. Firma Microsoft nie jest w stanie oszacować wyznaczonych danych wyjściowych eksportu lub określić akceptowalność dla przypadków użycia klienta i wymagań dotyczących zgodności. De-zidentyfikowany eksport nie jest gwarantowany do spełnienia wymogów szczególnych, prawnych lub wymagań dotyczących zgodności.

Polecenia $export można również użyć do eksportowania wyznaczonych danych z serwera FHIR. Używa aparatu zachowywanie anonimowości z [FHIR Tools for zachowywanie anonimowości](https://github.com/microsoft/FHIR-Tools-for-Anonymization)i pobiera szczegóły konfiguracji zachowywanie anonimowości w parametrach zapytania. Można utworzyć własny plik konfiguracji zachowywanie anonimowości lub użyć [przykładowego pliku konfiguracji](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) dla metody HIPAA "Safe przystani" jako punktu początkowego. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Parametr zapytania            | Przykład |Opcjonalność| Opis|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsna|Wymagane dla deidentyfikowanego eksportu |Nazwa pliku konfiguracji. W [tym miejscu](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)Sprawdź format pliku konfiguracji. Ten plik powinien być przechowywany w kontenerze o nazwie **zachowywanie anonimowości** w ramach tego samego konta usługi Azure Storage, które jest skonfigurowane jako lokalizacja eksportowania. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Opcjonalne dla deidentyfikowanego eksportu|Jest to element ETag pliku konfiguracji. Element ETag można uzyskać przy użyciu Eksploratora usługi Azure Storage z właściwości obiektu BLOB|

> [!IMPORTANT]
> Zarówno eksport pierwotny, jak i deidentyfikowane zapisy eksportu do tego samego konta usługi Azure Storage określone jako część konfiguracji eksportu. Zalecane jest używanie różnych kontenerów odpowiadających różnym wyznaczonym konfiguracjom konfiguracji i zarządzania dostępem użytkowników na poziomie kontenera.