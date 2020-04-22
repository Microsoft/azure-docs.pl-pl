---
title: Omówienie usługi Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Zawiera opis inicjowania obsługi administracyjnej urządzeń na platformie Azure za pomocą usługi inicjowania obsługi administracyjnej urządzeń (DPS) i usługi IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 1b12886ee55741f62a1156269423ffadd34cd433
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683305"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Aprowizowanie urządzeń za pomocą usługi Azure IoT Hub Device Provisioning Service
Platforma Microsoft Azure zapewnia bogaty zestaw zintegrowanych usług w chmurze publicznej spełniający wszelkie potrzeby rozwiązania IoT. Usługa inicjowania obsługi administracyjnej (DPS) centrum IoT hub jest usługą pomocniczą dla usługi IoT Hub, która umożliwia inicjowanie obsługi administracyjnej typu "tylko w czasie" do odpowiedniego centrum IoT bez konieczności interwencji człowieka. Dps umożliwia inicjowanie obsługi administracyjnej milionów urządzeń w sposób bezpieczny i skalowalny.

## <a name="when-to-use-device-provisioning-service"></a>Kiedy stosować usługę Device Provisioning Service
Istnieje wiele scenariuszy inicjowania obsługi administracyjnej, w których DPS jest doskonałym wyborem do uzyskiwania urządzeń podłączonych i skonfigurowanych do usługi IoT Hub, takich jak:

* Bezobsługowe aprowizowanie w jednym rozwiązaniu IoT bez trwałego kodowania informacji o połączeniu z usługą IoT Hub w fabryce (konfiguracja początkowa)
* Urządzenia równoważące obciążenie w wielu koncentratorach
* Łączenie urządzeń z rozwiązaniem IoT właściciela na podstawie danych transakcji sprzedaży (multitenancy)
* Łączenie urządzeń z określonym rozwiązaniem IoT w zależności od przypadku użycia (izolacja rozwiązania)
* Łączenie urządzenia z centrum IoT o najmniejszym opóźnieniu (geograficzne dzielenie na fragmenty)
* Ponowne aprowizowanie na podstawie zmiany w urządzeniu
* Przerzucanie kluczy używanych przez urządzenie do nawiązywania połączenia z usługą IoT Hub (gdy na potrzeby połączenia nie są używane certyfikaty X.509)

## <a name="behind-the-scenes"></a>Za kulisami
Wszystkie scenariusze wymienione w poprzedniej sekcji można wykonać przy użyciu dps dla obsługi administracyjnej bez dotyku z tego samego przepływu. Wiele ręcznych kroków tradycyjnie zaangażowanych w inicjowanie obsługi administracyjnej są zautomatyzowane z DPS, aby skrócić czas wdrażania urządzeń IoT i zmniejszyć ryzyko błędu ręcznego. W poniższej sekcji opisano, co dzieje się za kulisami, aby aprowizować urządzenie. Pierwszy krok jest ręczny, a wszystkie następne kroki są zautomatyzowane.

![Podstawowy przepływ aprowizowania](./media/about-iot-dps/dps-provisioning-flow.png)

1. Producent urządzenia dodaje jego informacje o rejestracji do listy rejestracji w witrynie Azure Portal.
2. Urządzenie styka się z punktem końcowym DPS ustawionym fabrycznie. Urządzenie przekazuje informacje identyfikacyjne do DPS, aby udowodnić swoją tożsamość.
3. DPS sprawdza poprawność tożsamości urządzenia, sprawdzając identyfikator rejestracji i klucz względem wpisu listy rejestracji przy użyciu wyzwania typu nonce[(Moduł zaufanej platformy)](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)lub standardowej weryfikacji X.509 (X.509).
4. DPS rejestruje urządzenie za pomocą koncentratora IoT i wypełnia [żądany stan bliźniaczej.](../iot-hub/iot-hub-devguide-device-twins.md)
5. Centrum IoT zwraca informacje o identyfikatorze urządzenia do dps.
6. DPS zwraca informacje o połączeniu ioT hub do urządzenia. Urządzenie może teraz rozpocząć przesyłanie danych bezpośrednio do centrum IoT.
7. Urządzenie łączy się z centrum IoT.
8. Urządzenie pobiera żądany stan ze swojej bliźniaczej reprezentacji urządzenia w centrum IoT.

## <a name="provisioning-process"></a>Proces aprowizacji
Istnieją dwa odrębne kroki w procesie wdrażania urządzenia, w którym dps bierze udział, który można wykonać niezależnie:

