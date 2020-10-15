---
title: Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center
description: Ten dokument ułatwia korzystanie z adaptacyjnej kontroli aplikacji w Azure Security Center, aby umożliwić wyświetlanie aplikacji na komputerach z systemem Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2020
ms.author: memildin
ms.openlocfilehash: c580dd26c64a27b88b4416e85da101b78782013e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076984"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Korzystanie z adaptacyjnych kontrolek aplikacji w celu ograniczenia podatności na ataki maszyn

Dowiedz się więcej o zaletach adaptacyjnych kontrolek aplikacji Azure Security Center i sposobach zwiększania bezpieczeństwa dzięki tej funkcji inteligentnej opartej na danych.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Co to są Security Center adaptacyjne kontrolki aplikacji?

Adaptacyjne kontrolki aplikacji to inteligentne i zautomatyzowane rozwiązanie umożliwiające Definiowanie listy dozwolonych aplikacji z znanymi bezpiecznymi aplikacjami dla maszyn. 

Często organizacje mają kolekcje maszyn, które rutynowie uruchamiają te same procesy. Security Center korzysta z uczenia maszynowego, aby analizować aplikacje działające na maszynach i utworzyć listę znanego bezpiecznego oprogramowania. Listy dozwolonych są oparte na określonych obciążeniach platformy Azure i można bardziej dostosować zalecenia, korzystając z poniższych instrukcji.

Po włączeniu i skonfigurowaniu adaptacyjnych kontrolek aplikacji otrzymasz alerty zabezpieczeń w przypadku uruchamiania aplikacji innych niż te, które zostały zdefiniowane jako bezpieczne.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Jakie są zalety adaptacyjnych kontroli aplikacji?

Przez Definiowanie list znanych bezpiecznych aplikacji oraz generowanie alertów w przypadku wykonywania jakichkolwiek innych czynności można osiągnąć wiele celów związanych z ograniczaniem funkcjonalności:

- Zidentyfikuj potencjalne złośliwe oprogramowanie, nawet te, które mogły zostać pominięte przez rozwiązania chroniące przed złośliwym kodem
- Poprawa zgodności z lokalnymi zasadami zabezpieczeń, które wymuszają korzystanie wyłącznie z licencjonowanego oprogramowania
- Unikanie uruchamiania starych lub nieobsługiwanych aplikacji
- Zapobiegaj określonemu oprogramowaniu, które jest zabronione przez organizację
- Zwiększenie nadzoru nad aplikacjami, które uzyskują dostęp do poufnych danych



## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Obsługiwane maszyny:|![Tak na ](./media/icons/yes-icon.png) platformie Azure i na maszynach spoza platformy Azure z systemem Windows i Linux<br>![Tak — ](./media/icons/yes-icon.png) maszyny [usługi Azure Arc](https://docs.microsoft.com/azure/azure-arc/)|
|Wymagane role i uprawnienia:|Role czytnika i **czytnika** **zabezpieczeń** mogą wyświetlać grupy i listy znanych bezpiecznych aplikacji<br>Role administratora **współautora** i **zabezpieczeń** mogą edytować grupy i listy znanych bezpiecznych aplikacji|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Włączanie formantów aplikacji w grupie komputerów

Jeśli Security Center zidentyfikował grupy maszyn w Twoich subskrypcjach, które regularnie uruchamiają podobny zestaw aplikacji, zostanie wyświetlony monit z następującym zaleceniem: **adaptacyjne mechanizmy kontroli aplikacji do definiowania bezpiecznych aplikacji powinny być włączone na maszynach**.

Wybierz zalecenie lub Otwórz stronę adaptacyjne kontrolki aplikacji, aby wyświetlić listę sugerowanych, znanych, bezpiecznych aplikacji i grup maszyn.

