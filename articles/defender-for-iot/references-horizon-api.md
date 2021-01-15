---
title: Interfejs API horyzontu
description: W tym przewodniku opisano powszechnie używane metody horyzontu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/5/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 39770fe7aa7b11cae03304fda8901e81e0f1877a
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208419"
---
# <a name="horizon-api"></a>Interfejs API horyzontu 

W tym przewodniku opisano powszechnie używane metody horyzontu.

### <a name="getting-more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat pracy z horyzontem i usługą Defender for IoT, zobacz następujące informacje:

- W przypadku zestawu SDK open Development Environment (Node) można skontaktować się z usługą Defender for IoT.
- Aby uzyskać pomoc techniczną i rozwiązywanie problemów, skontaktuj się z firmą <support@cyberx-labs.com> .

- Aby uzyskać dostęp do podręcznika użytkownika usługi Defender for IoT z poziomu konsoli usługi Defender dla IoT, wybierz pozycję, :::image type="icon" source="media/references-horizon-api/profile.png"::: a następnie wybierz pozycję **Pobierz podręcznik użytkownika**.


## `horizon::protocol::BaseParser`

Abstrakcyjne dla wszystkich wtyczek. Obejmuje to dwie metody:

- Do przetwarzania filtrów wtyczki zdefiniowanych powyżej. W ten sposób horyzont wie, jak komunikować się z parserem.
- Do przetwarzania rzeczywistych danych.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

Pierwsza funkcja wywoływana dla wtyczki tworzy wystąpienie analizatora dla zakresu, aby go rozpoznać i zarejestrować.

### <a name="parameters"></a>Parametry 

Brak.

### <a name="return-value"></a>Wartość zwracana

shared_ptr do wystąpienia parsera.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Ta funkcja zostanie wywołana dla każdej zarejestrowanej powyżej wtyczki. 

W większości przypadków będzie to puste. Zgłoś wyjątek dla zakresu, aby poznać coś nieprawidłowego.

### <a name="parameters"></a>Parametry 

- Mapa zawierająca strukturę dissect_as, zgodnie z definicją w config.jsw innej wtyczki, która chce się zarejestrować.

### <a name="return-value"></a>Wartość zwracana 

Tablica uint64_t, która jest rejestracją przetworzoną na rodzaj uint64_t. Oznacza to, że na mapie będzie dostępna lista portów, których wartości będą uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Funkcja Main. W odniesieniu do logiki wtyczki, za każdym razem, gdy nowy pakiet osiągnie Analizator. Ta funkcja zostanie wywołana. w tym miejscu należy wykonać wszystkie czynności związane z przetwarzaniem pakietów.

### <a name="considerations"></a>Zagadnienia do rozważenia

Wtyczka powinna być bezpieczna wątkowo, ponieważ ta funkcja może zostać wywołana z różnych wątków. Dobrym rozwiązaniem jest zdefiniowanie wszystkich elementów na stosie.

### <a name="parameters"></a>Parametry

- Jednostka kontroli zestawu SDK odpowiedzialna za przechowywanie danych i tworzenie obiektów związanych z zestawem SDK, takich jak ILayer i pola.
- Pomocnik do odczytywania danych z nieprzetworzonego pakietu. Jest już ustawiona z kolejnością bajtów zdefiniowaną w config.jsna.

### <a name="return-value"></a>Wartość zwracana 

Wynik przetwarzania. Może to być *pomyślne*, *źle sformułowane* lub *Sanity*.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Oznacza przetwarzanie jako niepowodzenie błędów, co oznacza, że pakiet nie jest rozpoznawany przez bieżący protokół, a horyzont powinien przekazać go do innego parsera, jeśli istnieją zarejestrowane w tych samych filtrach.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Parametry 

- Definiuje kod błędu używany przez horyzont do rejestrowania, zgodnie z definicją w config.jsna.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Nieprawidłowo sformułowany wynik, wskazuje, że pakiet został już rozpoznany jako protokół, ale niektóre sprawdzanie poprawności poszło źle (zarezerwowane bity są włączone lub brakuje niektórych pól).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Parametry  

- Kod błędu, zgodnie z definicją w config.js.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Powiadamia horyzonty o pomyślnym przetworzeniu. Po pomyślnym zakończeniu pakiet został zaakceptowany, dane należą do nas, a wszystkie dane zostały wyodrębnione.

## `horizon::protocol::SuccessResult()`

Konstruktor. Utworzono podstawowy wynik. Oznacza to, że nie wiemy kierunek ani żadne inne metadane dotyczące pakietu.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Konstruktor.

### <a name="parameters"></a>Parametry 

- Kierunek pakietu, jeśli został zidentyfikowany. Możliwe wartości to *żądanie* lub *odpowiedź*.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Konstruktor.

### <a name="parameters"></a>Parametry

- Kierunek pakietu, jeśli został zidentyfikowany, może to być *żądanie*, *odpowiedź*.
- Ostrzeżeni. Te zdarzenia nie zakończyły się niepowodzeniem, ale zakres zostanie powiadomiony.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Konstruktor.

### <a name="parameters"></a>Parametry 

-  Ostrzeżeni. Te zdarzenia nie zakończyły się niepowodzeniem, ale zakres zostanie powiadomiony.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Konwertuje odwołanie oparte na ciągach na nazwę pola (na przykład function_code) na HorizonID.

### <a name="parameters"></a>Parametry 

