---
title: Wykonywanie operacji eksportowania przez wywoływanie $export polecenia w interfejsie API platformy Azure dla FHIR
description: W tym artykule opisano sposób konfigurowania i używania wyeksportowanego eksportu
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482321"
---
# <a name="how-to-export-fhir-data"></a>Jak wyeksportować dane FHIR

Aby skonfigurować ustawienia eksportu i utworzyć konto usługi Azure Storage, zapoznaj się z [tym](configure-export-data.md)miejscem.

## <a name="exporting-fhir-resources-using-export-command"></a>Eksportowanie zasobów FHIR za pomocą polecenia $export

Po skonfigurowaniu usługi Azure API for FHIR na potrzeby eksportu można teraz użyć polecenia $export, aby wyeksportować dane z usługi do konta magazynu określonego podczas konfigurowania funkcji eksportowania. Aby dowiedzieć się, jak wywołać polecenie $export na serwerze FHIR, Przeczytaj dokumentację dotyczącą $export Specyfikacja pod adresem [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

Polecenie $export w interfejsie API platformy Azure dla FHIR przyjmuje opcjonalny parametr _ \_ Conatiner_ , którego można użyć do określenia kontenera w skonfigurowanym koncie magazynu, do którego mają zostać wyeksportowane dane.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Należy pamiętać, że obecnie usługa Azure API for FHIR obsługuje wyłącznie eksport na poziomie systemu, jak zdefiniowano w specyfikacji $export pod adresem [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Ponadto tylko _ \_ od momentu_ , gdy parametr zapytania jest obecnie obsługiwany.

## <a name="exporting-de-identified-data-preview"></a>Eksportowanie niezidentyfikowanych danych (wersja zapoznawcza)

Polecenia $export można również użyć do eksportowania wyznaczonych danych z serwera FHIR. Używa aparatu zachowywanie anonimowości z [FHIR Tools for zachowywanie anonimowości](https://github.com/microsoft/FHIR-Tools-for-Anonymization)i pobiera szczegóły konfiguracji zachowywanie anonimowości w parametrach zapytania. Można utworzyć własny plik konfiguracji zachowywanie anonimowości lub użyć [przykładowego pliku konfiguracji](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) dla metody HIPAA "Safe przystani" jako punktu początkowego. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Parametr zapytania            | Przykład |Opcjonalność| Opis|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsna|Wymagane dla deidentyfikowanego eksportu |Nazwa pliku konfiguracji. W [tym miejscu](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)Sprawdź format pliku konfiguracji. Ten plik powinien być przechowywany w kontenerze o nazwie **zachowywanie anonimowości** w ramach tego samego konta usługi Azure Storage, które jest skonfigurowane jako lokalizacja eksportowania. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Opcjonalne dla deidentyfikowanego eksportu|Jest to element ETag pliku konfiguracji. Można uzyskać element ETag przy użyciu Eksplorator usługi Azure Storage z właściwości obiektu BLOB|

> [!IMPORTANT]
> Należy zauważyć, że zarówno eksport pierwotny, jak i deidentyfikowane zapisy eksportu do tego samego konta usługi Azure Storage określone jako część konfiguracji eksportu. Zalecane jest używanie różnych kontenerów odpowiadających różnym wyznaczonym konfiguracjom konfiguracji i zarządzania dostępem użytkowników na poziomie kontenera.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje na temat eksportowania FHIR zasobów przy użyciu polecenia $export, w tym z wyznaczonymi danymi. Następnie można skonfigurować dane eksportu:
 
>[!div class="nextstepaction"]
>[Konfigurowanie eksportu danych](configure-export-data.md)
