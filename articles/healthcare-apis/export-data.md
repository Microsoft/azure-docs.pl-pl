---
title: Wykonywanie operacji eksportowania przez wywoływanie $export polecenia w interfejsie API platformy Azure dla FHIR
description: W tym artykule opisano sposób eksportowania danych FHIR przy użyciu $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 2/19/2021
ms.author: cavoeg
ms.openlocfilehash: 675030ac47cb26e817a9ef7ee51999f25020f292
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712706"
---
# <a name="how-to-export-fhir-data"></a>Jak wyeksportować dane FHIR


Funkcja eksportu zbiorczego umożliwia eksportowanie danych z serwera FHIR na [specyfikację FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Przed rozpoczęciem korzystania z $export upewnij się, że jest skonfigurowany do korzystania z interfejsu API platformy Azure dla usługi FHIR. Aby skonfigurować ustawienia eksportu i utworzyć konto usługi Azure Storage, zapoznaj się z [stroną Konfigurowanie eksportu danych](configure-export-data.md).

## <a name="using-export-command"></a>Używanie $export polecenia

Po skonfigurowaniu interfejsu API platformy Azure dla usługi FHIR na potrzeby eksportu można użyć polecenia $export, aby wyeksportować dane z usługi. Dane będą przechowywane na koncie magazynu określonym podczas konfigurowania eksportu. Aby dowiedzieć się, jak wywołać $export polecenie na serwerze FHIR, Przeczytaj dokumentację dotyczącą [specyfikacji HL7 FHIR $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Interfejs API platformy Azure dla usługi FHIR obsługuje $export na następujących poziomach:
* [System](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Pacjent](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupa pacjentów *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) — interfejs API platformy Azure dla FHIR eksportuje wszystkie powiązane zasoby, ale nie eksportuje cech grupy: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Podczas eksportowania danych jest tworzony oddzielny plik dla każdego typu zasobu. Aby upewnić się, że eksportowane pliki nie staną się zbyt duże, utworzymy nowy plik po rozmiar pojedynczego wyeksportowanego pliku, który będzie większy niż 64 MB. W efekcie można uzyskać wiele plików dla każdego typu zasobu, który zostanie wyliczony (tj. pacjenta-1. ndjson, pacjent-2. ndjson). 


> [!Note] 
> `Patient/$export` i `Group/[ID]/$export` mogą eksportować duplikaty zasobów, jeśli zasób znajduje się w przedziale więcej niż jednego zasobu lub znajduje się w wielu grupach.

Ponadto sprawdzanie stanu eksportu za pośrednictwem adresu URL zwróconego przez nagłówek lokalizacji podczas kolejkowania jest obsługiwane wraz z anulowaniem rzeczywistego zadania eksportu.



## <a name="settings-and-parameters"></a>Ustawienia i parametry

### <a name="headers"></a>Nagłówki
Istnieją dwa wymagane parametry nagłówka, które muszą zostać ustawione dla $export zadań. Wartości są definiowane przez bieżącą [specyfikację $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Accept** -Application/fhir + JSON
* **Preferuj** — odpowiedź — Async

### <a name="query-parameters"></a>Parametry zapytania
Interfejs API platformy Azure dla usługi FHIR obsługuje następujące parametry zapytania. Wszystkie te parametry są opcjonalne:

|Parametr zapytania        | Zdefiniowano w specyfikacji FHIR?    |  Opis|
|------------------------|---|------------|
| \_outputFormat | Tak | Obecnie program obsługuje trzy wartości do dopasowania do specyfikacji FHIR: Application/FHIR + ndjson, Application/ndjson lub tylko ndjson. Wszystkie zadania eksportu zostaną zwrócone `ndjson` i przeniesiona wartość nie ma wpływu na zachowanie kodu. |
| \_Fire | Tak | Umożliwia eksportowanie tylko zasobów zmodyfikowanych od czasu podanego |
| \_Wprowadź | Tak | Pozwala określić, które typy zasobów zostaną uwzględnione. Na przykład \_ wpisz = pacjent zwróci tylko zasoby pacjenta|
| \_typefilter | Tak | Aby zażądać bardziej szczegółowego filtrowania, można użyć \_ TypeFilter wraz z \_ parametrem typu. Wartość parametru _typeFilter jest rozdzielaną przecinkami listą zapytań FHIR, które bardziej ograniczają wyniki |
| \_wbudowane | Nie |  Określa kontener w skonfigurowanym koncie magazynu, w którym mają zostać wyeksportowane dane. Jeśli kontener jest określony, dane zostaną wyeksportowane do tego kontenera w nowym folderze o nazwie. Jeśli kontener nie zostanie określony, zostanie on wyeksportowany do nowego kontenera przy użyciu sygnatury czasowej i identyfikatora zadania. |

## <a name="secure-export-to-azure-storage"></a>Bezpieczny eksport do usługi Azure Storage

Interfejs API platformy Azure dla usługi FHIR obsługuje operację bezpiecznego eksportowania. Jedną z opcji wykonywania bezpiecznego eksportu jest umożliwienie określonym adresom IP skojarzonym z interfejsem API platformy Azure dla FHIR dostępu do konta usługi Azure Storage. Konfiguracja różni się w zależności od tego, czy konto magazynu znajduje się w tej samej lokalizacji, czy w innym miejscu niż interfejs API platformy Azure dla FHIR.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Gdy konto usługi Azure Storage znajduje się w innym regionie

Wybierz blok sieć konta usługi Azure Storage z portalu. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Ustawienia sieci usługi Azure Storage." lightbox="media/export-data/storage-networking.png":::
   
Wybierz pozycję "wybrane sieci" i określ adres IP w polu **zakres adresów** w obszarze zapory \| Dodaj zakresy adresów IP, aby zezwolić na dostęp z Internetu lub sieci lokalnych. Adres IP można znaleźć z poniższej tabeli dla regionu platformy Azure, w którym Zainicjowano obsługę interfejsu API platformy Azure dla usługi FHIR.

|**Region świadczenia usługi Azure**         |**Publiczny adres IP** |
|:----------------------|:-------------------|
| Australia Wschodnia       | 20.53.44.80       |
| Kanada Środkowa       | 20.48.192.84      |
| Central US           | 52.182.208.31     |
| East US              | 20.62.128.148     |
| Wschodnie stany USA 2            | 20.49.102.228     |
| Wschodnie stany USA 2 — EUAP       | 20.39.26.254      |
| Niemcy Północne        | 51.116.51.33      |
| Niemcy Środkowo-Zachodnie | 51.116.146.216    |
| Japonia Wschodnia           | 20.191.160.26     |
| Korea Środkowa        | 20.41.69.51       |
| Północno-środkowe stany USA     | 20.49.114.188     |
| Europa Północna         | 52.146.131.52     |
| Północna Republika Południowej Afryki   | 102.133.220.197   |
| South Central US     | 13.73.254.220     |
| Southeast Asia       | 23.98.108.42      |
| Szwajcaria Północna    | 51.107.60.95      |
| Południowe Zjednoczone Królestwo             | 51.104.30.170     |
| Zachodnie Zjednoczone Królestwo              | 51.137.164.94     |
| Zachodnio-środkowe stany USA      | 52.150.156.44     |
| West Europe          | 20.61.98.66       |
| Zachodnie stany USA 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Gdy konto usługi Azure Storage znajduje się w tym samym regionie

Proces konfiguracji jest taki sam jak powyżej, ale zamiast niego jest używany określony zakres adresów IP w formacie CIDR, 100.64.0.0/10. Należy określić, dlaczego zakres adresów IP, który obejmuje 100.64.0.0 – 100.127.255.255, musi być określony, ponieważ rzeczywisty adres IP używany przez usługę różni się, ale będzie znajdować się w zakresie, dla każdego żądania $export.

> [!Note] 
> Możliwe jest użycie w zamian prywatnego adresu IP z zakresu 10.0.2.0/24. W takim przypadku operacja $export nie powiedzie się. Można ponowić próbę żądania $export, ale nie ma gwarancji, że adres IP w zakresie 100.64.0.0/10 zostanie użyty następnym razem. Jest to znane zachowanie sieciowe według projektu. Alternatywą jest skonfigurowanie konta magazynu w innym regionie.
    
## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób eksportowania FHIR zasobów przy użyciu polecenia $export. Następnie Dowiedz się, jak eksportować dane z wyznaczonymi danymi:
 
>[!div class="nextstepaction"]
>[Eksportowanie wyznaczonych danych](de-identified-export.md)