* **Krok produkcji**, w ramach którego urządzenie jest tworzone i przygotowywane w fabryce.
* **Krok konfiguracji chmury**, w ramach którego usługa Device Provisioning Service jest konfigurowana na potrzeby automatycznego aprowizowania.

Oba te kroki bezproblemowo wpisują się w istniejące procesy produkcji i wdrażania. Dps upraszcza nawet niektóre procesy wdrażania, które wymagają pracy ręcznej, aby uzyskać informacje o połączeniu na urządzeniu.

### <a name="manufacturing-step"></a>Krok produkcji
Ten krok obejmuje wszystko to, co dzieje się na linii produkcyjnej. Role biorące udział w tym kroku to projektant układów scalonych, producent układów scalonych, integrator i/lub końcowy producent urządzenia. Ten krok dotyczy tworzenia samego sprzętu.

DPS nie wprowadza nowego kroku w procesie produkcyjnym; wiąże się raczej z istniejącym krokiem, który instaluje początkowe oprogramowanie i (najlepiej) modułem HSM na urządzeniu. W tym kroku, zamiast tworzyć identyfikator urządzenia, urządzenie jest programowane tak, aby zawierało informacje o usłudze aprowizowania, co umożliwia mu wywołanie usługi aprowizowania w celu uzyskania informacji dotyczących połączenia/przypisania do rozwiązania IoT po jego włączeniu.

Ponadto w tym kroku producent dostarcza osobie wdrażającej urządzenia/operatorowi urządzeń informacje o kluczu identyfikacyjnym. Dostarczenie tych informacji może być proste (np. potwierdzenie, że wszystkie urządzenia mają certyfikat X.509 wygenerowany na podstawie certyfikatu podpisywania udostępnionego przez osobę wdrażającą urządzenia/operatora urządzeń) lub skomplikowane (np. wyodrębnienie publicznej części klucza poręczenia modułu TPM z każdego urządzenia TPM). Te usługi są obecnie oferowane przez wielu producentów układów scalonych.

### <a name="cloud-setup-step"></a>Krok konfiguracji chmury
Ten krok dotyczy konfigurowania chmury na potrzeby odpowiedniego aprowizowania automatycznego. Zasadniczo w krok konfiguracji chmury są zaangażowane dwa typy użytkowników: ktoś, kto wie, jak urządzenia mają być wstępnie skonfigurowane (operator urządzeń) oraz ktoś inny, kto wie, jak urządzenia mają być podzielone między centrami IoT (operator rozwiązania).

Należy wykonać jednorazową początkową konfigurację aprowizowania. Zwykle zajmuje się tym operator rozwiązania. Gdy usługa aprowizowania zostanie skonfigurowana, nie trzeba jej modyfikować, chyba że przypadek użycia ulegnie zmianie.

Po skonfigurowaniu usługi na potrzeby automatycznego aprowizowania musi ona zostać przygotowana do rejestrowania urządzeń. Ten krok jest wykonywany przez operatora urządzeń, który zna żądaną konfigurację urządzeń i odpowiada za zapewnienie, że usługa aprowizowania może prawidłowo potwierdzić tożsamość urządzenia, gdy będzie ono wyszukiwać swoje centrum IoT. Operator urządzeń dodaje informacje o kluczu identyfikacyjnym, uzyskane od producenta, do listy rejestracji. Lista rejestracji może być później aktualizowana przez dodawanie nowych wpisów lub aktualizowanie istniejących przy użyciu najnowszych informacji o urządzeniach.

## <a name="registration-and-provisioning"></a>Rejestracja i aprowizowanie
*Aprowizowanie* w zależności od branży, w której ten termin jest używany, może oznaczać coś innego. W kontekście aprowizowania urządzeń IoT w ich rozwiązaniu w chmurze aprowizowanie to proces składający się z dwóch części:

1. Pierwsza część to ustanowienie połączenia początkowego między urządzeniem a rozwiązaniem IoT przez rejestrację urządzenia.
2. Druga część to zastosowanie odpowiedniej konfiguracji do urządzenia na podstawie określonych wymagań rozwiązania, w którym zostało ono zarejestrowane.

Po wykonaniu tych dwóch kroków można powiedzieć, że urządzenie zostało w pełni aprowizowane. Niektóre usługi w chmurze umożliwiają wykonanie tylko pierwszego kroku tego procesu aprowizowania przez zarejestrowanie urządzeń w punkcie końcowym rozwiązania IoT, ale nie zapewniają konfiguracji początkowej. Dps automatyzuje oba kroki, aby zapewnić bezproblemowe środowisko inicjowania obsługi administracyjnej dla urządzenia.

