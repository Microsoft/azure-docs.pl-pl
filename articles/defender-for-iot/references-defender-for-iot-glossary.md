---
title: Słownik usługi Defender for IoT
description: Ten słownik zawiera krótki opis ważnych warunków i postanowień dotyczących platformy IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 62d1f3e195247682dab96ec258d1b97695b20103
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986992"
---
# <a name="defender-for-iot-glossary"></a>Słownik usługi Defender for IoT

Ten słownik zawiera krótki opis ważnych pojęć i koncepcji dotyczących platformy Azure Defender for IoT. Wybierz linki **Dowiedz się więcej** , aby przejść do powiązanych postanowień w słowniku. Pomoże to szybciej poznać i korzystać z narzędzi produktów.

## <a name="a"></a>A

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Grupa dostępu** | Obsługuj wymagania dotyczące dostępu użytkowników w dużych organizacjach, tworząc reguły grupy dostępu.<br /><br />Reguły pozwalają kontrolować dostęp do widoku i konfiguracji do usługi Defender dla lokalnej konsoli zarządzania usługi IoT dla określonych ról użytkownika w odpowiednich jednostkach, regionach, lokacjach i strefach firmy.<br /><br />Na przykład Zezwól analitykom zabezpieczeń z grupy Active Directory na dostęp do zachodnich danych motoryzacyjnych o Europie, ale uniemożliwiają dostęp do danych w Afryce. | **[Lokalna Konsola zarządzania](#o)** <br /><br />**[Jednostka biznesowa](#b)** |
| **Tokeny dostępu** | Generuj tokeny dostępu, aby uzyskać dostęp do interfejsu API REST usługi Defender dla usługi IoT. | **[Interfejs API](#a)** |
| **Potwierdzenie zdarzenia alertu** | Poinstruuj usługę Defender for IoT, aby ukryć alert dla wykrytego zdarzenia. Alert zostanie wyzwolony ponownie, jeśli zdarzenie zostanie wykryte ponownie. | **[](#a) <br /> Alert <br /> [Informacje o zdarzeniu](#l) <br /> <br /> alertu [Wycisz zdarzenie alertu](#m)** |
| **Alert** | Komunikat, że wyzwalacz usługi Defender for IoT dotyczy odchyleń od autoryzowanych zachowań sieciowych, anomalii sieci lub podejrzanych działań i ruchu sieciowego. | **[Reguła przekazywania](#f) <br /> <br /> [](#e) <br /> Reguła <br /> wykluczania [Powiadomienia systemowe](#s)** |
| **Komentarz alertu** | Komentarze dla analityków zabezpieczeń i administratorów w komunikatach alertów. Na przykład komentarz dotyczący alertów może zawierać instrukcje dotyczące działań zaradczych w celu podjęcia lub nazwiska osób kontaktowych się ze zdarzeniem.<br /><br />Użytkownicy przeglądający alerty mogą wybrać komentarz lub komentarze najlepiej odzwierciedlające stan zdarzenia lub kroki, które należy wykonać w celu zbadania alertu. | **[Alerty](#a)** |
| **Aparat anomalii** | Aparat usługi Defender for IoT wykrywający nietypową komunikację między komputerami i zachowaniem (M2M). Na przykład aparat może wykryć nadmierne próby logowania przy użyciu protokołu SMB. Alerty anomalii są wyzwalane po wykryciu tych zdarzeń. | **[Aparat usługi Defender dla usługi IoT](#d)** |
| **Interfejs API** | Umożliwia zewnętrznym systemom dostęp do danych wykrytych przez usługę Defender for IoT i wykonywanie akcji przy użyciu zewnętrznego interfejsu API REST za pośrednictwem połączeń SSL. | **[Tokeny dostępu](#a)** |
| **Raport wektora ataków** | Graficzna reprezentacja sieci w czasie rzeczywistym w łańcuchu luk w zabezpieczeniach z możliwością wykorzystania punktów końcowych.<br /><br />Raporty pozwalają oszacować efekt działań zaradczych w sekwencji ataków, aby określić. Można na przykład sprawdzić, czy uaktualnienie systemu zakłóca ścieżkę osoby atakującej przez przerwanie łańcucha ataków, czy alternatywną ścieżkę ataku. Spowoduje to określenie priorytetów działań naprawczych i zaradczych. | **[Raport z oceny ryzyka](#r)** |

## <a name="b"></a>B

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Jednostka biznesowa** | Logiczna organizacja firmy według określonych branż.<br /><br />Na przykład firma globalna, która zawiera fabryki szklane i fabryki plastikowe, może być zarządzana jako dwie różne jednostki biznesowe. Dostęp do usługi Defender dla użytkowników IoT można kontrolować w określonych jednostkach roboczych. | **[Lokalna Konsola zarządzania](#o) <br /> <br /> [Grupa dostępu](#o) <br /> <br /> [](#s) <br /> Witryna <br /> [Strefa](#z)** |
| **Punkt odniesienia** | Zatwierdzony ruch sieciowy, protokoły, polecenia i urządzenia. Usługa Defender for IoT określa odchylenia od linii bazowej sieci. Wyświetlanie zatwierdzonego ruchu bazowego przez generowanie raportów wyszukiwania danych. | **[Wyszukiwanie danych](#d) <br /> <br /> [Tryb uczenia](#l)** |

## <a name="c"></a>C

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Polecenia interfejsu wiersza polecenia** | Opcje interfejsu wiersza polecenia (CLI) dla usługi Defender dla użytkowników administratorów IoT. Poleceń interfejsu wiersza polecenia są dostępne dla funkcji, do których nie można uzyskać dostępu za pomocą konsoli usługi Defender dla usługi IoT. | - |


## <a name="d"></a>D

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Wyszukiwanie danych** | Generowanie pełnych i szczegółowych raportów dotyczących urządzeń sieciowych:<br /><br />- **Odpowiedź na zdarzenia SOC**: raporty w czasie rzeczywistym, aby pomóc w rozproszeniu natychmiastowego reagowania na zdarzenia. Na przykład raport może zawierać listę urządzeń, które mogą wymagać zastosowania poprawek.<br /><br />- **Dowodowych**: raporty oparte na danych historycznych dla raportów dochodzeniowych.<br /><br />- **Integralność sieci IT**: raporty, które pomagają ulepszyć ogólne zabezpieczenia sieci. Na przykład raport może wyświetlać listę urządzeń z słabymi poświadczeniami uwierzytelniania.<br /><br />- **widoczność**: raporty, które obejmują wszystkie elementy zapytania, aby wyświetlić wszystkie parametry linii bazowej sieci.<br /><br />Zapisywanie raportów wyszukiwania danych dla użytkowników tylko do odczytu do wyświetlania. | **[Linia bazowa](#b) <br /> <br /> [Raporty](#r)** |
| **Aparat usługi Defender dla usługi IoT** | Aparaty analizy samodzielnej w usłudze Defender for IoT eliminują konieczność aktualizowania podpisów lub definiowania reguł. Aparaty wykorzystują analizy behawioralne specyficzne dla usługi ICS, aby stale analizować ruch sieciowy pod kątem anomalii, złośliwego oprogramowania, problemów operacyjnych, naruszeń protokołu i odchyleń od aktywności sieci podstawowej.<br /><br />Gdy silnik wykryje odchylenia, zostanie wyzwolony alert. Alerty można wyświetlać i zarządzać nimi z poziomu ekranu **alertów** lub z Siem. | **[Alerty](#a)** |
| **Usługa Defender dla platformy IoT** | Rozwiązanie Defender for IoT zainstalowane w usłudze Defender dla czujników IoT i lokalnej konsoli zarządzania. | **[](#s) <br /> Czujnik <br /> [Lokalna Konsola zarządzania](#o)** |
| **Mapa urządzenia** | Graficzna reprezentacja urządzeń sieciowych wykrywanych przez usługę Defender for IoT. Pokazuje połączenia między urządzeniami a informacjami o poszczególnych urządzeniach. Użyj mapy, aby:<br /><br />-Pobieranie i kontrola krytycznych informacji o urządzeniu.<br /><br />-Analizuj wycinki sieci.<br /><br />-Eksportuj szczegóły i podsumowania urządzenia. | **[Grupa warstw Purdue](#p)** |
| **Spis urządzeń — czujnik** | Spis urządzeń zawiera szeroką gamę atrybutów urządzeń wykrywanych przez usługę Defender for IoT. Dostępne są następujące opcje:<br /><br />-Filtruj wyświetlane informacje.<br /><br />— Wyeksportuj te informacje do pliku CSV.<br /><br />-Zaimportuj szczegóły rejestru systemu Windows. | **[Group (Grupa)](#g)** <br /><br />**[Spis urządzeń — lokalna Konsola zarządzania](#d)** |
| **Spis urządzeń — lokalna Konsola zarządzania** | Informacje o urządzeniu z połączonych czujników można wyświetlić w lokalnej konsoli zarządzania w spisie urządzeń. Zapewnia to użytkownikom lokalnej konsoli zarządzania Kompleksowy wgląd w wszystkie informacje o sieci. | **[Spis urządzeń — czujnik](#d) <br /> <br /> [Spis urządzeń — Integrator danych](#d)** |
| **Spis urządzeń — Integrator danych** | Możliwości integracji danych w lokalnej konsoli zarządzania pozwalają ulepszyć dane w spisie urządzeń przy użyciu informacji z innych zasobów przedsiębiorstwa. Przykładowymi zasobami są CMDB, DNS, zapory i interfejsy API sieci Web. | **[Spis urządzeń — lokalna Konsola zarządzania](#d)** |

## <a name="e"></a>E

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Widok przedsiębiorstwa** | Globalna mapa, która przedstawia jednostki biznesowe, Lokacje i strefy, w których są zainstalowane obrony dla czujników IoT. Wyświetlanie lokalizacji geograficznych złośliwych alertów, alertów operacyjnych i innych elementów. | **[](#b) <br /> Jednostka <br /> biznesowa [](#s) <br /> Witryna <br /> [Strefa](#z)** |
| **Oś czasu zdarzeń** | Oś czasu działania wykryta w sieci, w tym:<br /><br />-Wyzwolone alerty.<br /><br />-Zdarzenia sieciowe (informacje).<br /><br />— Operacje użytkownika, takie jak logowanie, usuwanie użytkowników i tworzenie użytkowników oraz operacje zarządzania alertami, takie jak wyciszenie, nauka i potwierdzenie. Dostępne w konsolach czujników. | - |
| **Reguła wykluczania** | Poinstruuj usługę Defender for IoT, aby ignorować wyzwalacze alertów na podstawie okresu, adresu urządzenia i nazwy alertu lub określonego czujnika.<br /><br />Jeśli na przykład wiadomo, że wszystkie urządzenia, które są monitorowane przez określony czujnik, przechodzą przez procedurę konserwacji od 6:30 do 10:15 rano, można ustawić regułę wykluczania, która stwierdza, że ten czujnik powinien wysyłać bez alertów w wstępnie zdefiniowanym okresie. | **[](#a) <br /> Alert <br /> [Wycisz zdarzenie alertu](#m)** |

## <a name="f"></a>F

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Reguła przekazywania** | Reguły przekazywania instruują usługę Defender for IoT, aby wysyłali informacje o alertach do dostawców lub systemów partnerskich.<br /><br />Na przykład wysyłanie informacji o alercie do serwera Splunk lub serwera dziennika systemowego. | **[Alerty](#a)** |

## <a name="g"></a>G

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Grupa** | Wstępnie zdefiniowana lub niestandardowa grupa urządzeń, które zawierają określone atrybuty, takie jak urządzenia, które przeprowadziły działania programistyczne lub urządzenia znajdujące się w określonej podsieci. Użyj grup, aby ułatwić Wyświetlanie urządzeń i analizowanie urządzeń w usłudze Defender for IoT.<br /><br />Grupy mogą być wyświetlane i tworzone na podstawie mapy urządzeń i spisu urządzeń. | **[Mapa urządzenia](#d) <br /> <br /> [Spis urządzeń](#d)** |

## <a name="h"></a>H

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Otwarte środowisko programistyczne w ramach horyzontu** | Zabezpiecz urządzenia IoT i ICS z własnymi protokołami lub protokołami niestandardowymi, które odbiegają od standardów. Użyj zestawu SDK open Development Environment (Node) w celu opracowania wtyczek do rozwinięcia, które dekodują ruch sieciowy na podstawie zdefiniowanych protokołów. Obronność usług IoT analizuje ruch, aby zapewnić pełne monitorowanie, alerty i raportowanie.<br /><br />Użyj zakresu od do:<br /><br />- **Rozwiń** widoczność i kontrolę bez potrzeby uaktualniania wersji platformy usługi IoT.<br /><br />- **Zabezpieczanie** informacji własnościowych przez Tworzenie lokacji jako wtyczki zewnętrznej.<br /><br />- **Lokalizowanie** tekstu dla alertów, zdarzeń i parametrów protokołu.<br /><br />Aby uzyskać szczegółowe informacje, skontaktuj się z przedstawicielem działu IT. | **[Obsługa protokołu](#p) <br /> <br /> [Lokalizacja](#l)** |
| **Niestandardowy alert dotyczący horyzontu** | Podnieś poziom zarządzania alertami w przedsiębiorstwie, wyzwalając niestandardowe alerty dla dowolnego protokołu (w oparciu o sektory ruchu w strukturze horyzontu).<br /><br />Te alerty mogą służyć do przekazywania informacji:<br /><br />— Informacje o wykrywaniu ruchu w oparciu o protokoły i protokoły bazowe w ramach wtyczki własnościowego horyzontu.<br /><br />— Informacje o kombinacji pól protokołu ze wszystkich warstw protokołu. | **[Obsługa protokołu](#p)** |

## <a name="i"></a>I

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Integracje** | Rozwiń węzeł Defender for IoT, udostępniając informacje o urządzeniach w systemach partnerskich. Organizacje mogą odesłać wcześniej silosy zabezpieczeń, NAC, zarządzanie zdarzeniami i rozwiązania do zarządzania urządzeniami, aby przyspieszyć reagowanie na całe systemy i szybciej ograniczyć ryzyko. | **[Reguła przekazywania](#f)** |
| **Podsieć wewnętrzna** | Konfiguracje podsieci zdefiniowane przez usługę Defender for IoT. W niektórych przypadkach, takich jak środowiska korzystające z zakresów publicznych jako zakresy wewnętrzne, można wydać usługę Defender for IoT, aby rozwiązać wszystkie podsieci jako podsieci wewnętrzne. Podsieci są wyświetlane na mapie i w różnych raportach usługi Defender for IoT. | **[Podsieci](#s)** |

## <a name="l"></a>L

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Informacje o zdarzeniu alertu** | Poinstruuj usługę Defender for IoT, aby autoryzować ruch wykryty w zdarzeniu alertu. | **[](#a) <br /> Alert <br /> [Potwierdzenie zdarzenia](#a) <br /> <br /> alertu [Wycisz zdarzenie alertu](#m)** |
| **Tryb uczenia** | Tryb używany podczas nauki działania sieci przez usługę Defender for IoT. To działanie jest podstawą sieci. Usługa Defender dla IoT pozostaje w trybie dla wstępnie zdefiniowanego okresu po instalacji. Działanie, które odbiega od działań uzyskanych po tym okresie, spowoduje wyzwolenie alertów usługi Defender dla usługi IoT. | **[Inteligentne uczenie IT](#s) <br /> <br /> [Linia bazowa](#b)** |
| **Lokalizacja** | Lokalizowanie tekstu dotyczącego alertów, zdarzeń i parametrów protokołu dla wtyczek rozsektorowych opracowanych przez horyzonty. | **[Otwarte środowisko programistyczne w ramach horyzontu](#h)** |

## <a name="m"></a>M

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Wycisz zdarzenie alertu** | Nakazuje usłudze Defender for IoT ciągłe ignorowanie aktywności z identycznymi urządzeniami i porównywalnym ruchem. | **[](#a) <br /> Alert <br /> [](#e) <br /> Reguła <br /> wykluczania [Potwierdzenie zdarzenia](#a) <br /> <br /> alertu [Informacje o zdarzeniu alertu](#l)** |

## <a name="n"></a>N

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Powiadomienia** | Informacje o zmianach w sieci lub nierozpoznanych właściwościach urządzenia. Dostępne są opcje aktualizowania informacji o urządzeniu i sieci z wykrytymi nowymi danymi. Reagowanie na powiadomienia wzbogaca spis urządzeń, mapę i różne raporty. Dostępne w konsolach czujników. | **[](#a) <br /> Alert <br /> [Powiadomienia systemowe](#s)** |

## <a name="o"></a>O

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Lokalna Konsola zarządzania** | Lokalna Konsola zarządzania zapewnia scentralizowany widok i zarządzanie urządzeniami i zagrożeniami, które są wykrywane przez obrony przed wdrożeniami czujnika IoT w organizacji. | **Usługa [Defender dla platformy IoT](#d) <br /> <br /> [Czujnik](#s)** |
| **Alert operacyjny** | Alerty, które zajmują problemy z siecią operacyjną, takie jak urządzenie podejrzane o odłączenie od sieci. | **[](#a) <br /> Alert <br /> [Alert zabezpieczeń](#s)** |

## <a name="p"></a>P

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Warstwa Purdue** | Pokazuje połączenia i wzajemnych zależności głównych składników typowego połączenia internetowego na mapie. |  |
| **Obsługa protokołu** | Oprócz obsługi protokołu osadzonego można zabezpieczyć urządzenia IoT i ICS z własnymi i niestandardowymi protokołami lub protokoły odbiegające od dowolnego standardu, korzystając z zestawu SDK open Development Environment. | **[Otwarte środowisko programistyczne w ramach horyzontu](#h)** |

## <a name="r"></a>R

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Region** | Logiczna podział organizacji globalnej na regiony geograficzne. Przykłady to Ameryka Północna, Europa Zachodnia i Europa Wschodnia.<br /><br />Ameryka Północna mogą mieć fabryki z różnych jednostek biznesowych. | **[Grupa dostępu](#a) <br /> <br /> [](#b) <br /> Jednostka <br /> biznesowa [Lokalna Konsola zarządzania](#o) <br /> <br /> [](#s) <br /> Witryna <br /> [Strefa](#z)** |
| **Raporty** | Raporty odzwierciedlają informacje generowane przez wyniki zapytania wyszukiwania danych. Obejmuje to domyślne wyniki wyszukiwania danych, które są dostępne w widoku **raporty** . Administratorzy i analitycy zabezpieczeń mogą również generować niestandardowe zapytania wyszukiwania danych i zapisywać je jako raporty. Te raporty będą również dostępne dla użytkowników tylko do odczytu. | **[Wyszukiwanie danych](#d)** |
| **Raport z oceny ryzyka** | Raportowanie oceny ryzyka umożliwia wygenerowanie oceny zabezpieczeń dla każdego urządzenia sieciowego wraz z ogólnym wynikiem zabezpieczeń sieci. Wynik ogólny przedstawia wartość procentową zabezpieczeń 100%. Raport zawiera zalecenia zaradcze, które pomogą ulepszyć bieżący wynik. | - |

## <a name="s"></a>S

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Alert zabezpieczeń** | Alerty, które dotyczą problemów z zabezpieczeniami, takich jak nadmierne próby logowania przy użyciu protokołu SMB lub wykrywanie złośliwego oprogramowania. | **[](#a) <br /> Alert <br /> [Alert operacyjny](#o)** |
| **Sondowanie selektywne** | Usługa Defender dla usługi IoT pasywnie przeprowadza inspekcję i ruch wychodzący i wykrywa odpowiednie informacje na temat urządzeń, ich atrybutów, zachowań i nie tylko. W niektórych przypadkach niektóre informacje mogą nie być widoczne w przypadku pasywnych analiz sieci.<br /><br />W takim przypadku można użyć bezpiecznych, szczegółowych narzędzi do sondowania w usłudze Defender for IoT, aby poznać ważne informacje dotyczące wcześniej nieosiągalnych urządzeń. | - |
| **Czujnik** | Maszyna fizyczna lub wirtualna, na której zainstalowano platformę Defender for IoT. | **[Lokalna Konsola zarządzania](#o)** |
| **Witryna** | Lokalizacja fabryki lub innej jednostki. Lokacja powinna zawierać strefę lub kilka stref, w których jest zainstalowany czujnik. | **[Strefa](#z)** |
| **Zarządzanie lokacją** | Opcja lokalnej konsoli zarządzania, która umożliwia zarządzanie czujnikami przedsiębiorstwa. | - |
| **Inteligentne uczenie IT** | Po zakończeniu okresu nauki i przełączeniu trybu uczenia usługa Defender dla IoT może wykryć nienormalnie wysoki poziom zmian linii bazowej, które są wynikiem normalnych działań IT, takich jak żądania DNS i HTTP. Ten ruch może spowodować niepotrzebne alerty naruszenia zasad i powiadomienia systemowe. Aby zmniejszyć te alerty i powiadomienia, możesz włączyć inteligentną naukę IT. | **[](#l) <br /> Tryb <br /> uczenia [Linia bazowa](#b)** |
| **Podsieci** | Aby włączyć fokus na urządzeniach, urządzenia IT są automatycznie agregowane przez podsieć na mapie urządzeń. Każda podsieć jest prezentowana jako jedna jednostka na mapie, łącznie z interaktywną zwijaniem lub rozwijaną funkcją, aby skoncentrować się na podsieci IT i z powrotem. | **[Mapa urządzenia](#d)** |
| **Powiadomienia systemowe** | Powiadomienia z przeklasyfikowania lokalnej konsoli zarządzania:<br /><br />— Stan połączenia czujnika.<br /><br />-Błędy zdalnej kopii zapasowej. | **[](#n) <br /> Powiadomienia <br /> [Alert](#a)** |

## <a name="z"></a>Z

| Okres | Opis | Więcej tutaj |
|--|--|--|
| **Strefa** | Obszar w obrębie lokacji, w której jest zainstalowany czujnik lub czujniki. | **[](#s) <br /> Witryna <br /> [](#b) <br /> Jednostka <br /> biznesowa [Region](#r)** |
