---
title: Wykonywanie operacji eksportowania przez wywoływanie $export polecenia w interfejsie API platformy Azure dla FHIR
description: W tym artykule opisano sposób eksportowania danych FHIR przy użyciu $export
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 74fe09895f49cc9f7c3cdf6b6c97c1624c3e9c0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839830"
---
# <a name="how-to-export-fhir-data"></a>Jak wyeksportować dane FHIR


Funkcja eksportu zbiorczego umożliwia eksportowanie danych z serwera FHIR na [specyfikację FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Przed rozpoczęciem korzystania z $export upewnij się, że jest skonfigurowany do korzystania z interfejsu API platformy Azure dla usługi FHIR. Aby skonfigurować ustawienia eksportu i utworzyć konto usługi Azure Storage, zapoznaj się z [stroną Konfigurowanie eksportu danych](configure-export-data.md).

## <a name="using-export-command"></a>Używanie $export polecenia

Po skonfigurowaniu interfejsu API platformy Azure dla usługi FHIR na potrzeby eksportu można użyć polecenia $export, aby wyeksportować dane z usługi. Dane będą przechowywane na koncie magazynu określonym podczas konfigurowania eksportu. Aby dowiedzieć się, jak wywołać polecenie $export na serwerze FHIR, Przeczytaj dokumentację dotyczącą [$Export specyfikacji](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Polecenie $export w interfejsie API platformy Azure dla FHIR przyjmuje opcjonalny parametr _ \_ kontenera_ , który określa kontener w skonfigurowanym koncie magazynu, w którym dane mają zostać wyeksportowane. Jeśli kontener jest określony, dane zostaną wyeksportowane do tego kontenera w nowym folderze o nazwie. Jeśli kontener nie zostanie określony, zostanie on wyeksportowany do nowego kontenera z losowo wygenerowaną nazwą. 

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Interfejs API platformy Azure dla usługi FHIR obsługuje $export na poziomie systemu, pacjenta i grupy. W przypadku eksportu grup eksportuje wszystkie powiązane zasoby, ale nie eksportują właściwości grupy.

> [!Note] 
> $export będzie eksportować duplikaty zasobów, jeśli zasób znajduje się w przedziale więcej niż jednego zasobu lub znajduje się w wielu grupach.

Ponadto sprawdzanie stanu eksportu za pośrednictwem adresu URL zwróconego przez nagłówek lokalizacji podczas kolejkowania jest obsługiwane wraz z anulowaniem rzeczywistego zadania eksportu.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób eksportowania FHIR zasobów przy użyciu polecenia $export. Następnie możesz przejrzeć nasze obsługiwane funkcje:
 
>[!div class="nextstepaction"]
>[Obsługiwane funkcje](fhir-features-supported.md)