## <a name="features-of-the-device-provisioning-service"></a>Funkcje usługi Device Provisioning Service
DPS ma wiele funkcji, dzięki czemu idealnie nadaje się do inicjowania obsługi administracyjnej urządzeń.

* Obsługa **bezpiecznego zaświadczania** dla tożsamości opartych zarówno na standardzie X.509, jak i module TPM.
* **Lista rejestracji** zawierająca pełny rejestr urządzeń/grup urządzeń, które w pewnym momencie mogą zostać zarejestrowane. Lista rejestracji zawiera informacje dotyczące żądanej konfiguracji urządzenia po jego zarejestrowaniu i może być aktualizowana w dowolnym momencie.
* **Wiele zasad alokacji** do kontrolowania sposobu, w jaki DPS przypisuje urządzenia do centrów IoT w celu obsługi scenariuszy: najniższe opóźnienie, równomiernie ważona dystrybucja (domyślna) i konfiguracja statyczna za pośrednictwem listy rejestracji. Opóźnienie jest określane przy użyciu tej samej metody co [Usługa Traffic Manager.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance)
* **Monitorowanie i rejestrowanie diagnostyczne** w celu zapewnienia poprawnego działania.
* **Obsługa wielu koncentratorów** umożliwia dps przypisać urządzenia do więcej niż jednego centrum IoT hub. DPS może rozmawiać z koncentratorami w wielu subskrypcjach platformy Azure.
* **Obsługa między regionami** umożliwia dps przypisać urządzenia do centrów IoT w innych regionach.
* **Szyfrowanie danych w stanie spoczynku** umożliwia szyfrowanie i odszyfrowywanie danych w DPS za pomocą 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych i zgodnego ze standardem FIPS 140-2.


Więcej informacji o pojęciach i funkcjach z zakresu aprowizowania urządzeń możesz znaleźć w artykułach na temat [pojęć dotyczących urządzeń](concepts-device.md), [pojęć dotyczących usługi](concepts-service.md) oraz [pojęć dotyczących zabezpieczeń](concepts-security.md).

## <a name="cross-platform-support"></a>Obsługa wielu platform
Podobnie jak wszystkie usługi Azure IoT, DPS działa między platformami z różnymi systemami operacyjnymi. Platforma Azure oferuje zestawy SDK typu open source w wielu różnych [językach](https://github.com/Azure/azure-iot-sdks), aby usprawnić łączenie urządzeń i zarządzanie usługą. Dps obsługuje następujące protokoły do podłączania urządzeń:

* HTTPS
* AMQP
* AMQP za pośrednictwem obiektów Web Socket
* MQTT
* MQTT za pośrednictwem obiektów Web Socket

Dps obsługuje tylko połączenia HTTPS dla operacji usługi.

## <a name="regions"></a>Regiony
DPS jest dostępny w wielu regionach. Aktualna lista obecnych i nowo ogłoszonych regionów dla wszystkich usług jest dostępna na stronie [Regiony świadczenia usługi Azure](https://azure.microsoft.com/regions/). Dostępność usługi Device Provisioning Service można sprawdzić na stronie [Stan platformy Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> DPS jest globalny i nie jest powiązany z lokalizacją. Należy jednak określić region, w którym będą znajdować się metadane skojarzone z profilem DPS.

## <a name="availability"></a>Dostępność
Istnieje 99,9% Service Level Agreement dla DPS, i można [przeczytać SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

## <a name="quotas"></a>Przydziały
W każdej subskrypcji platformy Azure obowiązują domyślne limity przydziału, które mogą mieć wpływ na zakres rozwiązania IoT. Aktualny limit dla każdej subskrypcji wynosi 10 usług Device Provisioning Service na subskrypcję.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Więcej informacji na temat limitów przydziałów znajduje się w artykułach:
* [Azure Subscription Service Limits](../azure-resource-manager/management/azure-subscription-service-limits.md) (Limity usług subskrypcji platformy Azure)

## <a name="related-azure-components"></a>Powiązane składniki platformy Azure
DPS automatyzuje inicjowanie obsługi administracyjnej urządzeń za pomocą usługi Azure IoT Hub. Dowiedz się więcej na temat usługi [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Następne kroki
Teraz ogólnie wiesz, na czym polega aprowizowanie urządzeń IoT na platformie Azure. Następnym krokiem jest wypróbowanie kompletnego scenariusza IoT.
> [!div class="nextstepaction"]
> [Konfigurowanie usługi inicjowania obsługi administracyjnej urządzeń w centrum IoT za pomocą portalu](quick-setup-auto-provision.md)
> Azure[Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md)
> [Konfigurowanie urządzenia do inicjowania obsługi administracyjnej](tutorial-set-up-device.md)
