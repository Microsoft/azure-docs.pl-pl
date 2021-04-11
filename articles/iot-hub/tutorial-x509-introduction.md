---
title: Samouczek — Omówienie certyfikatów kryptografii i X. 509 dla platformy Azure IoT Hub | Microsoft Docs
description: Samouczek — Omówienie kryptografii i infrastruktury PKI X. 509 dla usługi Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: 88f5803e1d4348b79c7675d627a541ff47700dc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630748"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Samouczek: Omówienie kryptografii klucza publicznego i infrastruktury kluczy publicznych X. 509

Za pomocą certyfikatów X. 509 można uwierzytelniać urządzenia w usłudze Azure IoT Hub. Certyfikat jest dokumentem cyfrowym zawierającym klucz publiczny urządzenia i może służyć do sprawdzenia, czy urządzenie jest tym, co to jest. Certyfikaty X. 509 i listy odwołania certyfikatów (CRL) są udokumentowane w [dokumencie RFC 5280](https://tools.ietf.org/html/rfc5280). Certyfikaty są tylko jedną częścią infrastruktury kluczy publicznych X. 509 (PKI). Aby zrozumieć infrastrukturę PKI X. 509, należy zrozumieć algorytmy kryptograficzne, klucze kryptograficzne, certyfikaty i urzędy certyfikacji (CA):

* **Algorytmy** definiują sposób, w jaki oryginalne dane zwykłego tekstu są przekształcane w tekst szyfrowany i z powrotem do zwykłego tekstu.
* **Klucze** są losowe lub pseudolosowych ciągi danych używane jako dane wejściowe algorytmu.
* **Certyfikaty** są dokumentami cyfrowymi, które zawierają klucz publiczny jednostki i umożliwiają ustalenie, czy podmiot certyfikatu jest odpowiedzialny za lub jakie są oświadczenia.
* **Urzędy certyfikacji** poświadczają autentyczność podmiotów certyfikatów.

Certyfikat można zakupić od urzędu certyfikacji (CA). Możesz również testować i projektować lub jeśli pracujesz w środowisku własnym, Utwórz główny urząd certyfikacji z podpisem własnym. Jeśli na przykład masz własne urządzenie i testujesz uwierzytelnianie usługi IoT Hub, możesz podpisać swój główny urząd certyfikacji i używać go do wystawiania certyfikatów urządzeń. Można także wydać certyfikaty urządzeń z podpisem własnym. Jest to omówione w kolejnych artykułach.

Przed przeprowadzeniem szczegółowych informacji o certyfikatach X. 509 i użyciu ich do uwierzytelniania urządzeń w IoT Hub omówiono kryptografię, na której oparto certyfikaty.

## <a name="cryptography"></a>Kryptografia

Kryptografia jest używana do ochrony informacji i komunikacji. Jest to zazwyczaj realizowane przy użyciu technik kryptograficznych w celu zaszyfrowania zwykłego tekstu (zwykły tekst) do tekstu szyfrowanego (tekst zakodowany) i z powrotem. Ten proces szyfrowania nazywa się szyfrowaniem. Proces odwrotny nazywa się odszyfrowywaniem. Kryptografia jest objęta następującymi celami:

* **Poufność**: informacje mogą być zrozumiałe tylko dla zamierzonych odbiorców.
* **Integralność**: informacje nie mogą zostać zmienione w magazynie ani w trakcie przesyłania.
* **Brak** odrzucania: twórca informacji nie może później odmówić tego utworzenia.
* **Uwierzytelnianie**: nadawca i odbiorca mogą potwierdzić tożsamość tożsamości.

## <a name="encryption"></a>Szyfrowanie

Proces szyfrowania wymaga algorytmu i klucza. Algorytm definiuje, jak dane są przekształcane ze zwykłego tekstu na tekst szyfrowany i z powrotem do zwykłego tekstu. Klucz to losowy ciąg danych używany jako dane wejściowe algorytmu. Wszystkie zabezpieczenia procesu są zawarte w kluczu. W związku z tym klucz musi być przechowywany w bezpiecznym miejscu. Szczegółowe informacje o najpopularniejszych algorytmach są jednak dostępne publicznie.

Istnieją dwa typy szyfrowania. Szyfrowanie symetryczne używa tego samego klucza do szyfrowania i odszyfrowywania. Szyfrowanie asymetryczne używa różnych, ale matematycznie powiązanych kluczy do szyfrowania i odszyfrowywania.

### <a name="symmetric-encryption"></a>Szyfrowanie symetryczne

Szyfrowanie symetryczne używa tego samego klucza do szyfrowania zwykłego tekstu w formacie szyfrowanego i odszyfrowywania tekstu szyfrowanego z powrotem do zwykłego tekstu. Wymagana długość klucza wyrażona w liczbie bitów jest określana przez algorytm. Gdy klucz jest używany do szyfrowania zwykłego tekstu, zaszyfrowana wiadomość jest wysyłana do adresata, który następnie odszyfrowuje tekst szyfrowany. Klucz symetryczny musi być bezpiecznie przekazany do adresata. W przypadku korzystania z algorytmu symetrycznego podczas przesyłania klucza jest największe ryzyko związane z zabezpieczeniami.

![Przykład szyfrowania symetrycznego](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Szyfrowanie asymetryczne

Jeśli używane jest tylko szyfrowanie symetryczne, problem polega na tym, że wszystkie strony komunikacji muszą mieć klucz prywatny. Jednak jest możliwe, że nieupoważnione osoby trzecie mogą przechwycić klucz podczas przesyłania do autoryzowanych użytkowników. Aby rozwiązać ten problem, należy zamiast tego użyć kryptografii klucza asymetrycznego lub publicznego.

W kryptografii asymetrycznej każdy użytkownik ma dwie powiązane z nimi klucze nazywane parą kluczy. Jeden klucz jest publiczny, a drugi klucz jest prywatny. Para kluczy gwarantuje, że tylko odbiorca ma dostęp do klucza prywatnego wymaganego do odszyfrowania danych. Poniższa ilustracja zawiera podsumowanie procesu szyfrowania asymetrycznego.

![Przykład szyfrowania asymetrycznego](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Odbiorca tworzy parę kluczy publiczny-prywatny i wysyła klucz publiczny do urzędu certyfikacji. Urząd certyfikacji pakuje klucz publiczny w certyfikacie X. 509.

1. Strona wysyłająca uzyskuje klucz publiczny odbiorcy z urzędu certyfikacji.

1. Nadawca szyfruje dane w postaci zwykłego tekstu przy użyciu algorytmu szyfrowania. Klucz publiczny adresata jest używany do szyfrowania.

1. Nadawca przesyła tekst szyfrowany do adresata. Nie jest konieczne wysłanie klucza, ponieważ odbiorca ma już klucz prywatny potrzebny do odszyfrowania tekstu szyfrowanego.

1. Odbiorca odszyfrowuje tekst szyfrowany przy użyciu określonego algorytmu asymetrycznego i klucza prywatnego.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Łączenie szyfrowania symetrycznego i asymetrycznego

Szyfrowanie symetryczne i asymetryczne można łączyć, aby wykorzystać ich względną siłę. Szyfrowanie symetryczne jest znacznie szybsze niż asymetryczny, ale ze względu na konieczność przesyłania kluczy prywatnych innym stronom nie jest to bezpieczne. Aby połączyć dwa typy, można użyć szyfrowania symetrycznego do przekonwertowania zwykłego tekstu na tekst szyfrowany. Szyfrowanie asymetryczne służy do wymiany klucza symetrycznego. Jest to zademonstrowane na poniższym diagramie.

![Szyfrowanie symetryczne i assymetric](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Nadawca Pobiera klucz publiczny adresata.

1. Nadawca generuje klucz symetryczny i używa go do szyfrowania oryginalnych danych.

1. Nadawca używa klucza publicznego odbiorcy do szyfrowania klucza symetrycznego.

1. Nadawca przesyła zaszyfrowany klucz symetryczny i tekst szyfrowany do zamierzonego odbiorcy.

1. Odbiorca korzysta z klucza prywatnego, który pasuje do klucza publicznego odbiorcy, aby odszyfrować klucz symetryczny nadawcy.

1. Odbiorca używa klucza symetrycznego do odszyfrowania tekstu szyfrowanego.

### <a name="asymmetric-signing"></a>Podpisywanie asymetryczne

Algorytmy asymetryczne mogą służyć do ochrony danych przed modyfikacją i udowodnić tożsamość twórcy danych. Na poniższej ilustracji pokazano, jak podpisywanie asymetryczne pomaga potwierdzić tożsamość nadawcy.

![Przykład podpisywania asymetrycznego](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Nadawca przekazuje dane zwykłego tekstu za pośrednictwem algorytmu szyfrowania asymetrycznego przy użyciu klucza prywatnego do szyfrowania. Należy zauważyć, że w tym scenariuszu użyto kluczy prywatnych i publicznych przedstawionych w poprzedniej sekcji, które szczegółowo deszyfrowanie asymetryczne.

1. Wyniki tego tekstu są wysyłane do adresata.

1. Odbiorca uzyskuje klucz publiczny z katalogu.

1. Odbiorca odszyfrowuje tekst szyfrowany przy użyciu klucza publicznego programu inicjującego. Wynikowy tekst potwierdza tożsamość osoby inicjującej, ponieważ tylko nadawca ma dostęp do klucza prywatnego, który początkowo zaszyfrował oryginalny tekst.

## <a name="signing"></a>Podpisywanie

Podpis cyfrowy może służyć do określenia, czy dane zostały zmodyfikowane podczas przesyłania czy w spoczynku. Dane są przekazywane przez algorytm wyznaczania wartości skrótu, jednokierunkowa funkcja, która generuje wynik matematyczny z danego komunikatu. Wynik jest określany jako *wartość skrótu*, *skrót wiadomości*, *skrót*, *podpis* lub *odcisk palca*. Nie można odwrócić wartości skrótu w celu uzyskania oryginalnej wiadomości. Ponieważ niewielka zmiana w komunikacie powoduje znaczącą zmianę *odcisku palca*, wartość skrótu może służyć do określenia, czy komunikat został zmieniony. Na poniższej ilustracji przedstawiono sposób szyfrowania asymetrycznego i algorytmów wyznaczania wartości skrótu w celu sprawdzenia, czy komunikat nie został zmodyfikowany.

![Przykład podpisywania](media/tutorial-x509-introduction/signing.png)

1. Nadawca tworzy wiadomość w postaci zwykłego tekstu.

1. Wartość skrótu nadawcy wiadomości w postaci zwykłego tekstu w celu utworzenia skrótu wiadomości.

1. Nadawca szyfruje podsumowanie przy użyciu klucza prywatnego.

1. Nadawca przesyła wiadomość w postaci zwykłego tekstu i zaszyfrowany skrót do zamierzonego odbiorcy.

1. Odbiorca odszyfrowuje podsumowanie przy użyciu klucza publicznego nadawcy.

1. Odbiorca uruchamia ten sam algorytm wyznaczania wartości skrótu, którego nadawca użył w komunikacie.

1. Odbiorca porównuje otrzymany podpis z odszyfrowanym podpisem. Jeśli skróty są takie same, komunikat nie został zmodyfikowany podczas transmisji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat pól, które składają się na certyfikat, zobacz [Opis certyfikatów kluczy publicznych X. 509](tutorial-x509-certificates.md).

Jeśli znasz już wiele certyfikatów X. 509 i chcesz wygenerować wersje testowe, których można użyć do uwierzytelniania w IoT Hub, zobacz następujące tematy:

* [Tworzenie certyfikatów testowych przy użyciu skryptów Microsoft-Supplied](tutorial-x509-scripts.md)
* [Tworzenie certyfikatów testowych przy użyciu OpenSSL](tutorial-x509-openssl.md)
* [Tworzenie Self-Signed certyfikatów testowych przy użyciu programu OpenSSL](tutorial-x509-self-sign.md)

Jeśli masz certyfikat urzędu certyfikacji lub podrzędnego urzędu certyfikacji i chcesz przekazać go do centrum IoT i udowodnić, że jesteś jego własnością, zobacz [dowód posiadania certyfikatu urzędu certyfikacji](tutorial-x509-prove-possession.md).
