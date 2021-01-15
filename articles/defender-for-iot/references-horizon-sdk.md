---
title: Zestaw Horizon SDK
titleSuffix: Azure Defender for IoT
description: Zestaw horyzont SDK umożliwia deweloperom platformy Azure Defender projektowanie wtyczek do desektorów, które dekodują ruch sieciowy, aby można było go przetwarzać przez zautomatyzowane usługi Defender dla programów do analizy sieci IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: d6105f65508eff59164246020d9a3f286b68c5a1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210652"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Odsektora bezwłasnościowego protokołu

Horyzont to otwarte środowisko programistyczne (Node) służące do zabezpieczania urządzeń IoT i ICS z działającymi protokołami.

To środowisko udostępnia następujące rozwiązania dla klientów i partnerów technologicznych:

- Nieograniczone, pełne wsparcie dla wspólnych, własnościowych protokołów lub protokołów, które odbiegają od dowolnego standardu. 

- Nowy poziom elastyczności i zakresu rozwoju DPI.

- Narzędzie, które wykładniczo rozszerza niewidoczność i kontrolę, bez konieczności uaktualniania wersji platformy usługi IoT.

- Bezpieczeństwo umożliwiające programowanie własnościowe bez ujawniania poufnych informacji.

Zestaw horyzont SDK umożliwia deweloperom platformy Azure Defender projektowanie wtyczek do desektorów, które dekodują ruch sieciowy, aby można było go przetwarzać przez zautomatyzowane usługi Defender dla programów do analizy sieci IoT.

Odsektory protokołu są opracowywane jako zewnętrzne wtyczki i są zintegrowane z szeroką gamy usługi Defender for IoT. Na przykład usługi, które zapewniają możliwości monitorowania, tworzenia alertów i raportowania.

## <a name="secure-development-environment"></a>Bezpieczne środowisko programistyczne 

Horyzont Node umożliwia programowanie niestandardowych lub własnościowych protokołów, które nie mogą być udostępniane poza organizacją. Na przykład ze względu na przepisy prawne lub zasady firmowe.

Opracowywanie wtyczek do rozsektoru bez: 

- ujawnienie wszelkich własnościowych informacji o sposobie definiowania protokołów.

- Udostępnianie dowolnych poufnych PCAPsów.

- naruszanie przepisów dotyczących zgodności.

## <a name="customization-and-localization"></a>Dostosowywanie i lokalizacja  