1. Otwórz pulpit nawigacyjny usługi Azure Defender i w obszarze Ochrona zaawansowana wybierz pozycję **adaptacyjne kontrolki aplikacji**.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Otwieranie adaptacyjnych kontrolek aplikacji z poziomu pulpitu nawigacyjnego platformy Azure" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Zostanie otwarta strona **adaptacyjne kontrolki aplikacji** z maszynami wirtualnymi pogrupowanymi na następujące karty:

    - **Skonfigurowane** grupy maszyn, które mają już zdefiniowaną listę dozwolonych aplikacji. Dla każdej grupy na skonfigurowanej karcie są wyświetlane:
        - Liczba maszyn w grupie
        - Ostatnie alerty

    - **Zalecane** — grupy maszyn, które regularnie uruchamiają te same aplikacje i nie mają skonfigurowanej listy dozwolonych. Zalecamy włączenie adaptacyjnych kontrolek aplikacji dla tych grup.
    
      > [!TIP]
      > Jeśli zostanie wyświetlona nazwa grupy z prefiksem "Reviewing", zawiera ona komputery z częściowo spójną listą aplikacji. Security Center nie widzi wzorca, ale zaleca przejrzenie tej grupy, aby sprawdzić _,_ czy można ręcznie zdefiniować niektóre reguły adaptacyjnych kontroli aplikacji zgodnie z opisem w sekcji [Edytowanie reguły adaptacyjnych kontroli aplikacji w grupie](#edit-a-groups-adaptive-application-controls-rule).
      >
      > Możesz również przenieść maszyny z tej grupy do innych grup, zgodnie z opisem w [Przenieś maszynę z jednej grupy do innej](#move-a-machine-from-one-group-to-another).

    - **Brak zaleceń** -maszyn bez zdefiniowanej listy dozwolonych aplikacji, które nie obsługują tej funkcji. Twoja maszyna może znajdować się na tej karcie z następujących powodów:
      - Brak agenta Log Analytics
      - Agent Log Analytics nie wysyła zdarzeń
      - Jest to maszyna z systemem Windows z wcześniej istniejącymi zasadami [funkcji AppLocker](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) , które są włączone przez obiekt zasad grupy lub zasady zabezpieczeń lokalnych.

      > [!TIP]
      > Security Center potrzebuje co najmniej dwóch tygodni danych do zdefiniowania unikatowych zaleceń dla każdej grupy maszyn. Ostatnio utworzone maszyny lub należące do subskrypcji, które były niedawno włączone w usłudze Azure Defender, będą wyświetlane na karcie **bez rekomendacji** .


1. Otwórz kartę **zalecane** . Zostanie wyświetlona Grupa komputerów z zalecanymi listami dozwolonych.

   ![Karta zalecana](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Wybierz grupę. 

1. Aby skonfigurować nową regułę, Przejrzyj różne sekcje tej strony **Konfigurowanie reguł kontroli aplikacji** oraz zawartość, która będzie unikatowa dla tej konkretnej grupy maszyn:

   ![Skonfiguruj nową regułę](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Wybierz pozycję Maszyny** — domyślnie zaznaczone są wszystkie komputery w określonej grupie. Usuń zaznaczenie dowolnego, aby usunąć je z tej reguły.
   
   1. **Zalecane aplikacje** — Przejrzyj tę listę aplikacji, które są wspólne dla maszyn w ramach tej grupy, i zaleca się ich uruchomienie.
   
   1. **Więcej aplikacji** — Przejrzyj tę listę aplikacji, które są rzadziej widoczne na maszynach w ramach tej grupy lub są znane jako możliwe do wykorzystania. Ikona ostrzeżenia wskazuje, że dana aplikacja może być używana przez osobę atakującą do obejścia listy dozwolonych aplikacji. Zalecamy dokładne zapoznanie się z tymi aplikacjami.

      > [!TIP]
      > Obie listy aplikacji obejmują opcję ograniczenia określonej aplikacji do określonych użytkowników. Jeśli to możliwe, należy zastosować zasadę najniższych uprawnień.
      > 
      > Aplikacje są definiowane przez wydawców, jeśli aplikacja nie ma informacji o wydawcy (bez znaku), tworzona jest reguła ścieżki dla pełnej ścieżki konkretnej aplikacji.

   1. Aby zastosować regułę, wybierz pozycję **Inspekcja**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Edytuj regułę adaptacyjnych kontrolek aplikacji grupy

Możesz zdecydować się na edycję listy dozwolonych dla grupy komputerów ze względu na znane zmiany w organizacji. 

Aby edytować reguły dla grupy komputerów:

1. Otwórz pulpit nawigacyjny usługi Azure Defender i w obszarze Ochrona zaawansowana wybierz pozycję **adaptacyjne kontrolki aplikacji**.

1. Na **skonfigurowanej** karcie Wybierz grupę z regułą, którą chcesz edytować.

1. Zapoznaj się z różnymi sekcjami strony **Konfigurowanie reguł kontroli aplikacji** zgodnie z opisem w temacie [Włączanie adaptacyjnych kontroli aplikacji w grupie komputerów](#enable-application-controls-on-a-group-of-machines).

1. Opcjonalnie dodaj co najmniej jedną regułę niestandardową:

   1. Wybierz pozycję **Dodaj regułę**.

      ![Dodawanie reguły niestandardowej](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Jeśli definiujesz znaną bezpieczną ścieżkę, Zmień **Typ reguły** na "Path". W ścieżce można uwzględnić symbole wieloznaczne.
   
      > [!TIP]
      > Niektóre scenariusze, w których mogą być przydatne symbole wieloznaczne w ścieżce:
      > 
      > * Użyj symbolu wieloznacznego na końcu ścieżki, aby zezwolić na wszystkie pliki wykonywalne w tym folderze i podfolderach.
      > * Użycie symbolu wieloznacznego w środku ścieżki w celu włączenia znanej nazwy pliku wykonywalnego ze zmianą nazwy folderu (na przykład folderów osobistych użytkownika zawierających znane pliki wykonywalne, automatycznie wygenerowane nazwy folderów itp.).
  
   1. Zdefiniuj dozwolonych użytkowników i chronione typy plików.

   1. Po zakończeniu definiowania reguły wybierz pozycję **Dodaj**.

1. Aby zastosować zmiany, wybierz pozycję **Zapisz**.


## <a name="review-and-edit-a-groups-settings"></a>Przeglądanie i edytowanie ustawień grupy

1. Aby wyświetlić szczegóły i ustawienia grupy, wybierz pozycję **Ustawienia grupy**

    To okienko zawiera nazwę grupy (którą można modyfikować), typ systemu operacyjnego, lokalizację i inne istotne szczegóły.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Otwieranie adaptacyjnych kontrolek aplikacji z poziomu pulpitu nawigacyjnego platformy Azure" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

    > [!IMPORTANT]
    > Opcja **Wymuś** w ustawieniach trybu ochrony typu pliku jest wyszarzona we **wszystkich** scenariuszach. W tej chwili nie są dostępne żadne opcje wymuszania. 
    >
    > :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-modes.png" alt-text="Otwieranie adaptacyjnych kontrolek aplikacji z poziomu pulpitu nawigacyjnego platformy Azure":::

1. Opcjonalnie możesz zmodyfikować nazwy grupy lub tryby ochrony typów plików.

1. Wybierz pozycję **Zastosuj** i **Zapisz**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Należy zaktualizować zalecenia dotyczące "reguł dozwolonych w ramach adaptacyjnych zasad kontroli aplikacji".

To zalecenie zostanie wyświetlone, gdy Security Center Uczenie maszynowe będzie identyfikować potencjalnie wiarygodne zachowanie, które nie było wcześniej dozwolone. Zalecenie sugeruje nowe reguły dla istniejących definicji w celu zmniejszenia liczby fałszywych alertów pozytywnych.

Aby skorygować problemy:

1. Na stronie zalecenia wybierz **reguły dozwolonychymi w ramach adaptacyjnych zasad kontroli aplikacji należy zaktualizować** zalecenia, aby zobaczyć grupy z nowo określonymi, potencjalnie uprawnionym zachowaniem.

1. Wybierz grupę z regułą, którą chcesz edytować.

1. Zapoznaj się z różnymi sekcjami strony **Konfigurowanie reguł kontroli aplikacji** zgodnie z opisem w temacie [Włączanie adaptacyjnych kontroli aplikacji w grupie komputerów](#enable-application-controls-on-a-group-of-machines).

1. Aby zastosować zmiany, wybierz pozycję **Inspekcja**.




## <a name="audit-alerts-and-violations"></a>Inspekcja alertów i naruszeń

1. Otwórz pulpit nawigacyjny usługi Azure Defender i w obszarze Ochrona zaawansowana wybierz pozycję **adaptacyjne kontrolki aplikacji**.

1. Aby wyświetlić grupy z maszynami, które mają Ostatnie alerty, przejrzyj grupy wymienione na karcie **skonfigurowane** .

1. Aby dokładniej zbadać, wybierz grupę.

   ![Ostatnie alerty](./media/security-center-adaptive-application/recent-alerts.png)

1. Aby uzyskać więcej szczegółów, a także listę maszyn, których to dotyczy, wybierz alert.



## <a name="move-a-machine-from-one-group-to-another"></a>Przenoszenie maszyny z jednej grupy do innej

Po przeniesieniu maszyny z jednej grupy do innej zasady kontroli aplikacji zastosowane do niej zmieniają ustawienia grupy, do której została przeniesiona. Możesz również przenieść maszynę ze skonfigurowanej grupy do grupy, która nie jest skonfigurowana. spowoduje to usunięcie wszystkich reguł kontroli aplikacji, które zostały zastosowane do maszyny.

1. Otwórz pulpit nawigacyjny usługi Azure Defender i w obszarze Ochrona zaawansowana wybierz pozycję **adaptacyjne kontrolki aplikacji**.

1. Na stronie **adaptacyjne kontrolki aplikacji** na **skonfigurowanej** karcie Wybierz grupę zawierającą maszynę, która ma zostać przeniesiona.

1. Otwórz listę  **skonfigurowanych maszyn**.

1. Otwórz menu maszyny z trzech kropek na końcu wiersza, a następnie wybierz pozycję **Przenieś**. Zostanie otwarte okienko **Przenieś maszynę do innej grupy** .

1. Wybierz grupę docelową, a następnie wybierz pozycję **Przenieś maszynę**.

1. Aby zapisać zmiany, wybierz pozycję **Zapisz**.





## <a name="manage-application-controls-via-the-rest-api"></a>Zarządzanie kontrolkami aplikacji za pośrednictwem interfejsu API REST 

Aby zarządzać adaptacyjnymi kontrolkami aplikacji programowo, użyj naszego interfejsu API REST. 

Pełna dokumentacja interfejsu API jest dostępna [tutaj](https://docs.microsoft.com/rest/api/securitycenter/adaptiveapplicationcontrols).

Niektóre funkcje, które są dostępne w interfejsie API REST:

* **Lista** pobiera wszystkie zalecenia dotyczące grupy i zawiera kod JSON z obiektem dla każdej grupy.

* **Pobierz** plik JSON z pełnymi danymi rekomendacji (czyli listą maszyn, regułami wydawcy/ścieżki itd.).

* **Put** konfiguruje regułę (Użyj pliku JSON pobranego z opcją **Get** jako treść dla tego żądania).
 
   > [!IMPORTANT]
   > Funkcja **Put** oczekuje mniej parametrów niż kod JSON zwrócony przez polecenie Get zawiera.
   >
   > Przed użyciem kodu JSON w żądaniu Put należy usunąć następujące właściwości: recommendationStatus, configurationStatus, Issues, Location i sourceSystem.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób korzystania z adaptacyjnej kontroli aplikacji w Azure Security Center, aby zdefiniować listę dozwolonych aplikacji uruchomionych na maszynach na platformie Azure i poza platformą Azure. Aby dowiedzieć się więcej na temat innych funkcji ochrony obciążeń w chmurze Security Center, zobacz:

* [Zrozumienie dostępu do maszyny wirtualnej just-in-Time (JIT)](just-in-time-explained.md)
* [Zabezpieczanie klastrów usługi Azure Kubernetes](defender-for-kubernetes-introduction.md)