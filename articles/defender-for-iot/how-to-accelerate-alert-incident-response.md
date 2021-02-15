---
title: Przyspiesz przepływy pracy alertów
description: Ulepsz przepływy pracy alertów i zdarzeń.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 0a445abe0f448c28742282aedd1a886fae0f5a43
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526293"
---
# <a name="accelerate-alert-workflows"></a>Przyspiesz przepływy pracy alertów

W tym artykule opisano sposób przyspieszenia przepływów pracy alertów przy użyciu komentarzy alertów, grup alertów i niestandardowych reguł alertów w usłudze Azure Defender dla IoT.  Narzędzia te pomagają:

- Analizuj dużą liczbę zdarzeń alertów wykrytych w sieci i zarządzaj nimi.

- Zlokalizuj i obsługuj określoną aktywność sieciową.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Przyspiesz przepływy pracy zdarzeń przy użyciu komentarzy alertów

Pracuj z komentarzami alertów w celu poprawy komunikacji między osobami i zespołami podczas badania zdarzenia alertu.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Zrzut ekranu przedstawiający złośliwe działanie.":::

Użyj komentarzy alertów, aby zwiększyć:

- **Etapy przepływu pracy**: zapewnianie czynności zaradczych alertów.

- **Monit dotyczący przepływu pracy**: powiadamianie o wykonaniu kroków.

- **Wskazówki dotyczące przepływu pracy**: zapewnianie zaleceń, szczegółowych informacji lub ostrzeżeń dotyczących zdarzenia.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Zrzut ekranu przedstawiający komentarze dotyczące alertów.":::

Lista dostępnych opcji pojawia się w każdym alercie. Użytkownicy mogą wybrać jeden lub kilka komunikatów.

Aby dodać komentarze alertu:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W oknie **Ustawienia systemu** wybierz pozycję **komentarze dotyczące alertów**.

3. W polu **Dodaj komentarze** wprowadź tekst komentarza. Użyj maksymalnie 50 znaków. Przecinki są niedozwolone.

4. Wybierz pozycję **Dodaj**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Przyspiesz przepływy pracy zdarzeń przy użyciu grup alertów

Grupy alertów umożliwiają zespołom SOC wyświetlanie i filtrowanie alertów w swoich rozwiązaniach SIEM, a następnie zarządzanie tymi alertami na podstawie zasad zabezpieczeń przedsiębiorstwa i priorytetów firmy. Na przykład alerty dotyczące nowych wykryć są zorganizowane w grupie odnajdywania. Ta grupa zawiera alerty dotyczące wykrywania nowych urządzeń, nowych sieci VLAN, nowych kont użytkowników, nowych adresów MAC i innych.

Grupy alertów są stosowane podczas tworzenia reguł przekazywania dla następujących rozwiązań partnerskich:

  - Serwery dziennika systemowego

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Zrzut ekranu przedstawiający tworzenie reguły przekazywania.":::

Odpowiednia grupa alertów pojawia się w rozwiązaniach danych wyjściowych partnera. 

### <a name="requirements"></a>Wymagania

W obszarze obsługiwane rozwiązania partnerskie zostanie wyświetlona Grupa alertów z następującymi prefiksami:

- **kot** for QRadar, ArcSight, dziennik systemowy CEF, dziennik SYSTEMowy LEEF

- **Grupa alertów** dla komunikatów tekstowych dziennika systemowego

- **alert_group** dla obiektów dziennika systemowego

Te pola należy skonfigurować w rozwiązaniu partnerskim, aby wyświetlić nazwę grupy alertów. Jeśli nie ma alertu skojarzonego z grupą alertów, w polu w rozwiązaniu partnerskim będzie wyświetlana wartość **na**.

### <a name="default-alert-groups"></a>Domyślne grupy alertów

Automatycznie definiowane są następujące grupy alertów:
|  |  |  |
|--|--|--|
| Nietypowe zachowanie komunikacji | Alerty niestandardowe | Dostęp zdalny |
| Nietypowe zachowanie komunikacji z protokołem HTTP | Odnajdywanie | Polecenia Uruchom ponownie i Zatrzymaj |
| Authentication | Zmiana oprogramowania układowego | Skanowanie |
| Zachowanie komunikacji nieautoryzowanej | Niedozwolone polecenia | Ruch z czujnika |
| Anomalie przepustowości | Dostęp do Internetu | Podejrzenie złośliwego oprogramowania |
| Przepełnienie buforu | Błędy operacji | Podejrzenie złośliwego działania |
| Błędy poleceń | Problemy operacyjne |  |
| Zmiany konfiguracji | Użytkow |  |

