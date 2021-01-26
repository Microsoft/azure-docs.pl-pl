---
title: IoT Hub Device Provisioning Service — role i operacje
description: Ten artykuł zawiera omówienie pojęć dotyczących ról i operacji związanych z tworzeniem i przetwarzaniem rozwiązań IoT przy użyciu usługi IoT Device Provisioning (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 322d6b590863d8065454c0439c5d899107a6abe7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784969"
---
# <a name="roles-and-operations"></a>Role i operacje

Fazy tworzenia rozwiązania IoT mogą obejmować tygodnie lub miesiące, ze względu na rzeczywiste działania produkcyjne, takie jak czas produkcji, wysyłka, proces celny itp. Ponadto mogą obejmować działania w wielu rolach, w których występują różne jednostki. W tym temacie zawarto szczegółowe zapoznaj się z różnymi rolami i operacjami związanymi z każdą fazą, a następnie ilustruje przepływ w diagramie sekwencji. 

Inicjowanie obsługi umieszcza również wymagania dotyczące producenta urządzenia, specyficzne dla włączenia [mechanizmu zaświadczania](concepts-service.md#attestation-mechanism). Operacje produkcyjne mogą również odbywać się niezależnie od chronometrażu etapów autouzupełniania, szczególnie w przypadku, gdy nowe urządzenia są nabywane po ustanowieniu autoaprowizacji.

Seria przewodników Szybki Start znajduje się w spisie treści po lewej stronie, aby pomóc w wyjaśnieniu samoobsługowego udostępniania przy użyciu praktycznego środowiska. Aby ułatwić/uprościć proces uczenia, oprogramowanie jest używane do symulowania urządzenia fizycznego na potrzeby rejestracji i rejestracji. Niektóre Przewodniki Szybki Start wymagają wykonania operacji dla wielu ról, w tym operacji dla nieistniejących ról, ze względu na symulowany charakter przewodników Szybki Start.

| Rola | Operacja | Opis |
|------| --------- | ------------|
| Producent | Koduj tożsamość i adres URL rejestracji | Na podstawie używanego mechanizmu zaświadczania producent jest odpowiedzialny za kodowanie informacji o tożsamości urządzenia i adres URL rejestracji usługi Device Provisioning.<br><br>**Przewodniki Szybki Start**: od momentu symulowania urządzenia nie ma roli producenta. Zapoznaj się z rolą dewelopera, aby uzyskać szczegółowe informacje na temat sposobu uzyskiwania tych informacji, które są używane do kodowania przykładowej aplikacji do rejestracji. |
| | Podaj tożsamość urządzenia | Jako podmiot nadawczy informacji o tożsamości urządzenia producent jest odpowiedzialny za komunikowanie się z operatorem (lub wskazanym agentem) lub bezpośrednio z rejestracją w usłudze Device Provisioning za pośrednictwem interfejsów API.<br><br>**Przewodniki Szybki Start**: od momentu symulowania urządzenia nie ma roli producenta. Zobacz rolę operatora, aby uzyskać szczegółowe informacje na temat sposobu uzyskiwania tożsamości urządzenia, która jest używana do rejestrowania symulowanego urządzenia w wystąpieniu usługi Device Provisioning. |
| Operator | Konfigurowanie autoaprowizacji | Ta operacja odpowiada pierwszej fazie inicjowania obsługi.<br><br>**Przewodniki Szybki Start**: wykonywanie roli operatora, Konfigurowanie usługi Device Provisioning i wystąpień IoT Hub w ramach subskrypcji platformy Azure. |
|  | Rejestrowanie tożsamości urządzenia | Ta operacja odpowiada drugiej fazie aprowizacji samoobsługowego.<br><br>**Przewodniki Szybki Start**: wykonujesz rolę operatora, rejestrując urządzenie symulowane w wystąpieniu usługi Device Provisioning. Tożsamość urządzenia jest określana przez metodę zaświadczania symulowaną w przewodniku szybki start (TPM lub X. 509). Szczegółowe informacje o zaświadczeniu można znaleźć w roli dewelopera. |
| Usługa Device Provisioning,<br>Usługa IoT Hub | \<all operations\> | W przypadku wdrożenia produkcyjnego z urządzeniami fizycznymi i przewodników szybki start z symulowanymi urządzeniami te role są spełnione za pośrednictwem usług IoT skonfigurowanych w ramach subskrypcji platformy Azure. Role/operacje działają dokładnie tak samo, co usługi IoT różnią się w zależności od aprowizacji urządzeń fizycznych a symulowanych. |
| Deweloper | Kompiluj/Wdróż oprogramowanie rejestracyjne | Ta operacja odpowiada trzeciej fazie inicjowania obsługi. Deweloper jest odpowiedzialny za tworzenie i wdrażanie oprogramowania do rejestracji na urządzeniu przy użyciu odpowiedniego zestawu SDK.<br><br>**Przewodniki Szybki Start**: kompilacja przykładowej aplikacji do rejestracji symuluje rzeczywiste urządzenie dla wybranej platformy/języka, który działa na stacji roboczej (zamiast wdrażać ją na urządzeniu fizycznym). Aplikacja do rejestracji wykonuje te same operacje, które zostały wdrożone na urządzeniu fizycznym. Należy określić metodę zaświadczania (Certyfikat modułu TPM lub X. 509) oraz adres URL rejestracji i "zakres identyfikatorów" wystąpienia usługi Device Provisioning Service. Tożsamość urządzenia jest określana przez logikę zaświadczania zestawu SDK w czasie wykonywania na podstawie określonej metody: <ul><li>**Zaświadczanie modułu TPM** — na stacji roboczej deweloperskiej jest uruchomiona [aplikacja symulatora modułu TPM](quick-create-simulated-device.md). Po uruchomieniu oddzielna aplikacja jest używana do wyodrębnienia "klucza poręczenia" i "Identyfikator rejestracji" do użycia podczas rejestrowania tożsamości urządzenia. Logika zaświadczania zestawu SDK używa również symulatora podczas rejestracji, aby przedstawić podpisany token sygnatury dostępu współdzielonego w celu uwierzytelniania i weryfikacji rejestracji.</li><li>**Zaświadczanie** [o certyfikatach x509 — wygenerowanie certyfikatu](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain)jest możliwe za pomocą narzędzia. Po wygenerowaniu należy utworzyć plik certyfikatu wymagany do użycia podczas rejestracji. Logika zaświadczania zestawu SDK używa również certyfikatu podczas rejestracji, aby można było zaprezentować uwierzytelnianie i weryfikację rejestracji.</li></ul> |
| Urządzenie | Rozruchu i zarejestruj | Ta operacja jest zgodna z trzecią fazą samoobsługowego inicjowania obsługi przez oprogramowanie do rejestracji urządzeń utworzone przez dewelopera. Aby uzyskać szczegółowe informacje, zobacz rolę dewelopera. Po pierwszym rozruchu: <ol><li>Aplikacja nawiązuje połączenie z wystąpieniem usługi Device Provisioning, używając globalnego adresu URL i usługi "Scope ID" określonego podczas tworzenia.</li><li>Po nawiązaniu połączenia urządzenie jest uwierzytelniane w oparciu o metodę zaświadczania i tożsamość określoną podczas rejestracji.</li><li>Po uwierzytelnieniu urządzenie zostanie zarejestrowane przy użyciu wystąpienia IoT Hub określonego przez wystąpienie usługi aprowizacji.</li><li>Po pomyślnej rejestracji unikatowy identyfikator urządzenia i punkt końcowy IoT Hub są zwracane do aplikacji rejestracji w celu komunikowania się z IoT Hub.</li><li> Z tego miejsca urządzenie może pobrać stan wstępnej [splotu urządzenia](~/articles/iot-hub/iot-hub-devguide-device-twins.md) w celu skonfigurowania i rozpocząć proces raportowania danych telemetrycznych.</li></ol>**Przewodniki Szybki Start**: ponieważ urządzenie jest symulowane, oprogramowanie do rejestracji działa na stacji roboczej deweloperskiej.|

Poniższy diagram podsumowuje role i sekwencjonowanie operacji podczas samoobsługowego dostarczania urządzeń:
<br><br>
[![Sekwencja autoaprowizacji dla urządzenia](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcjonalnie producent może również wykonać operację "Zarejestruj tożsamość urządzenia" przy użyciu interfejsów API usługi Device Provisioning (zamiast za pośrednictwem operatora). Aby uzyskać szczegółową dyskusję na temat tej sekwencji i innych informacji, zobacz temat [Rejestracja urządzenia dotykowego w usłudze Azure IoT wideo](https://youtu.be/cSbDRNg72cU?t=2460) (od znacznika 41:00).

## <a name="roles-and-azure-accounts"></a>Role i konta platformy Azure

Sposób, w jaki każda rola jest mapowana na konto platformy Azure, jest zależna od scenariusza i istnieje kilka scenariuszy, które mogą być używane. Typowe wzorce poniżej powinny pomóc w ustaleniu ogólnego zagadnienia dotyczącego sposobu, w jaki role są zwykle mapowane na konto platformy Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Producent mikroukładu dostarcza usługi zabezpieczeń

W tym scenariuszu producent zarządza zabezpieczeniami dla klientów poziomu jednego. Ten scenariusz może być preferowany przez ten poziom — jeden klient nie musi zarządzać szczegółowymi zabezpieczeniami. 

Producent wprowadza zabezpieczenia do sprzętowych modułów zabezpieczeń (sprzętowych modułów zabezpieczeń). Zabezpieczenia te mogą obejmować producentom uzyskanie kluczy, certyfikatów itp. od potencjalnych klientów, którzy mają już wystąpienia usługi DPS i konfigurację grup rejestracji. Producent może również generować te informacje o zabezpieczeniach dla swoich klientów.

W tym scenariuszu mogą być uwzględnione dwa konta platformy Azure:

- **#1 konta**: możliwe, że w ramach ról operatora i dewelopera chcesz mieć pewien stopień. Ta strona może zakupić wióry modułu HSM od producenta. Te wióry są wskazywane dla wystąpień DPS skojarzonych z #1 konta. W przypadku rejestracji w usłudze DPS ta strona może dzierżawić urządzenia wielu klientom z wieloma poziomami przez ponowne skonfigurowanie ustawień rejestracji urządzeń w usłudze DPS. Ta strona może również mieć przypisane centra IoT przeznaczone dla systemów zaplecza użytkowników końcowych do interfejsów w programie w celu uzyskania dostępu do telemetrii urządzenia itp. W tym drugim przypadku drugie konto może nie być konieczna.

- **#2 konta**: użytkownicy końcowi, poziom — dwaj klienci mogą mieć własne centra IoT Hub. Strona skojarzona z kontem #1 jedynie punkty dzierżawione urządzenia do właściwego centrum w ramach tego konta. Ta konfiguracja wymaga łączenia usługi DPS i centrów IoT na kontach platformy Azure, które można wykonać za pomocą szablonów Azure Resource Manager.

#### <a name="all-in-one-oem"></a>Wszyscy producenci OEM

Producent może być "Wszyscy producenci OEM", w przypadku którego będzie wymagana tylko jedno konto producenta. Producent obsługuje kompleksowe zabezpieczenia i Inicjowanie obsługi administracyjnej.

Producent może udostępnić aplikacji opartej na chmurze klientom, którzy kupują urządzenia. Ta aplikacja będzie mieć interfejs z IoT Hub przyznanym przez producenta.

W tym scenariuszu maszyny sprzedaży lub zautomatyzowane komputery kawowe reprezentują przykłady.




## <a name="next-steps"></a>Następne kroki

Pomocne może być zakładanie tego artykułu jako punktu odniesienia podczas pracy przez odpowiednie Przewodniki Szybki Start dotyczące samoobsługowego udostępniania. 

Zacznij od wykonania kroku "Konfigurowanie automatycznej aprowizacji" przewodnika Szybki Start, który najlepiej odpowiada preferencjom narzędzia do zarządzania, które przeprowadzi Cię przez fazę "Konfiguracja usługi":

- [Konfigurowanie samoobsługowego inicjowania obsługi przy użyciu interfejsu wiersza polecenia platformy Azure](quick-setup-auto-provision-cli.md)
- [Skonfiguruj funkcję autoaprowizacji przy użyciu Azure Portal](quick-setup-auto-provision.md)
- [Konfigurowanie samoobsługowego inicjowania obsługi przy użyciu szablonu Menedżer zasobów](quick-setup-auto-provision-rm.md)

Następnie przejdź do przewodnika Szybki Start "Inicjowanie obsługi urządzenia", który odpowiada mechanizmowi zaświadczania urządzenia i zestawie SDK/języka usługi aprowizacji urządzeń. W tym przewodniku szybki start przeprowadzisz etapy "rejestracja urządzeń" i "rejestracja urządzeń i konfiguracji": 

| Mechanizm zaświadczania urządzenia | Zestaw SDK/język szybkiego startu | 
| ------------------------------- | -------------------- |
| Klucz symetryczny | [S](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| Certyfikat X. 509 | [S](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Symulowane Trusted Platform Module (TPM) | [S](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




