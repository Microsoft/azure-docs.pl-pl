---
title: Samouczek — opis kryptografii i certyfikatów X.509 dla Azure IoT Hub | Microsoft Docs
description: Samouczek — opis kryptografii i X.509 PKI dla Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378384"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Samouczek: opis kryptografii klucza publicznego i infrastruktury kluczy publicznych X.509

Certyfikatów X.509 można użyć do uwierzytelniania urządzeń w Azure IoT Hub. Certyfikat jest dokumentem cyfrowym, który zawiera klucz publiczny urządzenia i może służyć do weryfikowania, czy urządzenie jest tym, za co się podaje. Certyfikaty X.509 i listy odwołania certyfikatów (CRL) są udokumentowane w dokumencie [RFC 5280.](https://tools.ietf.org/html/rfc5280) Certyfikaty są tylko jedną z części infrastruktury kluczy publicznych (PKI) X.509. Aby zrozumieć X.509 PKI, musisz zrozumieć algorytmy kryptograficzne, klucze kryptograficzne, certyfikaty i urzędy certyfikacji:

* **Algorytmy definiują** sposób przekształcania oryginalnych danych w postaci zwykłego tekstu na tekst szyfrowany i z powrotem do postaci zwykłego tekstu.
* **Klucze** to losowe lub pseudolosowe ciągi danych używane jako dane wejściowe algorytmu.
* **Certyfikaty są** dokumentami cyfrowymi, które zawierają klucz publiczny jednostki i umożliwiają określenie, czy podmiotem certyfikatu jest to, kto, czy jak się podaje.
* **Urzędy certyfikacji** potwierdzają autentyczność podmiotów certyfikatów.

Certyfikat można kupić w urzędzie certyfikacji. Można również utworzyć główny urząd certyfikacji z podpisem własnym w celu testowania i testowania lub pracy w środowisku samodzielnym. Jeśli na przykład masz co najmniej jedno urządzenie i testujesz uwierzytelnianie centrum IoT Hub, możesz samodzielnie podpisać główny urząd certyfikacji i użyć go do wystawienia certyfikatów urządzeń. Można również wystawiać certyfikaty urządzeń z podpisem własnym. Omówiono to w kolejnych artykułach.

Przed bardziej szczegółowym omówieniem certyfikatów X.509 i użyciem ich do uwierzytelniania urządzeń w IoT Hub omówiono kryptografię, na której opierają się certyfikaty.

## <a name="cryptography"></a>Kryptografia

Kryptografia służy do ochrony informacji i komunikacji. Zwykle odbywa się to przy użyciu technik kryptograficznych w celu zaszyfrowania zwykłego tekstu (zwykłego tekstu) do szyfru (zakodowanego tekstu) i z powrotem. Ten proces tworzenia kodów jest nazywany szyfrowaniem. Proces odwrotny jest nazywany odszyfrowywaniem. Kryptografia dotyczy następujących celów:

* **Poufność:** informacje mogą być zrozumiałe tylko dla odbiorców.
* **Integralność:** informacji nie można zmienić w magazynie ani w trakcie przesyłania.
* **Brak wypierenia** się: twórca informacji nie może później odmówić tego utworzenia.
* **Uwierzytelnianie:** nadawca i odbiorca mogą potwierdzić swoją tożsamość.

## <a name="encryption"></a>Szyfrowanie

Proces szyfrowania wymaga algorytmu i klucza. Algorytm definiuje sposób przekształcania danych z zwykłego tekstu na tekst szyfrowany i z powrotem do zwykłego tekstu. Klucz to losowy ciąg danych używany jako dane wejściowe algorytmu. Wszystkie zabezpieczenia procesu są zawarte w kluczu . W związku z tym klucz musi być przechowywany bezpiecznie. Szczegóły najpopularniejszych algorytmów są jednak publicznie dostępne.

Istnieją dwa typy szyfrowania. Szyfrowanie symetryczne używa tego samego klucza zarówno do szyfrowania, jak i odszyfrowywania. Szyfrowanie asymetryczne używa różnych, ale matematycznie powiązanych kluczy do szyfrowania i odszyfrowywania.

### <a name="symmetric-encryption"></a>Szyfrowanie symetryczne

Szyfrowanie symetryczne używa tego samego klucza, aby zaszyfrować zwykły tekst do szyfru i odszyfrować zaszyfrowany tekst z powrotem do postaci zwykłego tekstu. Niezbędna długość klucza wyrażona w liczbie bitów jest określana przez algorytm. Po zaszyfrowaniu klucza w postaci zwykłego tekstu zaszyfrowana wiadomość jest wysyłana do adresata, który następnie odszyfrowuje zaszyfrowany tekst. Klucz symetryczny musi być bezpiecznie przesłany do odbiorcy. Wysyłanie klucza jest największym zagrożeniem dla bezpieczeństwa podczas korzystania z algorytmu symetrycznego.

![Przykład szyfrowania symetrycznego](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Szyfrowanie asymetryczne

Jeśli używane jest tylko szyfrowanie symetryczne, problem polega na tym, że wszystkie strony komunikacji muszą posiadać klucz prywatny. Istnieje jednak możliwość, że nieautoryzowane osoby trzecie przechwycą klucz podczas przesyłania do autoryzowanych użytkowników. Aby rozwiązać ten problem, zamiast tego użyj kryptografii klucza asymetrycznego lub publicznego.

W kryptografii asymetrycznej każdy użytkownik ma dwa matematycznie powiązane klucze nazywane parą kluczy. Jeden klucz jest publiczny, a drugi klucz prywatny. Para kluczy zapewnia, że tylko odbiorca ma dostęp do klucza prywatnego potrzebnego do odszyfrowania danych. Na poniższej ilustracji przedstawiono podsumowanie procesu szyfrowania asymetrycznego.

![Przykład szyfrowania asymetrycznego](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Odbiorca tworzy parę kluczy publiczny-prywatny i wysyła klucz publiczny do urzędu certyfikacji. Urząd certyfikacji pakuje klucz publiczny w certyfikacie X.509.

1. Strona wysyłająca uzyskuje klucz publiczny odbiorcy z urzędu certyfikacji.

1. Nadawca szyfruje dane w postaci zwykłego tekstu przy użyciu algorytmu szyfrowania. Klucz publiczny odbiorcy jest używany do szyfrowania.

1. Nadawca przesyła zaszyfrowany tekst do odbiorcy. Wysłanie klucza nie jest konieczne, ponieważ odbiorca ma już klucz prywatny wymagany do odszyfrowania szyfru.

1. Odbiorca odszyfrowuje zaszyfrowany tekst przy użyciu określonego algorytmu asymetrycznego i klucza prywatnego.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Łączenie szyfrowania symetrycznego i asymetrycznego

Szyfrowanie symetryczne i asymetryczne można połączyć, aby wykorzystać ich względne mocne strony. Szyfrowanie symetryczne jest znacznie szybsze niż szyfrowanie asymetryczne, ale ze względu na konieczność wysyłania kluczy prywatnych do innych podmiotów nie jest tak bezpieczne. Aby połączyć te dwa typy ze sobą, można użyć szyfrowania symetrycznego w celu przekonwertowania tekstu zwykłego na tekst szyfrowy. Szyfrowanie asymetryczne służy do wymiany klucza symetrycznego. Przedstawiono to na poniższym diagramie.

![Szyfrowanie symetryczne i asymetryczne](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Nadawca pobiera klucz publiczny odbiorcy.

1. Nadawca generuje klucz symetryczny i używa go do szyfrowania oryginalnych danych.

1. Nadawca używa klucza publicznego odbiorcy do szyfrowania klucza symetrycznego.

1. Nadawca przesyła zaszyfrowany klucz symetryczny i szyfrowany tekst do zamierzonego adresata.

1. Odbiorca używa klucza prywatnego, który pasuje do klucza publicznego adresata, do odszyfrowania klucza symetrycznego nadawcy.

1. Odbiorca używa klucza symetrycznego do odszyfrowania szyfru.

### <a name="asymmetric-signing"></a>Podpisywanie asymetryczne

Algorytmy asymetryczne mogą służyć do ochrony danych przed modyfikacjami i potwierdzenia tożsamości twórcy danych. Na poniższej ilustracji pokazano, jak podpisywanie asymetryczne pomaga udowodnić tożsamość nadawcy.

![Przykład podpisywania asymetrycznego](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Nadawca przekazuje dane w postaci zwykłego tekstu za pośrednictwem algorytmu szyfrowania asymetrycznego przy użyciu klucza prywatnego do szyfrowania. Należy zauważyć, że ten scenariusz odwraca użycie kluczy prywatnych i publicznych opisanych w poprzedniej sekcji, w których szczegółowo opisano szyfrowanie asymetryczne.

1. Wynikowy szyfr jest wysyłany do odbiorcy.

1. Odbiorca uzyskuje klucz publiczny inicjatora z katalogu.

1. Odbiorca odszyfrowuje szyfrowany tekst przy użyciu klucza publicznego osoby inicjatora. Wynikowy zwykły tekst potwierdza tożsamość inicjatora, ponieważ tylko inicjator ma dostęp do klucza prywatnego, który początkowo zaszyfrował oryginalny tekst.

## <a name="signing"></a>Podpisywanie

Podpisywanie cyfrowe może służyć do określania, czy dane zostały zmodyfikowane podczas przesyłania, czy też w spoczynku. Dane są przekazywane za pośrednictwem algorytmu wyznaczania wartości skrótu, jednokierunkowej funkcji, która generuje matematyczny wynik z danego komunikatu. Wynik jest nazywany wartością *skrótu,* *skrótem komunikatu,* *skrótem,* *podpisem* lub *odciskiem palca.* Wartości skrótu nie można cofnąć, aby uzyskać oryginalny komunikat. Ponieważ niewielka zmiana w komunikacie powoduje znaczącą zmianę odcisku *palca,* wartość skrótu może służyć do określenia, czy komunikat został zmieniony. Na poniższej ilustracji przedstawiono, jak można użyć szyfrowania asymetrycznego i algorytmów wyznaczania wartości skrótu w celu sprawdzenia, czy komunikat nie został zmodyfikowany.

![Przykład podpisywania](media/tutorial-x509-introduction/signing.png)

1. Nadawca tworzy komunikat w postaci zwykłego tekstu.

1. Nadawca tworzy skrót wiadomości w postaci zwykłego tekstu, aby utworzyć skrót wiadomości.

1. Nadawca szyfruje podsumowanie przy użyciu klucza prywatnego.

1. Nadawca przesyła wiadomość w postaci zwykłego tekstu i zaszyfrowany skrót do zamierzonego odbiorcy.

1. Odbiorca odszyfrowuje podsumowanie przy użyciu klucza publicznego nadawcy.

1. Odbiorca uruchamia ten sam algorytm wyznaczania wartości skrótu, który nadawca użył w wiadomości.

1. Odbiorca porównuje wynikowy podpis z odszyfrowanym podpisem. Jeśli skróty są takie same, komunikat nie został zmodyfikowany podczas przesyłania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o polach, które są certyfikatem, zobacz [Understanding X.509 Public Key Certificates (Opis certyfikatów kluczy publicznych X.509).](tutorial-x509-certificates.md)

Jeśli wiesz już wiele o certyfikatach X.509 i chcesz wygenerować wersje testowe, których można użyć do uwierzytelniania na komputerze IoT Hub, zobacz następujące tematy:

* [Tworzenie certyfikatów Microsoft-Supplied za pomocą skryptów testowych](tutorial-x509-scripts.md)
* [Tworzenie certyfikatów testowych przy użyciu programu OpenSSL](tutorial-x509-openssl.md)
* [Tworzenie certyfikatów testów Self-Signed OpenSSL](tutorial-x509-self-sign.md)

Jeśli masz certyfikat urzędu certyfikacji (CA) lub certyfikat podrzędnego urzędu certyfikacji i chcesz przekazać go do centrum IoT i udowodnić, że jesteś jego właścicielem, zobacz Dokument posiadania certyfikatu [urzędu certyfikacji](tutorial-x509-prove-possession.md).
