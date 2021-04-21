---
title: Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center
description: Ten dokument ułatwia korzystanie z funkcji adaptacyjnego sterowania aplikacjami w Azure Security Center do zezwalania na listę aplikacji uruchomionych na maszynach platformy Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 9e2dcace673a1c7215634434f9e89ddc6b953a63
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834627"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Używanie funkcji adaptacyjnego sterowania aplikacją w celu zmniejszenia powierzchni ataków na maszyny

Dowiedz się więcej o zaletach funkcji adaptacyjnego sterowania Azure Security Center firmy Azure Security Center oraz o tym, jak można zwiększyć bezpieczeństwo dzięki tej inteligentnej funkcji opartej na danych.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Co to Security Center adaptacyjnego sterowania aplikacją?

Funkcje adaptacyjnego sterowania aplikacjami to inteligentne i zautomatyzowane rozwiązanie do definiowania list zezwalania znanych bezpiecznych aplikacji dla maszyn. 

Często organizacje mają kolekcje maszyn, które rutynowo uruchamiają te same procesy. Security Center używa uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzenia listy znanego bezpiecznego oprogramowania. Listy zezwalania są oparte na konkretnych obciążeniach platformy Azure i możesz dodatkowo dostosować zalecenia, korzystając z poniższych instrukcji.

Po włączeniu i skonfigurowaniu funkcji adaptacyjnego sterowania aplikacją otrzymasz alerty zabezpieczeń, jeśli dowolna aplikacja działa poza zdefiniowanymi jako bezpieczne.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Jakie są zalety adaptacyjnego sterowania aplikacją?

Definiując listy znanych bezpiecznych aplikacji i generując alerty, gdy są wykonywane inne, można osiągnąć wiele celów wzmacniania zabezpieczeń:

- Identyfikowanie potencjalnego złośliwego oprogramowania, nawet tych, które mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem
- Zwiększenie zgodności z lokalnymi zasadami zabezpieczeń, które określają użycie tylko licencjonowanego oprogramowania
- Unikaj uruchamiania starych lub nieobsługiwanych aplikacji
- Zapobieganie określonemu oprogramowaniu zablokowanemu przez organizację
- Zwiększenie nadzoru nad aplikacjami, które mają dostęp do poufnych danych