Grupy alertów są wstępnie zdefiniowane. Aby uzyskać szczegółowe informacje o alertach skojarzonych z grupami alertów oraz o tworzeniu niestandardowych grup alertów, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Dostosuj reguły alertów

Użyj niestandardowych reguł alertów, aby dokładniej ustalić działania interesujące dla użytkownika. 

Możesz dodać niestandardowe reguły alertów w oparciu o:

- Kategoria, na przykład protokół, port lub plik.
- Adresy źródłowe i docelowe
- Warunek oparty na wybranej kategorii, na przykład funkcja skojarzona z protokołem, nazwą pliku, portem lub numerem transportu.
- Warunek oparty na odwołaniu daty i godziny, na przykład w przypadku wykrycia określonego dnia lub określonej części dnia.

Jeśli czujnik wykrywa działanie opisane w regule, jest wysyłany alert.
informacje wykrywane przez poszczególne czujniki. Na przykład Zdefiniuj regułę, która powoduje, że czujnik wyzwala alert w oparciu o źródłowy adres IP, docelowy adres IP lub polecenie (w ramach protokołu). Gdy czujnik wykryje ruch zdefiniowany w regule, generowany jest alert lub zdarzenie.

Możesz również użyć akcji reguły alertu, aby wyszkolić usługę Defender for IoT:

- Zezwól użytkownikom na dostęp do pliku PCAP z poziomu alertu.
- Przypisywanie ważności alertu.
- Generuj zdarzenie zamiast alertu. Wykryte informacje pojawią się na osi czasu zdarzeń.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Zrzut ekranu, który pokazuje regułę zdefiniowaną przez użytkownika.":::

Komunikat alertu wskazuje, że alert jest wyzwalany przez regułę zdefiniowaną przez użytkownika.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Zrzut ekranu pokazujący niestandardowe alerty.":::

Aby utworzyć niestandardową regułę alertu:

1. Wybierz pozycję **alerty niestandardowe** z menu po stronie czujnika.
1. Wybierz znak plus ( **+** ), aby utworzyć regułę.
1. Zdefiniuj nazwę reguły.
1. Wybierz kategorię lub protokół z okienka **Kategorie** .
1. Zdefiniuj konkretny źródłowy i docelowy adres IP lub MAC lub wybierz dowolny adres.
1. Zdefiniuj jeden lub kilka warunków reguły. Można utworzyć dwie kategorie warunków:
    - Warunki na podstawie unikatowych wartości skojarzonych z wybraną kategorią. Wybierz pozycję Dodaj i Zdefiniuj wartości.
    - Warunki w zależności od tego, kiedy działanie zostało wykryte. W sekcji wykrycia Wybierz okres i dzień, w którym należy przeprowadzić wykrywanie w celu wysłania alertu. Można wysłać Alert, jeśli działanie zostanie wykryte w dowolnym czasie, w czasie lub po godzinach pracy. Użyj opcji Definiuj godziny pracy, aby wyszkolić usługę Defender dla godzin pracy IoT w organizacji.
1. Zdefiniuj akcje reguły: 
    - Wskaż, czy reguła wyzwala **alarm** czy **zdarzenie**.
    - Przypisz do alertu poziom ważności.
    - Wskaż, czy alert będzie zawierać plik PCAP.
1. Wybierz pozycję **Zapisz**.

Reguła jest dodawana do listy **niestandardowych reguł alertów** , w której można przeglądać podstawowe parametry reguł, przy ostatnim wywołaniu reguły i nie tylko. Możesz również włączyć i wyłączyć regułę z listy.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Zrzut ekranu przedstawiający dodaną regułę dostosowaną przez użytkownika.":::

### <a name="see-also"></a>Zobacz też

[Wyświetlanie informacji podanych w alertach](how-to-view-information-provided-in-alerts.md)

[Zarządzanie zdarzeniem alertu](how-to-manage-the-alert-event.md)