- Ciąg do przekonwertowania.

### <a name="return-value"></a>Wartość zwracana

- HorizonID utworzone na podstawie ciągu.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Tworzy nową warstwę, więc horyzonty będą wiedzieć, że wtyczka chce przechowywać dane. Jest to podstawowa jednostka magazynowa, której należy użyć.

### <a name="return-value"></a>Wartość zwracana

Odwołanie do utworzonej warstwy, dzięki czemu można dodać do niej dane.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Pobiera obiekt zarządzania polami, który jest odpowiedzialny za tworzenie pól w różnych obiektach, na przykład w ILayer.

### <a name="return-value"></a>Wartość zwracana

Odwołanie do Menedżera.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Tworzy nowe pole liczbowe o 64 bitach na warstwie z żądanym IDENTYFIKATORem.

### <a name="parameters"></a>Parametry 

- Utworzona wcześniej warstwa.
- HorizonID utworzony przez makro **HORIZON_FIELD** .
- Wartość pierwotna, która ma zostać zapisana.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Tworzy nowe pole ciągu dla warstwy z żądanym IDENTYFIKATORem. Pamięć zostanie przeniesiona, dlatego należy zachować ostrożność. Nie będzie można użyć tej wartości ponownie.

### <a name="parameters"></a>Parametry  

- Utworzona wcześniej warstwa.
- HorizonID utworzony przez makro **HORIZON_FIELD** .
- Wartość pierwotna, która ma zostać zapisana.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Tworzy nowe pole wartość pierwotna (tablica bajtów) w warstwie, z żądanym IDENTYFIKATORem. Pamięć zostanie przeniesiona, więc należy zachować ostrożność, ale nie będzie można użyć tej wartości ponownie.

### <a name="parameters"></a>Parametry

- Utworzona wcześniej warstwa.
- HorizonID utworzony przez makro **HORIZON_FIELD** .
- Wartość pierwotna, która ma zostać zapisana.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Tworzy pole wartości tablicy (Array) w warstwie określonego typu z żądanym IDENTYFIKATORem.

### <a name="parameters"></a>Parametry

- Utworzona wcześniej warstwa.
- HorizonID utworzony przez makro **HORIZON_FIELD** .
- Typ wartości, które będą przechowywane wewnątrz tablicy.

### <a name="return-value"></a>Wartość zwracana

Odwołanie do tablicy, do której należy dołączyć wartości.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Dołącza nową wartość całkowitą do utworzonej wcześniej tablicy.

### <a name="parameters"></a>Parametry

- Utworzona wcześniej tablica.
- Wartość pierwotna, która ma być przechowywana w tablicy.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Dołącza nową wartość ciągu do utworzonej wcześniej tablicy. Pamięć zostanie przeniesiona, więc należy zachować ostrożność, ale nie będzie można użyć tej wartości ponownie.

### <a name="parameters"></a>Parametry

- Utworzona wcześniej tablica.
- Wartość pierwotna, która ma być przechowywana w tablicy.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Dołącza nową wartość pierwotną do utworzonej wcześniej tablicy. Pamięć zostanie przeniesiona, więc należy zachować ostrożność, ale nie będzie można użyć tej wartości ponownie.

### <a name="parameters"></a>Parametry

- Utworzona wcześniej tablica.
- Wartość pierwotna, która ma być przechowywana w tablicy.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Sprawdza, czy bufor zawiera co najmniej X b.

### <a name="parameters"></a>Parametry

Liczba bajtów, które powinny istnieć.

### <a name="return-value"></a>Wartość zwracana

Ma wartość true, jeśli bufor zawiera co najmniej X bajtów. W przeciwnym razie jest to `False` .

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Odczytuje wartość Uint8 (1 bajt) z buforu, zgodnie z kolejnością bajtów.

### <a name="return-value"></a>Wartość zwracana

Wartość odczytana z buforu.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Odczytuje wartość UInt16 (2 bajty) z buforu, zgodnie z kolejnością bajtów.

### <a name="return-value"></a>Wartość zwracana

Wartość odczytana z buforu.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Odczytuje wartość UInt32 (4 bajty) z buforu zgodnie z kolejnością bajtów.

### <a name="return-value"></a>Wartość zwracana

Wartość odczytana z buforu.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Odczytuje wartość UInt64 (8 bajtów) z buforu, zgodnie z kolejnością bajtów.

### <a name="return-value"></a>Wartość zwracana

Wartość odczytana z buforu.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Odczytuje do wstępnie przydzielonej pamięci o określonym rozmiarze, w rzeczywistości kopiuje dane do regionu pamięci.

### <a name="parameters"></a>Parametry 

- Region pamięci, do którego mają zostać skopiowane dane.
- Rozmiar obszaru pamięci, ten parametr definiuje także liczbę bajtów, które zostaną skopiowane.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Odczytuje do ciągu z bufora.

### <a name="parameters"></a>Parametry 

- Liczba bajtów, które mają zostać odczytane.

### <a name="return-value"></a>Wartość zwracana

Odwołanie do regionu pamięci ciągu.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Informuje o liczbie bajtów pozostałych w buforze.

### <a name="return-value"></a>Wartość zwracana

Pozostały rozmiar buforu.

## `void horizon::general::IDataBuffer::skip(size_t)`

Pomija X bajtów w buforze.

### <a name="parameters"></a>Parametry

- Liczba bajtów do pominięcia.