Zestaw SDK obsługuje różne opcje dostosowywania, w tym:

  - Tekst dla kodów funkcji. 

  - Pełny tekst lokalizacji dla alertów, zdarzeń i parametrów protokołu. Aby uzyskać więcej informacji, zobacz [Tworzenie mapowania plików (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Wyświetlanie w pełni zlokalizowanych alertów.":::

## <a name="horizon-architecture"></a>Architektura horyzontu

Model architektoniczny obejmuje trzy warstwy produktu.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Warstwa platformy usługi Defender for IoT

Umożliwia natychmiastowe integrację i monitorowanie niestandardowych wtyczek niezwiązanych z sektorem w usłudze Defender for IoT bez konieczności uaktualniania wersji platformy Defender for IoT.

## <a name="defender-for-iot-services-layer"></a>Warstwa usługi Defender for IoT

Każda usługa jest zaprojektowana jako potok, oddzielona od określonego protokołu, co umożliwia wydajniejsze i niezależne programowanie.

Każda usługa jest zaprojektowana jako potok, oddzielona od określonego protokołu. Usługi nasłuchują ruchu na potoku. Współpracują z danymi wtyczki i ruch przechwytywany przez czujniki, aby indeksować wdrożone protokoły i analizować ładunek ruchu oraz zapewnić wydajniejsze i niezależne projektowanie.

## <a name="custom-dissector-layer"></a>Niestandardowa warstwa odsektora 

Umożliwia tworzenie wtyczek przy użyciu zestawu SDK usługi Defender for IoT (w tym implementacji języka C++ i konfiguracji JSON): 

- Zdefiniuj sposób identyfikowania protokołu

- Zdefiniuj sposób mapowania pól, które mają zostać wyodrębnione z ruchu, i Wyodrębnij je 

- Definiowanie sposobu integrowania z usługą Defender dla usług IoT

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Wbudowane warstwy.":::

Usługa Defender for IoT oferuje podstawowe sektory dla wspólnych protokołów. Można skompilować swoje sektory na podstawie tych protokołów.

## <a name="before-you-begin"></a>Przed rozpoczęciem

## <a name="what-this-sdk-contains"></a>Co to jest zestaw SDK 

Ten zestaw zawiera pliki nagłówkowe, które są związane z programowaniem. Proces opracowywania wymaga podstawowych kroków i opcjonalnych zaawansowanych kroków opisanych w tym zestawie SDK.

Skontaktuj się z <support@cyberx-labs.com> informacjami dotyczącymi uzyskiwania plików nagłówkowych i innych zasobów.

## <a name="about-the-environment-and-setup"></a>Informacje o środowisku i konfiguracji 

### <a name="requirements"></a>Wymagania 

- Preferowanym środowiskiem programistycznym jest system Linux. Jeśli tworzysz środowisko w środowisku systemu Windows, rozważ użycie maszyny wirtualnej z systemem Linux.

- W przypadku procesu kompilacji Użyj programu w wersji 7.4.0 lub nowszej. Użyj dowolnej klasy standardowej z STDLIB, która jest obsługiwana w języku C++ 17.

- Usługa Defender dla IoT w wersji 3,0 lub nowszej.

### <a name="process"></a>Proces

1. [Pobierz](https://www.eclipse.org/) środowisko IDE w języku zaćmieniym dla deweloperów C/C++. Możesz użyć dowolnego innego środowiska IDE. Ten dokument przeprowadzi Cię przez proces konfiguracji przy użyciu środowiska IDE.

1. Po uruchomieniu funkcji Zastąp środowisko IDE i skonfiguruj obszar roboczy (gdzie będą przechowywane projekty) Naciśnij **kombinację klawiszy CTRL + n** i utwórz go jako projekt języka C++.

1. Na następnym ekranie Ustaw nazwę protokołu, który chcesz opracować, i wybierz typ projektu jako `Shared Library` i `AND Linux GCC` .

1. Edytowanie właściwości projektu, w obszarze ustawień narzędzia ustawienia **kompilacji C/C++** ustawienia narzędzi w programie w ramach  >    >    >  **kompilatora C++ kompilator**  >  **inne**  >  **niezależny kod położenia**.

1. Wklej przykładowe kody otrzymane z zestawem SDK i skompiluj je.

1. Dodaj artefakty (biblioteki, config.jsw i metadanych) do pliku tar. gz, a następnie Zmień rozszerzenie pliku na \<XXX> . HDP, gdzie is to \<XXX> Nazwa wtyczki.

### <a name="research"></a>Research 

Przed rozpoczęciem upewnij się, że:

- Przeczytaj specyfikację protokołu, jeśli jest dostępna.

- Sprawdź, które pola protokołów mają zostać wyodrębnione.

- Zaplanowano cele mapowania.

## <a name="about-plugin-files"></a>Pliki wtyczek — informacje 

Trzy pliki są zdefiniowane podczas procesu tworzenia.

### <a name="json-configuration-file-required"></a>Plik konfiguracji JSON (wymagany) 

Ten plik powinien definiować identyfikator sektora i deklaracje, zależności, wymagania dotyczące integracji, parametry weryfikacji i definicje mapowania, aby przetłumaczyć wartości na nazwy, cyfry na tekst. Aby uzyskać więcej informacji, zobacz następujące linki:

- [Przygotuj plik konfiguracji (JSON)](#prepare-the-configuration-file-json)

- [Przygotowywanie walidacji kodu implementacji](#prepare-implementation-code-validations)

- [Wyodrębnij metadane urządzenia](#extract-device-metadata)

- [Łączenie z usługą indeksowania (linia bazowa)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Kod implementacji: C++ (wymagane)

Kod implementacji (CPP) analizuje ruch nieprzetworzony i mapuje go na wartości, takie jak usługi, klasy i kody funkcji. Wyodrębnia pola warstwy i mapuje je na nazwy indeksów z plików konfiguracji JSON. Pola do wyodrębnienia z CPP są zdefiniowane w pliku konfiguracyjnym. Aby uzyskać więcej informacji, zobacz [Przygotowywanie kodu implementacji (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Mapowanie plików (opcjonalnie)

Możesz dostosować tekst wyjściowy wtyczki, aby spełniał wymagania środowiska przedsiębiorstwa.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migracji":::

Możesz definiować i aktualizować pliki mapowania, aby aktualizować tekst bez zmiany kodu. Każdy plik może mapować jedno lub wiele pól:

  - Mapowanie wartości pól na nazwy, na przykład 1: Reset, 2: Start, 3: zatrzymywanie.

  - Mapowanie tekstu w celu obsługi wielu języków.

Aby uzyskać więcej informacji, zobacz [Tworzenie mapowania plików (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Tworzenie wtyczki dla sektora rozdzielenia (omówienie)

1. Zapoznaj się z sekcją [Informacje o środowisku i konfiguracji](#about-the-environment-and-setup) .

2.  [Przygotuj kod implementacji (C++)](#prepare-the-implementation-code-c). Skopiuj **template.js** pliku i zmodyfikuj go, aby spełniał Twoje potrzeby. Nie zmieniaj kluczy. 

3. [Przygotuj plik konfiguracji (JSON)](#prepare-the-configuration-file-json). Skopiuj plik **Template. cpp** i Zaimplementuj metodę przesłaniania. Aby uzyskać więcej informacji, zobacz [horyzont::p rotocol:: BaseParser](#horizonprotocolbaseparser) , aby uzyskać szczegółowe informacje.

4. [Przygotowywanie walidacji kodu implementacji](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Przygotuj kod implementacji (C++)

Plik CPP jest analizatorem odpowiedzialnym za:

- Sprawdzanie poprawności nagłówka i ładunku pakietu (na przykład długości nagłówka lub struktury ładunku).

- Wyodrębnianie danych z nagłówka i ładunku do pól zdefiniowanych.

- Implementowanie skonfigurowanych pól wyodrębnianych przez plik JSON.

### <a name="what-to-do"></a>Co należy zrobić

Skopiuj plik template **. cpp** i Zaimplementuj metodę przesłaniania. Aby uzyskać więcej informacji, zobacz [horyzont::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Przykładowy szablon Basic C++ 

Ta sekcja zawiera podstawowy szablon protokołu z funkcjami standardowymi dla przykładowego protokołu Defender for IoT.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Opis podstawowego szablonu C++  

Ta sekcja zawiera podstawowy szablon protokołu z opisem standardowych funkcji dla przykładowego protokołu Defender for IoT. 

### <a name="include-pluginpluginh"></a>#include "wtyczka/wtyczka. h"

Definicja użyta przez wtyczkę. Plik nagłówkowy zawiera wszystko, co jest potrzebne do ukończenia programowania.

### <a name="horizonprotocolbaseparser"></a>horyzont::p rotocol:: BaseParser

Interfejs komunikacyjny między infrastrukturą horyzontu i warstwą wtyczki. Aby uzyskać więcej informacji, zobacz [Architektura horyzontu](#horizon-architecture) , aby zapoznać się z omówieniem warstw.

ProcessLayer jest metodą służącą do przetwarzania danych.

- Pierwszy parametr w kodzie funkcji jest narzędziem przetwarzania używanym do pobierania danych wcześniej przetworzonych, tworzenia nowych pól i warstw.

- Drugi parametr w kodzie funkcji to bieżące dane przesyłane z poprzedniego parsera.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Użyj, aby utworzyć wystąpienie parsera.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Przykładowy kod funkcji protokołu 

Ta sekcja zawiera przykład sposobu wyodrębniania numeru kodu (2 bajty) i długości komunikatu (4 bajty).

Jest to realizowane zgodnie z parametrem endian dostępnym w pliku konfiguracyjnym JSON, co oznacza, że w przypadku *niewielkiej endian* protokołu i czujnik jest uruchamiany na komputerze z niewielką ilością bajtów, zostanie przekonwertowany.

Zostanie również utworzona warstwa do przechowywania danych. Aby utworzyć nowe pola, użyj *polamanager* z narzędzia do przetwarzania. Pole może zawierać tylko jeden z następujących typów: *String*, *Number*, *Data RAW*, *Array* (określony typ) lub *złożony*. Ta warstwa może zawierać liczbę, nieprzetworzoną lub ciąg z IDENTYFIKATORem.

W poniższym przykładzie następujące dwa pola są wyodrębniane:

- `function_code_number`

- `headerLength`

Nowa warstwa zostanie utworzona, a wyodrębnione pole jest kopiowane do niego.

W poniższym przykładzie opisano konkretną funkcję, która jest podstawową logiką zaimplementowaną do przetwarzania wtyczki.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Powiązane pole JSON 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="Powiązane pole JSON.":::

## <a name="prepare-the-configuration-file-json"></a>Przygotuj plik konfiguracji (JSON)

Zestaw horyzont SDK używa standardowego JavaScript Object Notation (JSON), uproszczonego formatu do przechowywania i transportowania danych i nie wymaga własnościowych języków skryptów.

W tej sekcji opisano minimalną deklarację konfiguracyjną JSON, powiązaną strukturę i przedstawiono przykładowy plik konfiguracji, który definiuje protokół. Ten protokół jest automatycznie zintegrowany z usługą odnajdywania urządzeń.

## <a name="file-structure"></a>Struktura plików

W poniższym przykładzie opisano strukturę plików.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Przykład struktury pliku.":::

### <a name="what-to-do"></a>Co należy zrobić

Skopiuj plik szablonu `config.json` i zmodyfikuj go, aby spełniał Twoje potrzeby. Nie zmieniaj klucza. Klucze są oznaczone kolorem czerwonym w [przykładowym pliku konfiguracji JSON](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Wymagania dotyczące nazewnictwa plików 

Plik konfiguracji JSON musi być zapisany jako `config.json` .

### <a name="json-configuration-file-fields"></a>Pola pliku konfiguracji JSON

W tej sekcji opisano pola konfiguracji JSON, które będą definiowane. Nie zmieniaj *etykiet* pól.

### <a name="basic-parameters"></a>Podstawowe parametry

W tej sekcji opisano podstawowe parametry.

| Etykieta parametru | Opis | Typ |
|--|--|--|
| **ID (Identyfikator)** | Nazwa protokołu. Usuń wartość domyślną i Dodaj nazwę protokołu w takiej postaci, w jakiej jest wyświetlana. | Ciąg |
| **endianess** | Definiuje sposób kodowania danych wielobajtowych. Użyj terminu "mały" lub "Big". Pobrano ze specyfikacji protokołu lub nagrania ruchu | Ciąg |
| **sanity_failure_codes** | Są to kody zwracane z parsera, gdy występuje konflikt Sanity związany z tożsamością kodu. Zobacz temat sprawdzanie poprawności liczby magicznej w sekcji C++. | Ciąg |
| **malformed_codes** | Są to kody, które zostały prawidłowo zidentyfikowane, ale wykryto błąd. Na przykład, jeśli długość pola jest za krótka lub długa lub wartość jest nieprawidłowa. | Ciąg |
| **dissect_as** | Tablica definiująca, gdzie ma być odbierany ruch określony przez protokół. | TCP/UDP, port itd. |
| **pola** | Deklaracja, z której pola zostaną wyodrębnione z ruchu. Każde pole ma własny identyfikator (nazwę) i typ (numeric, String, RAW, Array, Complex). Na przykład [Funkcja](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) pola wyodrębniona w pliku parsera implementacji. Pola, które są zapisywane w pliku konfiguracyjnym są jedynymi, które można dodać do warstwy. |  |

### <a name="other-advanced-fields"></a>Inne pola zaawansowane 

W tej sekcji opisano inne pola.

| Etykieta parametru | Opis |
|-----------------|--------|
| **Lista dozwolonych** | Można indeksować wartości protokołów i wyświetlać je w raportach wyszukiwania danych. Te raporty odzwierciedlają podstawę sieci. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Przykład widoku wyszukiwania danych."::: <br /> Aby uzyskać więcej informacji, zobacz [łączenie się z usługą indeksowania (Baseline)](#connect-to-an-indexing-service-baseline) , aby uzyskać szczegółowe informacje. |
| **wbudowane** | Można wyodrębnić informacje oprogramowania układowego, zdefiniować wartości indeksów i wyzwalać alerty oprogramowania układowego dla protokołu wtyczki. Aby uzyskać więcej informacji, zobacz [wyodrębnienie danych oprogramowania układowego](#extract-firmware-data) w celu uzyskania szczegółów. |
| **value_mapping** | Możesz dostosować tekst wyjściowy wtyczki, aby spełniał wymagania środowiska przedsiębiorstwa przez definiowanie i aktualizowanie plików mapowania. Na przykład zamapuj na pliki językowe. Zmiany można łatwo zaimplementować do tekstu bez zmiany lub wpływu na kod. Aby uzyskać więcej informacji, zobacz temat [Tworzenie plików mapowania (JSON)](#create-mapping-files-json) , aby uzyskać szczegółowe informacje. |

## <a name="sample-json-configuration-file"></a>Przykładowy plik konfiguracji JSON

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Przygotowywanie walidacji kodu implementacji

W tej sekcji opisano implementację funkcji walidacji kodu C++ i przedstawiono przykładowy kod. Dostępne są dwie warstwy weryfikacji:

- Sanity.

- Nieprawidłowo sformułowany kod.

Nie musisz tworzyć kodu weryfikacyjnego w celu utworzenia działającej wtyczki. Jeśli nie przygotujesz kodu weryfikacyjnego, możesz przejrzeć raporty wyszukiwania danych z czujnika jako wskazanie pomyślnego przetwarzania.

Wartości pól mogą być mapowane na tekst w mapowaniu plików i bezproblemowo aktualizowane bez wpływu na przetwarzanie.

## <a name="sanity-code-validations"></a>Sanity walidacji kodu 

Powoduje to sprawdzenie, czy przesłany pakiet jest zgodny z parametrami walidacji protokołu, co ułatwia identyfikację protokołu w ramach ruchu.

Na przykład użyj pierwszych 8 bajtów jako *liczby magicznej*. Jeśli Sanity nie powiedzie się, zostanie zwrócona odpowiedź z błędem Sanity.

Na przykład:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Jeśli zostały wdrożone inne odpowiednie wtyczki, pakiet zostanie sprawdzony pod kątem poprawności.

## <a name="malformed-code-validations"></a>Nieprawidłowo sformułowane walidacje kodu 

Nieprawidłowo sformułowane walidacje są używane po pozytywnym sprawdzeniu protokołu.

W przypadku niepowodzenia przetwarzania pakietów na podstawie protokołu zwracana jest odpowiedź na błąd.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="nieprawidłowo sformułowany kod":::

## <a name="c-sample-with-validations"></a>Przykład języka C++ z walidacjami

Zgodnie z funkcją proces jest wykonywany, jak pokazano w poniższym przykładzie.

### <a name="function-20"></a>Funkcja 20 

- Jest ona przetwarzana jako oprogramowanie układowe.

- Pola są odczytywane zgodnie z funkcją.

- Pola są dodawane do warstwy.

### <a name="function-10"></a>Funkcja 10 

- Funkcja zawiera inną funkcję sub, która jest bardziej konkretną operacją

- Podfunkcja jest odczytywana i dodawana do warstwy.

Gdy to zrobisz, przetwarzanie zostało zakończone. Wartość zwracana wskazuje, czy warstwa odsektora została pomyślnie przetworzona. W takim przypadku warstwa stanie się użyteczna.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Wyodrębnij metadane urządzenia

Można wyodrębnić następujące metadane dotyczące zasobów:

  - `Is_distributed_control_system` -Wskazuje, czy protokół jest częścią rozproszonego systemu sterowania. (przykład: protokół SCADA powinien mieć wartość false)

  - `Has_protocol_address` — Wskazuje, czy istnieje adres protokołu; konkretny adres dla bieżącego protokołu, na przykład identyfikator jednostki MODBUS

  - `Is_scada_protocol` -Wskazuje, czy protokół jest specyficzny dla niezwiązanych z siecią

  - `Is_router_potential` -Wskazuje, czy protokół jest używany głównie przez routery. Na przykład, LLDP, CDP lub STP.

Aby to osiągnąć, należy zaktualizować plik konfiguracji JSON przy użyciu właściwości Metadata.

## <a name="json-sample-with-metadata"></a>Przykład JSON z metadanymi 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Wyodrębnij kod programistyczny 

Gdy wystąpi zdarzenie programowania, można wyodrębnić zawartość kodu. Wyodrębniona zawartość umożliwia:

- Porównaj zawartość pliku kodu w różnych zdarzeniach programistycznych.

- Wyzwól alert dotyczący nieautoryzowanego programowania.  

- Wyzwalanie zdarzenia do uzyskiwania pliku kodu programowania.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Dziennik zmian programistycznych.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Aby wyświetlić programowanie, kliknij przycisk.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Alert dotyczący nieautoryzowanego programowania PLC.":::

Aby to osiągnąć, należy zaktualizować plik konfiguracji JSON przy użyciu `code_extraction` właściwości. 

### <a name="json-configuration-fields"></a>Pola konfiguracji JSON 

W tej sekcji opisano pola konfiguracji JSON. 

- **Method**

  Wskazuje sposób, w jaki pliki zdarzeń programistycznych są odbierane. 

  ALL (każda akcja programowania spowoduje, że wszystkie pliki kodu zostaną odebrane nawet wtedy, gdy istnieją pliki bez zmian).

- **file_type**  

  Wskazuje typ zawartości kodu.  

  TEKST (każdy plik kodu zawiera informacje tekstowe).

- **code_data_field**
  
  Wskazuje pole implementacji, które ma być używane w celu zapewnienia zawartości kodu.

  Polami.

- **code_name_field**

  Wskazuje pole implementacji, które ma być używane w celu podania nazwy pliku kodowania.

  Polami.

- **size_limit**

  Wskazuje limit rozmiaru każdej zawartości pliku kodowania w BAJTach, jeśli plik kodu przekroczy określony limit, zostanie porzucony. Jeśli to pole nie zostanie określone, wartością domyślną będzie 15 000 000, czyli 15 MB.

  Liczba.

- **metadane**

  Wskazuje dodatkowe informacje dotyczące pliku z kodem.

  Tablica zawierająca obiekty mające dwie właściwości:

    - Nazwa (ciąg) — wskazuje, że klucz metadanych XSense obecnie obsługuje tylko klucz nazwy użytkownika.
 
    - wartość (pole) — wskazuje pole implementacji, które ma być używane w celu dostarczania danych metadanych.

## <a name="json-sample-with-programming-code"></a>Przykład JSON z kodem programowania

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Niestandardowe alerty horyzontu

Kod funkcji niektórych protokołów może wskazywać na błąd. Na przykład, jeśli protokół kontroluje kontener z określoną substancją chemiczną, która musi być przechowywana zawsze w konkretnej temperaturze. W takim przypadku może istnieć kod funkcji wskazujący błąd w termometrie. Na przykład jeśli kod funkcji to 25, można wyzwolić alert w konsoli sieci Web, co oznacza, że występuje problem z kontenerem. W takim przypadku można zdefiniować alerty dotyczące głębokiego pakietu.

Dodaj parametr **alertów** do `config.json` wtyczki.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Pola konfiguracji JSON

W tej sekcji opisano pola konfiguracji JSON. 

| Nazwa pola | Opis | Możliwe wartości |
|--|--|--|
| **ID (Identyfikator)** | Reprezentuje pojedynczy identyfikator alertu. Musi być unikatowa w tym kontekście. | Wartość liczbowa 0-10000 |
| **Komunikat** | Informacje wyświetlane użytkownikowi. To pole umożliwia korzystanie z różnych pól. | Użyj dowolnego pola z protokołu lub dowolnego protokołu niższej warstwy. |
| **tytuły** | Tytuł alertu |  |
| **expression** | Jeśli chcesz, aby ten alert został wystawiony. | Użyj dowolnego pola liczbowego znajdującego się w niższych warstwach lub bieżącej warstwie.</br></br> Każde pole powinno być otoką z `{}` , aby zestaw SDK wykrył go jako pole, obsługiwane operatory logiczne to</br> = = Równa się</br> <=-mniejsze niż lub równe</br> >=-więcej niż lub równe</br> > — więcej niż</br> <-mniejsze niż</br> ~ =-Nie równa się |

## <a name="more-about-expressions"></a>Więcej informacji na temat wyrażeń

Za każdym razem, gdy zestaw SDK jest wywoływany w wyrażeniu i *ma wartość true*, w czujniku zostanie wyzwolony alert.

W ramach tego samego alertu można uwzględnić wiele wyrażeń. Przykład:

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

To wyrażenie sprawdza poprawność kodu funkcji tylko wtedy, gdy src pakietu IPv4 jest 10.10.10.10. Jest to nieprzetworzona reprezentacja adresu IP w postaci liczbowej.

Można użyć `and` lub w celu `or` połączenia wyrażeń.

## <a name="json-sample-custom-horizon-alerts"></a>Alerty niestandardowego horyzontu kodu JSON

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Łączenie z usługą indeksowania (linia bazowa)

Można indeksować wartości protokołów i wyświetlać je w raportach wyszukiwania danych.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Widok opcji wyszukiwania danych.":::

Te wartości mogą być później mapowane do określonych tekstów, na przykład mapowanie numerów jako tekstów lub Dodawanie informacji w dowolnym języku.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migracji":::

Aby uzyskać więcej informacji, zobacz temat [Tworzenie plików mapowania (JSON)](#create-mapping-files-json) , aby uzyskać szczegółowe informacje.

Można również użyć wartości z protokołów wcześniej przeanalizowanych w celu wyodrębnienia dodatkowych informacji.

Na przykład dla wartości, która jest oparta na protokole TCP, można użyć wartości z warstwy IPv4. Z tej warstwy można wyodrębnić wartości, takie jak źródło pakietu i miejsce docelowe.

Aby to osiągnąć, należy zaktualizować plik konfiguracji JSON przy użyciu `whitelist` właściwości.

## <a name="allow-list-data-mining-fields"></a>Pola listy dozwolonych (wyszukiwanie danych)

Dostępne są następujące pola listy dozwolonych:

- Name — Nazwa używana do indeksowania.

- alert_title — krótki, unikatowy tytuł objaśniający zdarzenie.

- alert_text — dodatkowe informacje

Można dodać wiele list dozwolonych, co zapewnia pełną elastyczność indeksowania.

## <a name="json-sample-with-indexing"></a>Przykład JSON z indeksem 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Wyodrębnij dane oprogramowania układowego

Można wyodrębnić informacje oprogramowania układowego, zdefiniować wartości indeksów i wyzwalać alerty oprogramowania układowego dla protokołu wtyczki. Na przykład

- Wyodrębnij model lub wersję oprogramowania układowego. Te informacje mogą być następnie wykorzystywane do identyfikacji CVEs.

- Wyzwól alert, gdy zostanie wykryta Nowa wersja oprogramowania układowego.

Aby to osiągnąć, należy zaktualizować plik konfiguracji JSON przy użyciu właściwości oprogramowania układowego.

## <a name="firmware-fields"></a>Pola oprogramowania układowego

W tej sekcji opisano pola konfiguracji oprogramowania układowego JSON.

- **Nazwij**
  
  Wskazuje, w jaki sposób pole jest prezentowane w konsoli czujnika.

- **wartość**

 Wskazuje pole implementacji, które ma być używane w celu zapewnienia danych. 

- **firmware_index:**

  Wybierz jedną z opcji:  
  - **model**: model urządzenia. Włącza wykrywanie CVEs.
  - **port szeregowy**: numer seryjny urządzenia. Numer seryjny nie zawsze jest dostępny dla wszystkich protokołów. Ta wartość jest unikatowa dla urządzenia.
  - **stojak**: wskazuje identyfikator stojaka, jeśli urządzenie jest częścią stojaka.
  - **gniazdo**: Identyfikator gniazda, jeśli urządzenie jest częścią stojaka.  
  - **module_address**: Użyj, aby przedstawić hierarchię, jeśli moduł może być prezentowany za innym urządzeniem. Dotyczy zamiast tego, czy jest to prostsza prezentacja.  
  - **firmware_version**: wskazuje wersję urządzenia. Włącza wykrywanie CVEs.
  - **alert_text**: wskazuje tekst opisujący odchylenia oprogramowania układowego, na przykład zmiany wersji.  
  - **index_by**: wskazuje pola używane do identyfikowania i indeksowania urządzenia. W poniższym przykładzie urządzenie jest identyfikowane za pomocą jego adresu IP. W niektórych protokołach można użyć bardziej złożonego indeksu. Jeśli na przykład inne urządzenie zostało połączone i wiesz, jak wyodrębnić jego wewnętrzną ścieżkę. Na przykład identyfikator jednostki MODBUS może służyć jako część indeksu jako inna kombinacja adresu IP i identyfikatora jednostki.
  - **firmware_fields**: wskazuje, które pola są polami metadanych urządzeń. W tym przykładzie używane są następujące elementy: model, poprawka i nazwa. Każdy protokół może definiować własne dane oprogramowania układowego.

## <a name="json-sample-with-firmware"></a>Przykład JSON z oprogramowaniem układowym 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Wyodrębnij atrybuty urządzenia 

Informacje dostępne na urządzeniu można ulepszyć w spisie, wyszukiwania danych i innych raportach.

- Nazwa

- Typ

- Dostawca

- System operacyjny

Aby to osiągnąć, należy zaktualizować plik konfiguracji JSON przy użyciu właściwości **Właściwości** . 

Można to zrobić po zapisaniu podstawowej wtyczki i wyodrębnieniu wymaganych pól.

## <a name="properties-fields"></a>Pola właściwości

W tej sekcji opisano pola konfiguracyjne właściwości JSON. 

**config_file** 

Zawiera nazwę pliku, który definiuje sposób przetwarzania każdego klucza w `key` polach. Sam plik konfiguracji powinien być w formacie JSON i być dołączony jako część folderu protokołu wtyczki.

Każdy klucz w formacie JSON definiuje zestaw akcji, które należy wykonać podczas wyodrębniania tego klucza z pakietu.

Każdy klucz może mieć:

- **Dane pakietu** — wskazuje właściwości, które będą aktualizowane na podstawie danych wyodrębnionych z pakietu (pole implementacji używane do zapewnienia tych danych).

- **Dane statyczne** — wskazuje wstępnie zdefiniowany zestaw `property-value` akcji, które powinny zostać zaktualizowane.

Właściwości, które można skonfigurować w tym pliku, to: 

- **Nazwa** — wskazuje nazwę urządzenia.

- **Typ** — wskazuje typ urządzenia.

- **Dostawca** — oznacza dostawcę urządzenia.

- **Opis** — wskazuje model oprogramowania układowego urządzenia (niższy priorytet niż "model").

- **operatingSystem** — wskazuje system operacyjny urządzenia.

### <a name="fields"></a>Pola

| Pole | Opis |
|--|--|
| key | Wskazuje klucz. |
| wartość | Wskazuje pole implementacji, które ma być używane w celu zapewnienia danych. |
| is_static_key | Wskazuje, czy `key` pole jest wyprowadzane jako wartość z pakietu, czy jest to wstępnie zdefiniowana wartość. |

### <a name="working-with-static-keys-only"></a>Praca z tylko kluczami statycznymi

W przypadku pracy z kluczami statycznymi nie trzeba konfigurować `config.file` . Można skonfigurować tylko plik JSON.

## <a name="json-sample-with-properties"></a>Przykład JSON z właściwościami 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Tworzenie plików mapowania (JSON)

Możesz dostosować tekst wyjściowy wtyczki, aby spełniał wymagania środowiska przedsiębiorstwa przez definiowanie i aktualizowanie plików mapowania. Zmiany można łatwo zaimplementować do tekstu bez zmiany lub wpływu na kod. Każdy plik może mapować jedno lub wiele pól.

- Mapowanie wartości pól na nazwy, na przykład 1: Reset, 2: Start, 3: zatrzymywanie.

- Mapowanie tekstu w celu obsługi wielu języków.

Można zdefiniować dwa typy plików mapowania.

  - [Prosty plik mapowania](#simple-mapping-file).

  - [Plik mapowania zależności](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Sieć eteru":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Widok nieobsłużonych alertów.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Lista znanych naruszeń zasad.":::

## <a name="file-naming-and-storage-requirements"></a>Wymagania dotyczące nazewnictwa i magazynowania plików 

Mapowanie plików powinno być zapisywane w folderze metadanych.

Nazwa pliku powinna być zgodna z IDENTYFIKATORem pliku konfiguracji JSON.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Przykład pliku konfiguracji JSON.":::

## <a name="simple-mapping-file"></a>Prosty plik mapowania 

Poniższy przykład przedstawia podstawowy plik JSON jako wartość klucza.

Gdy tworzysz listę dozwolonych elementów i zawiera ona co najmniej jedno mapowane pole. Wartość zostanie przekonwertowana z liczby, ciągu lub dowolnego typu w sformatowanym tekście przedstawionym w pliku mapowania.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Plik mapowania zależności 

Aby wskazać, że plik jest plikiem zależności, Dodaj słowo kluczowe `dependency` do konfiguracji mapowania.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Plik zawiera mapowanie między polem zależności a polem funkcji. Na przykład między funkcją i funkcją sub. Funkcja sub zmienia się zgodnie z podaną funkcją.

Na liście dozwolonych wcześniej skonfigurowana nie ma konfiguracji zależności, jak pokazano poniżej.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

Zależność może opierać się na określonej wartości lub polu. W poniższym przykładzie jest on oparty na polu. Jeśli opierasz się na wartości, zdefiniuj wartość wyodrębniania, która ma zostać odczytana z pliku mapowania.

W poniższym przykładzie zależność od tej samej wartości pola.

Na przykład w funkcji sub pięć wartość znaczenie jest zmieniana na podstawie funkcji.

  - Jeśli jest funkcją odczytu, pięć oznacza pamięć odczytu.

  - Jeśli jest to funkcja Write, ta sama wartość jest używana do odczytu z pliku.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Przykładowy plik

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Przykład JSON z mapowaniem

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Pakowanie, przekazywanie i monitorowanie wtyczki 

W tej sekcji opisano, jak

  - Pakowanie wtyczki.

  - Przekaż wtyczkę.

  - Monitoruj i Debuguj wtyczkę, aby oszacować, jak dobrze działa.

Aby spakować wtyczkę:

1. Dodaj **artefakt** (może to być, library, config.json lub Metadata) do `tar.gz` pliku.

1. Zmień rozszerzenie pliku na \<XXX.hdp> , gdzie jest \<XXX> nazwą wtyczki.

Aby zalogować się do konsoli horyzontu:

1.  Zaloguj się do interfejsu wiersza polecenia czujnika jako administrator, CyberX lub użytkownik pomocy technicznej.

2.  W pliku: `/var/cyberx/properties/horizon.properties` Zmień właściwość **UI. Enabled** na **true** ( `horizon.properties:ui.enabled=true` ).

3.  Zaloguj się do konsoli czujnika.

4.  Wybierz opcję **horyzonty** w menu głównym.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Wybierz opcję horyzontu w okienku po lewej stronie.":::

    Zostanie otwarta konsola horyzontów.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Widok konsoli horyzontu i wszystkich jej wtyczek.":::

## <a name="plugins-pane"></a>Okienko wtyczek

Panel wtyczki zawiera listę:

  - Wtyczki infrastruktury: wtyczki infrastruktury instalowane domyślnie z usługą Defender for IoT.

  - Wtyczki aplikacji: wtyczki aplikacji instalowane domyślnie z usługą Defender for IoT i innymi wtyczkami opracowanymi przez usługę Defender dla IoT lub zewnętrznych deweloperów.
    
Włączać i wyłączać wtyczki, które zostały przekazane przy użyciu przełącznika.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="Przełącznik PRZELEWu.":::

### <a name="uploading-a-plugin"></a>Przekazywanie wtyczki

Po utworzeniu i spakowaniu wtyczki można przekazać ją do czujnika usługi Defender for IoT. Aby zapewnić pełne pokrycie sieci, należy przekazać wtyczkę do każdego czujnika w organizacji.

Aby przekazać:

1.  Zaloguj się do czujnika.


2. Wybierz pozycję **Przekaż**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Przekaż wtyczki.":::

3. Przejdź do wtyczki i przeciągnij ją do okna dialogowego wtyczki. Sprawdź, czy prefiks jest `.hdp` . Wtyczka ładuje.

## <a name="plugin-status-overview"></a>Przegląd stanu wtyczki 

Okno **Przegląd** konsoli horyzontu zawiera informacje o dodatku, który został przekazany i umożliwia jego wyłączenie i włączenie.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Omówienie konsoli horyzontu.":::

| Pole | Opis |
|--|--|
| Aplikacja | Nazwa załadowanej wtyczki. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Przełącznik włączony i wyłączony."::: | Włącz **lub** **Wyłącz** wtyczkę. Usługa Defender for IoT nie będzie obsługiwać ruchu protokołu zdefiniowanego w dodatku plug-in po przełączeniu wtyczki. |
| Godzina | Godzina ostatniej analizy danych. Zaktualizowano co 5 sekund. |
| PPS | Liczba pakietów na sekundę. |
| Szerokość pasma | Średnia przepustowość wykryta w ciągu ostatnich 5 sekund. |
| Źle sformułowane | Nieprawidłowo sformułowane walidacje są używane po pozytywnym sprawdzeniu protokołu. W przypadku niepowodzenia przetwarzania pakietów na podstawie protokołu zwracana jest odpowiedź na błąd.   <br><br>Ta kolumna wskazuje liczbę wadliwych błędów w ciągu ostatnich 5 sekund. Aby uzyskać więcej informacji, zobacz [nieprawidłowo sformułowane walidacji kodu](#malformed-code-validations) , aby uzyskać szczegółowe informacje. |
| Ostrzeżenia | Pakiety pasują do struktury i specyfikacji, ale występują nieoczekiwane zachowanie na podstawie konfiguracji ostrzegawczej wtyczki. |
| błędy | Liczba pakietów, dla których nie powiodła się podstawowa Walidacja protokołów. Sprawdza, czy pakiet jest zgodny z definicjami protokołu. Liczba wyświetlana w tym miejscu wskazuje liczbę błędów wykrytych w ciągu ostatnich 5 sekund. Aby uzyskać więcej informacji, zobacz [Sanitye walidacji kodu](#sanity-code-validations) w celu uzyskania szczegółów. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Ikona monitora."::: | Przejrzyj szczegółowe informacje o błędach i ostrzeżeniach wykrytych dla wtyczki. |

## <a name="plugin-details"></a>Szczegóły wtyczki

Można monitorować zachowanie wtyczki w czasie rzeczywistym, analizując liczbę *nieprawidłowych* i *ostrzeżeń* wykrytych dla wtyczki. Dostępna jest opcja zawieszania ekranu i eksportu w celu dalszej analizy

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Ekran monitora SNMP.":::

Aby monitorować:

Wybierz przycisk Monitoruj dla wtyczki z przeglądu.

Następne kroki

Konfigurowanie [interfejsu API usługi Horizon](references-horizon-api.md)
