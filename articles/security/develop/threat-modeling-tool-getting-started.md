---
title: Wprowadzenie-Microsoft Threat Modeling Tool — Azure | Microsoft Docs
description: Dowiedz się, jak rozpocząć korzystanie z Threat Modeling Tool. Tworzenie diagramu, identyfikowanie zagrożeń, eliminowanie zagrożeń i weryfikowanie wszystkich środków zaradczych.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 322f5f6a79bdce23706b2211ccc04ef2451675d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94515731"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Wprowadzenie do Threat Modeling Tool

Microsoft Threat Modeling Tool 2018 został uwolniony jako **[rozbrany](https://aka.ms/threatmodelingtool)** we wrześniu 2018 w ramach bezpłatnego kliknięcia. Zmiana mechanizmu dostarczania pozwala nam na wypchnięcie najnowszych ulepszeń i poprawek błędów do klientów za każdym razem, gdy otwierają one narzędzie, ułatwiając ich konserwację i używanie.
W tym artykule opisano proces rozpoczynania pracy z podejściem do modelowania zagrożeń SDL firmy Microsoft i pokazano, jak używać narzędzia do tworzenia modeli doskonałych zagrożeń jako szkieletu procesu zabezpieczeń.

Ten artykuł jest oparty na istniejącej znajomości podejścia do modelowania zagrożeń SDL. Aby zapoznać się z krótkim przeglądem, zapoznaj się z artykułami dotyczącymi **[modelowania zagrożeń](/previous-versions/msp-n-p/ff648006(v=pandp.10))** i zarchiwizowaną wersją niezwiązanych z zabezpieczeniami, korzystając z artykułu MSDN z **[podejściem z etapem](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** opublikowanym w 2006.

Aby szybko podsumowywać, podejście obejmuje tworzenie diagramu, identyfikowanie zagrożeń, łagodzenie ich i sprawdzanie poprawności poszczególnych środków zaradczych. Oto diagram, który podświetla ten proces:

![Proces SDL](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Uruchamianie procesu modelowania zagrożeń

Po uruchomieniu Threat Modeling Tool zauważysz kilka rzeczy, jak widać na zdjęciu:

![Pusta strona początkowa](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Sekcja modelu zagrożeń

| Składnik                                   | Szczegóły                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Przycisk opinii, sugestie i problemy** | Obejmuje **[forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** dla wszystkich elementów SDL. Dzięki temu można zapoznać się z możliwościami wykonywania innych użytkowników, a także obejść i zaleceń. Jeśli nadal nie możesz znaleźć tego, czego szukasz, Wyślij wiadomość e-mail tmtextsupport@microsoft.com do naszego zespołu pomocy technicznej, aby Ci pomóc                                                                                                                            |
| **Tworzenie modelu**                          | Otwiera pustą kanwę do rysowania diagramu. Upewnij się, że wybierasz szablon, którego chcesz użyć dla modelu                                                                                                                                                                                                                                                                                                                                                                       |
| **Szablon dla nowych modeli**                 | Musisz wybrać szablon, który ma być używany przed utworzeniem modelu. Naszym głównym szablonem jest szablon modelu zagrożeń platformy Azure, który zawiera wzorniki, zagrożenia i środki związane z platformą Azure. W przypadku modeli ogólnych wybierz bazę wiedzy SDL TM z menu rozwijanego. Chcesz utworzyć własny szablon lub przesłać nowy dla wszystkich użytkowników? Zapoznaj się z naszą stroną **[repozytorium szablonów](https://github.com/Microsoft/threat-modeling-templates)** w serwisie GitHub, aby dowiedzieć się więcej                              |
| **Otwórz model**                            | <p>Otwiera poprzednio zapisane modele zagrożeń. Funkcja ostatnio otwieranych modeli jest doskonałym rozwiązaniem, jeśli musisz otworzyć najnowsze pliki. Po umieszczeniu wskaźnika myszy na zaznaczeniu znajdziesz dwa sposoby otwierania modeli:</p><p><ul><li>Otwórz z tego komputera — klasyczny sposób otwierania pliku przy użyciu magazynu lokalnego</li><li>Otwórz w usłudze OneDrive — zespoły mogą korzystać z folderów w usłudze OneDrive, aby zapisywać i udostępniać wszystkie modele zagrożeń w jednej lokalizacji, aby zwiększyć produktywność i współpracę</li></ul></p> |
| **Wprowadzenie — przewodnik**                   | Otwiera **[Microsoft Threat Modeling Tool](threat-modeling-tool.md)** stronę główną                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sekcja szablonu

| Składnik               | Szczegóły                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Utwórz nowy szablon** | Otwiera pusty szablon do kompilowania. Jeśli nie masz rozległej wiedzy na temat tworzenia szablonów od podstaw, zalecamy skompilowanie z istniejących |
| **Otwórz szablon**       | Otwiera istniejące szablony, aby wprowadzić zmiany                                                                                                              |

Zespół Threat Modeling Tool ciągle pracuje nad ulepszeniem funkcjonalności narzędzi i środowiska. Niektóre drobne zmiany mogą mieć miejsce w ciągu roku, ale wszystkie istotne zmiany wymagają ponownego zapisu w przewodniku. Często zapoznaj się z nim, aby upewnić się, że otrzymujesz najnowsze anonse.

## <a name="building-a-model"></a>Kompilowanie modelu

W tej sekcji obserwujemy:

- Cristina (deweloper)
- Ricardo (Menedżer programu) i
- Ashish (Tester)

Przechodzą przez proces opracowywania pierwszego modelu zagrożeń.

> Ricardo: Witaj Cristina, pracowałem na diagramie modelu zagrożeń i chciałem upewnić się, że mamy odpowiednie szczegóły. Czy mogę Ci pomóc w przeszukiwaniu?
> Cristina: absolutnie. Spójrzmy na przykład.
> Ricardo otwiera narzędzie i udostępnia swój ekran z Cristina.

![Podstawowy model zagrożeń](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: OK, wygląda prosta, ale czy można go przeszukać?
> Ricardo: Upewnij się! Poniżej przedstawiono podział:
> - Nasz użytkownik ludzki jest rysowany jako jednostka zewnętrzna — kwadrat
> - Wysyłają one polecenia do serwera sieci Web — koło
> - Serwer sieci Web korzysta z bazy danych (dwie równoległe linie)

Co Ricardo tylko Cristina to DPD, krótki dla **[diagramu przepływu danych](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Threat Modeling Tool pozwala użytkownikom na określenie granic zaufania wskazanych czerwono kropkowanymi liniami, aby pokazać, gdzie różne jednostki są w formancie. Na przykład Administratorzy IT wymagają systemu Active Directory na potrzeby uwierzytelniania, dlatego Active Directory jest poza kontrolą.

> Cristina: Wygląda na to, że od razu do mnie. Jakie są zagrożenia?
> Ricardo: Pozwól mi wyświetlić użytkownika.

## <a name="analyzing-threats"></a>Analizowanie zagrożeń

Po kliknięciu widoku analizy z menu ikony (plik z powiększaniem), zostaje on przełączony do listy wygenerowanych zagrożeń znalezionych przez Threat Modeling Tool na podstawie szablonu domyślnego, który używa podejścia SDL o nazwie **[krok (fałszowanie, manipulowanie, ujawnienie informacji, odmowa usługi i podniesienie uprawnień)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Pomysłem jest to, że oprogramowanie znajduje się w przewidywalnym zbiorze zagrożeń, które można znaleźć za pomocą tych 6 kategorii.

Takie podejście przypomina ochronę domu przez upewnienie się, że każde drzwi i okna mają mechanizm blokowania przed dodaniem systemu alarmów lub kartach po złodziej będzie.

![Zagrożenia podstawowe](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo rozpoczyna się od wybrania pierwszego elementu na liście. Oto co się dzieje:

Po pierwsze interakcja między dwoma wzornikami zostaje ulepszona

![Zrzut ekranu przedstawia dwa wzorniki i zakrzywioną strzałkę łączącą je w grubszej grubości linii.](./media/threat-modeling-tool-getting-started/interaction.png)

Po drugie, dodatkowe informacje o zagrożeniu pojawiają się w okno Właściwości zagrożeń

![Zrzut ekranu przedstawia okno Właściwości zagrożeń, które zawierają tytuł, kategorię, opis, interakcje i priorytet.](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Wygenerowane zagrożenie pomaga zrozumieć potencjalne wady projektu. Kategoryzacja kroków daje im pomysł na potencjalną replikę ataków, podczas gdy dodatkowy opis informuje o tym dokładnie, co jest niewłaściwe, oraz potencjalne sposoby jego rozwiązania. Może używać edytowalnych pól, aby pisać uwagi w szczegółach uzasadnienia lub zmienić priorytety klasyfikacji w zależności od paska błędów swojej organizacji.

Szablony platformy Azure zawierają dodatkowe szczegółowe informacje ułatwiające użytkownikom zapoznanie się nie tylko z nieprawidłowymi, ale również sposobami ich naprawy poprzez dodanie opisów, przykładów i hiperlinków do dokumentacji dotyczącej platformy Azure.

W opisie zawarto istotnie dodać mechanizm uwierzytelniania uniemożliwiający sfałszowanie użytkowników, ujawnianie pierwszego zagrożenia, nad którym należy pracować. Kilka minut w dyskusji z Cristina, rozumieją znaczenie implementowania kontroli dostępu i ról. Ricardo wypełnić niektóre szybkie notatki, aby upewnić się, że zostały zaimplementowane.

W miarę jak Ricardo się do zagrożeń w ramach ujawnienia informacji, plan kontroli dostępu wymagał niektórych kont tylko do odczytu na potrzeby inspekcji i generowania raportów. Zastanawiał się, czy powinien to być nowe zagrożenie, ale środki zaradcze były takie same, więc zanotowano odpowiednie zagrożenie.
Uważa również, że informacje o ujawnieniu informacji są nieco większe i zostały zrealizowane, że taśmy kopii zapasowych wymagały szyfrowania, a zadanie dla zespołu operacji.

Zagrożenia nie mają zastosowania do projektu ze względu na istniejące środki zaradcze lub gwarancje bezpieczeństwa można zmienić na "nie dotyczy" z listy rozwijanej stan. Istnieją trzy inne opcje: nie uruchomiono — wybór domyślny, wymaganie badania — używane do wykonywania działań na elementach i skorygowane — gdy jest on w pełni działał.

## <a name="reports--sharing"></a>Raporty & udostępniania

Gdy Ricardo przechodzi przez listę z Cristina i dodaje ważne uwagi, środki zaradcze/uzasadnienia, zmiany priorytetu i stanu, wybiera raporty — > Utwórz pełny raport — > Zapisz raport, który drukuje raport o całkiem, aby przeszedł do współpracowników, aby upewnić się, że zaimplementowano odpowiednią pracę w zabezpieczeniach.

![Zrzut ekranu przedstawia reprezentatywny raport modelowania zagrożeń.](./media/threat-modeling-tool-feature-overview/report.png)

Jeśli Ricardo chce udostępnić plik, można to łatwo zrobić, zapisując konto w usłudze OneDrive swojej organizacji. Gdy robi to, może skopiować link do dokumentu i udostępnić go współpracownikom. 

## <a name="threat-modeling-meetings"></a>Spotkania modelowania zagrożeń

Gdy Ricardo wysłał swój model zagrożeń do swojego współpracownika przy użyciu usługi OneDrive, Ashish, tester Underwhelmed. Zdarza się, że Ricardo i Cristina nie pozostały w kilku ważnych przypadkach narożnych, co może być łatwo naruszone. Skepticism to uzupełnienie modeli zagrożeń.

W tym scenariuszu po przeprowadzeniu Ashish przez model zagrożeń jest on wywoływany dla dwóch spotkań modelowania zagrożeń: jedno spotkanie do synchronizacji procesu i przechodzenie przez diagramy, a następnie drugie spotkanie na potrzeby przeglądu zagrożeń i wylogowania.

W pierwszym spotkaniu Ashish poświęcone 10 minutom na przechodzenie przez proces modelowania zagrożeń SDL. Następnie ściąga Diagram modelu zagrożeń i rozpoczął jego szczegółowo wyjaśnienie. W ciągu pięciu minut zidentyfikowano ważny brakujący składnik.

Kilka minut później, Ashish i Ricardo przygotowano do przedłużonej dyskusji na temat tworzenia serwera sieci Web. Nie było idealnym sposobem na kontynuację spotkania, ale wszyscy ostatecznie zgodziły się na wykrycie niezgodności, aby zaoszczędzić czas w przyszłości.

W drugim spotkaniu zespół przeprowadził przez zagrożenia, omawiając różne sposoby ich rozwiązywania i wylogowania w modelu zagrożeń. Sprawdzają dokumenty w kontroli źródła i kontynuują tworzenie.

## <a name="thinking-about-assets"></a>Zasoby dotyczące zasobów

Niektórzy czytelnicy z modelem zagrożeń mogą zauważyć, że nie poznamy jeszcze informacji o zasobach. Wykryliśmy, że wielu inżynierów oprogramowania znają swoje oprogramowanie lepiej niż rozumieją pojęcie zasobów i jakie zasoby mogą być zainteresowane przez osobę atakującą.

Jeśli zamierzasz utworzyć model zagrożenia, możesz zacząć od zamyślenia o swojej rodzinie, przechowywania niemożliwych zdjęciach lub cennej kompozycji. Może się okazać, że zastanawiasz się, kto może przerwać pracę i bieżący system zabezpieczeń. Można też zacząć od uwzględnienia funkcji fizycznych, takich jak Pula lub Porch przedniej. Są one analogiczne do zastanawiania się nad zasobami, osobami atakującymi lub projektem oprogramowania. Dowolna z tych trzech podejścia zadziałała.

Podejście do modelowania zagrożeń przedstawione tutaj jest znacznie prostsze niż w przeszłości przez firmę Microsoft. Okazało się, że podejście do projektowania oprogramowania działa dobrze dla wielu zespołów. Mamy nadzieję, że to ty.

## <a name="next-steps"></a>Następne kroki

Wyślij swoje pytania, komentarze i wątpliwości do programu tmtextsupport@microsoft.com . **[Pobierz](https://aka.ms/threatmodelingtool)** Threat Modeling Tool, aby rozpocząć pracę.