Obecnie nie są dostępne żadne opcje wymuszania. Funkcje adaptacyjnego sterowania aplikacją mają na celu zapewnienie alertów zabezpieczeń, jeśli są uruchamiane aplikacje inne niż zdefiniowane jako bezpieczne.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność|
|Ceny:|Wymaga [Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Obsługiwane maszyny:|![Tak ](./media/icons/yes-icon.png) maszyny platformy Azure i maszyny spoza platformy Azure z systemami Windows i Linux<br>![Tak ](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml) maszyn wirtualnych|
|Wymagane role i uprawnienia:|**Role Czytelnik zabezpieczeń** **i Czytelnik** mogą wyświetlać grupy i listy znanych bezpiecznych aplikacji<br>**Role Współautor** **i Administrator zabezpieczeń** mogą edytować grupy i listy znanych bezpiecznych aplikacji|
|Chmury:|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/Sovereign (US Gov, China Gov, Other Gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Włączanie kontrolek aplikacji na grupie maszyn

Jeśli program Security Center zidentyfikował grupy maszyn w subskrypcjach, które stale uruchamiają podobny zestaw aplikacji, zostanie wyświetlony monit z następującym zaleceniem: Na maszynach powinny być włączone funkcje adaptacyjnego sterowania aplikacjami do definiowania bezpiecznych **aplikacji.**

Wybierz zalecenie lub otwórz stronę adaptacyjnego sterowania aplikacjami, aby wyświetlić listę sugerowanych znanych bezpiecznych aplikacji i grup maszyn.

1. Otwórz pulpit Azure Defender nawigacyjny i w obszarze zaawansowanej ochrony wybierz pozycję **Adaptacyjne kontrolki aplikacji.**

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Otwieranie adaptacyjnych kontrolek aplikacji z pulpitu nawigacyjnego platformy Azure" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Zostanie **otwarta strona Funkcje adaptacyjnego sterowania** aplikacją z maszynami wirtualnych pogrupowanych na następujących kartach:

    - **Skonfigurowane —** grupy maszyn, które mają już zdefiniowaną listę zezwalań aplikacji. Dla każdej grupy na skonfigurowanej karcie przedstawiono:
        - liczba maszyn w grupie
        - ostatnie alerty

    - **Zalecane** — grupy maszyn, które stale uruchamiają te same aplikacje i nie mają skonfigurowanej listy zezwalań. Zalecamy włączenie funkcji adaptacyjnego sterowania aplikacją dla tych grup.
    
      > [!TIP]
      > Jeśli zostanie wyświetlona nazwa grupy z prefiksem "REVIEWGROUP", zawiera ona maszyny z częściowo spójną listą aplikacji. Security Center wzorca, ale zaleca przejrzenie tej grupy w celu  sprawdzenia, czy można ręcznie zdefiniować niektóre reguły adaptacyjnego sterowania aplikacją zgodnie z opisem w tece Edytowanie reguły adaptacyjnego sterowania aplikacją [grupy.](#edit-a-groups-adaptive-application-controls-rule)
      >
      > Możesz również przenieść maszyny z tej grupy do innych grup zgodnie z opisem w temacie Przenoszenie maszyny [z jednej grupy do innej.](#move-a-machine-from-one-group-to-another)

    - **Brak zaleceń** — maszyny bez zdefiniowanej listy zezwalań aplikacji, które nie obsługują tej funkcji. Maszyna może być na tej karcie z następujących powodów:
      - Brak agenta usługi Log Analytics
      - Agent usługi Log Analytics nie wysyła zdarzeń
      - Jest to maszyna z systemem Windows z już istniejącymi zasadami [funkcji AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) włączonymi za pomocą zasad grupy lub lokalnych zasad zabezpieczeń

      > [!TIP]
      > Security Center musi mieć co najmniej dwa tygodnie danych do zdefiniowania unikatowych zaleceń dla grupy maszyn. Maszyny, które zostały ostatnio utworzone lub należą do subskrypcji, które zostały niedawno włączone przy użyciu Azure Defender, będą wyświetlane na **karcie Brak** rekomendacji.


1. Otwórz **kartę Zalecane.** Zostanie wyświetlona lista grup maszyn z zalecanymi listami zezwalania.

   ![Zalecana karta](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Wybierz grupę. 

1. Aby skonfigurować nową regułę, przejrzyj  różne sekcje tej strony Konfigurowanie reguł sterowania aplikacją i zawartość, która będzie unikatowa dla tej konkretnej grupy maszyn:

   ![Konfigurowanie nowej reguły](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Wybierz maszyny** — domyślnie są zaznaczone wszystkie maszyny w zidentyfikowanej grupie. Usuń zaznaczenie dowolnego z nich, aby usunąć je z tej reguły.
   
   1. **Zalecane aplikacje** — przejrzyj tę listę aplikacji, które są wspólne dla maszyn w tej grupie, i zalecane, aby można było je uruchamiać.
   
   1. **Więcej aplikacji** — przejrzyj tę listę aplikacji, które są rzadziej spotykane na maszynach w tej grupie lub które można wykorzystać. Ikona ostrzeżenia wskazuje, że osoba atakująca może użyć konkretnej aplikacji do obejścia listy zezwalań aplikacji. Zalecamy dokładne zapoznanie się z tymi aplikacjami.

      > [!TIP]
      > Obie listy aplikacji zawierają opcję ograniczenia określonej aplikacji do określonych użytkowników. Jeśli to możliwe, należy przyjąć zasadę najmniejszych uprawnień.
      > 
      > Aplikacje są definiowane przez ich wydawców, jeśli aplikacja nie ma informacji o wydawcy (jest niepodpisane), tworzona jest reguła ścieżki dla pełnej ścieżki określonej aplikacji.

   1. Aby zastosować regułę, wybierz pozycję **Inspekcja.** 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Edytowanie reguły adaptacyjnego sterowania aplikacją grupy

Możesz zdecydować się na edycję listy zezwalań dla grupy maszyn z powodu znanych zmian w organizacji. 

Aby edytować reguły dla grupy maszyn:

1. Otwórz pulpit Azure Defender nawigacyjny i w obszarze zaawansowanej ochrony wybierz pozycję **Adaptacyjne kontrolki aplikacji.**

1. Na **karcie Skonfigurowane** wybierz grupę z regułą, którą chcesz edytować.

1. Zapoznaj się z różnymi sekcjami strony **Konfigurowanie reguł sterowania aplikacją** zgodnie z opisem w sekcji Włączanie adaptacyjnego sterowania aplikacją [na grupie maszyn.](#enable-application-controls-on-a-group-of-machines)

1. Opcjonalnie dodaj co najmniej jedną regułę niestandardową:

   1. Wybierz **pozycję Dodaj regułę.**

      ![Dodawanie reguły niestandardowej](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Jeśli definiujesz znaną bezpieczną ścieżkę, zmień typ reguły na "Ścieżka" i wprowadź pojedynczą ścieżkę.  W ścieżce można uwzględnić symbole wieloznaczne.
   
      > [!TIP]
      > Niektóre scenariusze, w których symbole wieloznaczne w ścieżce mogą być przydatne:
      > 
      > * Użycie symbolu wieloznacznego na końcu ścieżki, aby zezwolić na wszystkie pliki wykonywalne w tym folderze i podfolderach.
      > * Użycie symbolu wieloznacznego w środku ścieżki w celu włączenia znanej nazwy pliku wykonywalnego ze zmianą nazwy folderu (na przykład osobistych folderów użytkownika zawierających znany plik wykonywalny, automatycznie wygenerowanych nazw folderów itp.).
  
   1. Zdefiniuj dozwolonych użytkowników i chronione typy plików.

   1. Po zakończeniu definiowania reguły wybierz pozycję **Dodaj**.

1. Aby zastosować zmiany, wybierz pozycję **Zapisz.**


## <a name="review-and-edit-a-groups-settings"></a>Przeglądanie i edytowanie ustawień grupy

1. Aby wyświetlić szczegóły i ustawienia grupy, wybierz pozycję **Ustawienia grupy**

    W tym okienku jest przedstawiana nazwa grupy (którą można zmodyfikować), typ systemu operacyjnego, lokalizacja i inne istotne szczegóły.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Strona ustawień grupy dla adaptacyjnych kontrolek aplikacji" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Opcjonalnie zmodyfikuj tryby ochrony nazwy lub typu pliku grupy.

1. Wybierz **pozycję Zastosuj** i **zapisz**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Reagowanie na zalecenie "Reguły listy zezwalania w zasadach adaptacyjnego sterowania aplikacją powinny zostać zaktualizowane"

Zobaczysz to zalecenie, gdy Security Center maszynowe zidentyfikuje potencjalnie uzasadnione zachowanie, które nie było wcześniej dozwolone. Zalecenie sugeruje nowe reguły dla istniejących definicji w celu zmniejszenia liczby fałszywie dodatnich alertów.

Aby rozwiązać te problemy:

1. Na stronie zaleceń wybierz  zalecenie Reguły listy zezwalania w zasadach adaptacyjnego sterowania aplikacją, aby wyświetlić grupy z nowo zidentyfikowanym, potencjalnie legalnym zachowaniem.

1. Wybierz grupę z regułą, którą chcesz edytować.

1. Zapoznaj się z różnymi sekcjami strony **Konfigurowanie reguł sterowania aplikacją** zgodnie z opisem w sekcji Włączanie adaptacyjnego sterowania aplikacją [na grupie maszyn.](#enable-application-controls-on-a-group-of-machines)

1. Aby zastosować zmiany, wybierz pozycję **Inspekcja.**




## <a name="audit-alerts-and-violations"></a>Inspekcja alertów i naruszeń

1. Otwórz pulpit Azure Defender nawigacyjny i w obszarze zaawansowanej ochrony wybierz pozycję **Adaptacyjne kontrolki aplikacji.**

1. Aby wyświetlić grupy z maszynami z najnowszymi alertami, przejrzyj grupy wymienione na **karcie Skonfigurowane.**

1. Aby dokładniej zbadać ten temat, wybierz grupę.

   ![Ostatnie alerty](./media/security-center-adaptive-application/recent-alerts.png)

1. Aby uzyskać więcej informacji i listę maszyn, których dotyczy problem, wybierz alert.

    Strona alertów zawiera więcej szczegółów alertów i zawiera **link** Podjąć akcję z zaleceniami co do sposobu ograniczenia zagrożenia.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="Czas rozpoczęcia alertów adaptacyjnego sterowania aplikacją to ":::

    > [!NOTE]
    > Funkcje adaptacyjnego sterowania aplikacją oblicza zdarzenia co 12 godzin. "Godzina rozpoczęcia działania" wyświetlana na stronie alertów to czas  utworzenia alertu przez funkcje adaptacyjnego sterowania aplikacją, a nie czas aktywności podejrzanego procesu.


## <a name="move-a-machine-from-one-group-to-another"></a>Przenoszenie maszyny z jednej grupy do innej

Gdy przeniesiesz maszynę z jednej grupy do innej, zastosowane do niego zasady sterowania aplikacji zmienią ustawienia grupy, do których została przeniesiona. Można również przenieść maszynę ze skonfigurowanej grupy do nieskonfigurowane grupy, co spowoduje usunięcie wszystkich reguł kontroli aplikacji, które zostały zastosowane do maszyny.

1. Otwórz pulpit Azure Defender nawigacyjny i w obszarze zaawansowanej ochrony wybierz pozycję **Adaptacyjne kontrolki aplikacji.**

1. Na stronie **Funkcje adaptacyjnego sterowania** aplikacją na **karcie Skonfigurowane** wybierz grupę zawierającą maszynę, która ma zostać przeniesiona.

1. Otwórz listę **skonfigurowanych maszyn.**

1. Otwórz menu maszyny z trzech kropek na końcu wiersza, a następnie wybierz pozycję **Przenieś**. Zostanie **otwarte okienko Przenoszenie maszyny do innej** grupy.

1. Wybierz grupę docelową, a następnie wybierz **pozycję Przenieś maszynę.**

1. Wybierz pozycję **Zapisz**, aby zapisać zmiany.





## <a name="manage-application-controls-via-the-rest-api"></a>Zarządzanie kontrolkami aplikacji za pośrednictwem interfejsu API REST 

Aby programowo zarządzać adaptacyjnym kontrolą aplikacji, użyj naszego interfejsu API REST. 

Odpowiednią dokumentację interfejsu API można znaleźć w sekcji Funkcje adaptacyjnego sterowania Security Center [dokumentacji interfejsu API.](/rest/api/securitycenter/adaptiveapplicationcontrols)

Niektóre funkcje dostępne w interfejsie API REST:

* **Lista** pobiera wszystkie rekomendacje dotyczące grup i udostępnia dane JSON z obiektem dla każdej grupy.

* **Pobierz** pobiera dane JSON z pełnymi danymi rekomendacji (czyli listą maszyn, regułami wydawcy/ścieżki itd.).

* **Put** konfiguruje regułę (użyj danych JSON pobranych za pomocą funkcji **Get** jako treści tego żądania).
 
   > [!IMPORTANT]
   > Funkcja **Put** oczekuje mniejszej liczby parametrów niż zawiera kod JSON zwracany przez polecenie Get.
   >
   > Przed użyciem kodu JSON w żądaniu Put usuń następujące właściwości: recommendationStatus, configurationStatus, issues, location i sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Często zadawane pytania — funkcje adaptacyjnego sterowania aplikacją

- [Czy istnieją jakieś opcje wymuszania kontrolek aplikacji?](#are-there-any-options-to-enforce-the-application-controls)
- [Dlaczego w zalecanych aplikacjach widzę aplikację firmy Qualys?](#why-do-i-see-a-qualys-app-in-my-recommendeded-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Czy istnieją jakieś opcje wymuszania kontrolek aplikacji?
Obecnie nie są dostępne żadne opcje wymuszania. Funkcje adaptacyjnego sterowania aplikacją mają na celu zapewnienie alertów **zabezpieczeń,** jeśli są uruchamiane aplikacje inne niż zdefiniowane jako bezpieczne. Mają one szereg korzyści (Jakie są zalety adaptacyjnego sterowania[aplikacją?)](#what-are-the-benefits-of-adaptive-application-controls)i można je bardzo dostosowywać, jak pokazano na tej stronie.

### <a name="why-do-i-see-a-qualys-app-in-my-recommendeded-applications"></a>Dlaczego w zalecanych aplikacjach widzę aplikację firmy Qualys?
[Azure Defender dla serwerów obejmuje](defender-for-servers-introduction.md) skanowanie w celu zabezpieczenia maszyn bez dodatkowych kosztów. Nie potrzebujesz licencji firmy Qualys, a nawet konta firmy Qualys — wszystko jest obsługiwane bezproblemowo w Security Center. Aby uzyskać szczegółowe informacje o tym skanerze i instrukcje dotyczące sposobu jego wdrażania, zobacz Rozwiązanie do zintegrowanego oceny luk [w zabezpieczeniach usługi Defender.](deploy-vulnerability-assessment-vm.md)

Aby upewnić się, że po Security Center skanera nie są generowane żadne alerty, lista zalecanych możliwości adaptacyjnego sterowania aplikacją zawiera skaner dla wszystkich maszyn. 


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób używania funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center do definiowania list zezwalania aplikacji działających na maszynach platformy Azure i poza platformą Azure. Aby dowiedzieć się więcej o niektórych Security Center innych funkcji ochrony obciążeń w chmurze, zobacz:

* [Informacje o dostępie just in time (JIT) do maszyny wirtualnej](just-in-time-explained.md)
* [Zabezpieczanie klastrów Usługi Azure Kubernetes](defender-for-kubernetes-introduction.md)