---
title: Nawiązywanie połączenia z aplikacjami 3270 na komputerze mainframe firmy IBM
description: Integrowanie i automatyzowanie 3270 aplikacji opartych na ekranach z platformą Azure przy użyciu łącznika Azure Logic Apps i IBM 3270
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 02/03/2021
tags: connectors
ms.openlocfilehash: 7873076618125241d9bdf4f225c194572553d060
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573436"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrowanie 3270 aplikacji opartych na ekranach na komputery z systemem Azure przy użyciu łącznika Azure Logic Apps i IBM 3270

Korzystając z usługi Azure Logic Apps i łącznika IBM 3270, możesz uzyskiwać dostęp do aplikacji mainframe IBM i uruchamiać je, przechodząc do ekranów emulatora 3270. Dzięki temu możesz zintegrować aplikacje mainframe IBM z platformą Azure, firmą Microsoft i innymi aplikacjami, usługami i systemami, tworząc zautomatyzowane przepływy pracy za pomocą usługi Azure Logic Apps. Łącznik komunikuje się z komputerami mainframe firmy IBM przy użyciu protokołu TN3270 i jest dostępny we wszystkich regionach usługi Azure Logic Apps z wyjątkiem regionów Azure Government i Azure (Chiny) — 21Vianet. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

W tym artykule opisano te zagadnienia dotyczące korzystania z łącznika 3270: 

* Dlaczego warto używać łącznika IBM 3270 w Azure Logic Apps oraz jak działa łącznik programu 3270 aplikacji opartych na ekranie

* Wymagania wstępne i Instalator korzystania z łącznika 3270

* Procedura dodawania akcji łącznika 3270 do aplikacji logiki

## <a name="why-use-this-connector"></a>Dlaczego warto używać tego łącznika?

Aby uzyskiwać dostęp do aplikacji na komputerze mainframe firmy IBM, zazwyczaj używany jest emulator terminalu 3270, często nazywany "zielonym ekranem". Ta metoda jest przetestowanym czasem, ale ma ograniczenia. Mimo że Host Integration Server (jego) pomaga w pracy bezpośrednio z tymi aplikacjami, czasami rozdzielenie go na ekran i logikę biznesową może nie być możliwe. Lub, być może nie masz już informacji na temat działania aplikacji hosta.

Aby można było rozciągnąć te scenariusze, łącznik IBM 3270 w Azure Logic Apps współpracuje z narzędziem do projektowania 3270, które służy do rejestrowania lub "przechwytywania", ekranów hostów używanych w określonym zadaniu, definiowania przepływu nawigacji dla tego zadania za pomocą aplikacji typu mainframe i definiowania metod z parametrami wejściowymi i wyjściowymi dla tego zadania. Narzędzie projektowe konwertuje te informacje na metadane, które są używane przez łącznik 3270 podczas wywoływania akcji, która reprezentuje to zadanie z aplikacji logiki.

Po wygenerowaniu pliku metadanych przy użyciu narzędzia do projektowania należy dodać ten plik do konta integracji na platformie Azure. Dzięki temu aplikacja logiki będzie mogła uzyskiwać dostęp do metadanych aplikacji po dodaniu akcji łącznika 3270. Łącznik odczytuje plik metadanych z konta integracji, obsługuje nawigację przez ekrany 3270 i dynamicznie wyświetla parametry dla akcji łącznika 3270. Następnie można dostarczyć dane do aplikacji hosta, a łącznik zwraca wyniki do aplikacji logiki. Dzięki temu możesz zintegrować starsze aplikacje z platformą Azure, firmą Microsoft i innymi aplikacjami, usługami i systemami obsługiwanymi przez Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Zalecane: [środowisko usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  To środowisko można wybrać jako lokalizację tworzenia i uruchamiania aplikacji logiki. ISE zapewnia dostęp z aplikacji logiki do zasobów chronionych wewnątrz sieci wirtualnych platformy Azure.

* Aplikacja logiki służąca do automatyzowania i uruchamiania aplikacji opartej na ekranie 3270

  Łącznik IBM 3270 nie ma wyzwalaczy, więc Użyj innego wyzwalacza, aby uruchomić aplikację logiki, taką jak wyzwalacz **cyklu** . Następnie można dodać akcje łącznika 3270. Aby rozpocząć, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Jeśli używasz ISE, wybierz tę ISEę jako lokalizację aplikacji logiki.

* [Pobierz i zainstaluj narzędzie do projektowania 3270](https://aka.ms/3270-design-tool-download).
Jedynym wymaganiem wstępnym jest [Microsoft .NET Framework 4,8](https://aka.ms/net-framework-download).

  To narzędzie ułatwia rejestrowanie ekranów, ścieżek nawigacyjnych, metod i parametrów zadań w aplikacji, które są dodawane i uruchamiane jako akcje łącznika 3270. Narzędzie generuje plik XML programu Host Integration Designer (HIDX), który dostarcza niezbędne metadane dla łącznika do obsługi aplikacji mainframe.
  
  Po pobraniu i zainstalowaniu tego narzędzia wykonaj następujące kroki, aby nawiązać połączenie z hostem:

  1. Otwórz narzędzie projektowania 3270. Z menu **sesji** wybierz pozycję **sesje hosta**.
  
  1. Podaj informacje o serwerze hosta protokołu TN3270.

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), czyli miejsce przechowywania pliku hidx jako mapy, aby aplikacja logiki mogła uzyskać dostęp do definicji metadanych i metod w tym pliku. 

  Upewnij się, że konto integracji jest połączone z używaną aplikacją logiki. Ponadto, jeśli używasz ISE, upewnij się, że lokalizacja konta integracji jest taka sama, ISE, z której korzysta Twoja aplikacja logiki.

* Dostęp do serwera protokołu TN3270, który obsługuje aplikację mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Przegląd tworzenia metadanych

W aplikacji opartej na ekranach 3270 ekrany i pola danych są unikatowe dla Twoich scenariuszy, więc łącznik 3270 potrzebuje tych informacji na temat aplikacji, którą można podać jako metadane. W tych metadanych opisano informacje pomagające aplikacji logiki identyfikować i rozpoznawać ekrany, opisuje sposób nawigowania między ekranami, gdzie można wprowadzać dane i gdzie oczekiwać wyników. Aby określić i wygenerować te metadane, należy użyć narzędzia do projektowania 3270, które przeprowadzi Cię przez te konkretne *tryby* lub etapy, zgodnie z opisem w dalszej części szczegółów:

* **Przechwytywanie**: w tym trybie rejestruje się ekrany wymagane do wykonania określonego zadania z aplikacją mainframe, na przykład w celu uzyskania salda banku.

* **Nawigacja**: w tym trybie należy określić plan lub ścieżkę służącą do nawigowania po ekranach aplikacji mainframe dla określonego zadania.

* **Metody**: w tym trybie należy zdefiniować metodę, na przykład, `GetBalance` która opisuje ścieżkę nawigacji ekranu. Należy również zaznaczyć pola na każdym ekranie, które staną się parametrami wejściowymi i wyjściowymi metody.

### <a name="unsupported-elements"></a>Nieobsługiwane elementy

Narzędzie projektowe nie obsługuje następujących elementów:

* Mapy częściowej obsługi podstawowych mapowań firmy IBM (MS): w przypadku zaimportowania mapy MS narzędzie do projektowania ignoruje definicje ekranu częściowego.

* Przetwarzanie menu

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Przechwyć ekrany

W tym trybie oznaczasz element na każdym ekranie 3270, który jednoznacznie identyfikuje ten ekran. Na przykład można określić wiersz tekstu lub bardziej złożony zestaw warunków, takich jak konkretny tekst i niepuste pole. Można zarejestrować te ekrany za pośrednictwem połączenia na żywo z serwerem hosta lub zaimportować te informacje z mapy programu IBM Basic Mappinging Support (MS). Połączenie na żywo używa emulatora protokołu TN3270 do nawiązywania połączenia z hostem. Każda akcja łącznika musi być zamapowana na jedno zadanie, które rozpoczyna się od nawiązywania połączenia z sesją i kończące się na rozłączeniu z sesją.

1. Jeśli jeszcze tego nie zrobiono, Otwórz narzędzie projektowania 3270. Na pasku narzędzi wybierz pozycję **Przechwyć** , aby wprowadzić tryb przechwytywania.

1. Z menu **sesji** wybierz pozycję **Połącz**.

1. Aby rozpocząć nagrywanie, w menu **nagrywanie** wybierz pozycję **Rozpocznij nagrywanie**. (Klawiatura: Ctrl + E)

1. W okienku **przechwytywanie** , rozpoczynając od pierwszego ekranu w aplikacji, przejdź do aplikacji w poszukiwaniu konkretnego zadania, które jest rejestrowane.

1. Po zakończeniu zadania Wyloguj się z aplikacji w zwykły sposób.

1. Z menu **sesji** wybierz pozycję **Rozłącz**.

1. Aby zatrzymać nagrywanie, w menu **nagrywanie** wybierz polecenie **Zatrzymaj nagrywanie**. (Klawiatura: Ctrl + Shift + E)

   Po przechwyceniu ekranów dla zadania narzędzie Projektant pokazuje miniatury reprezentujące te ekrany. Niektóre uwagi dotyczące tych miniatur:

   * Dołączenie do przechwyconych ekranów zawiera ekran o nazwie "Empty".

     Po pierwszym nawiązaniu połączenia z usługą [CICS](https://www.ibm.com/it-infrastructure/z/cics)należy wysłać klucz "Clear", aby można było wprowadzić nazwę transakcji, która ma zostać uruchomiona. Ekran, na którym wysyłasz klucz "Clear", nie ma żadnych *atrybutów rozpoznawania*, takich jak tytuł ekranu, który można dodać przy użyciu edytora rozpoznawania ekranu. Aby przedstawić ten ekran, miniatury obejmują ekran o nazwie "Empty". Możesz później użyć tego ekranu do reprezentowania ekranu, w którym wprowadzasz nazwę transakcji.

   * Domyślnie nazwa przechwyconego ekranu używa pierwszego wyrazu na ekranie. Jeśli ta nazwa już istnieje, narzędzie projektowania dołącza nazwę z podkreśleniem i cyfrą, na przykład "WBGB" i "WBGB_1".

1. Aby nadać bardziej zrozumiałą nazwę przechwyconego ekranu, wykonaj następujące kroki:

   1. W okienku **ekrany hosta** wybierz ekran, którego nazwę chcesz zmienić.

   1. W tym samym okienku w dolnej części tego samego okienka Znajdź właściwość **Nazwa ekranu** .

   1. Zmień nazwę bieżącego ekranu na bardziej opisową nazwę.

1. Teraz Określ pola do identyfikacji każdego ekranu.

   W przypadku strumienia danych 3270 ekrany nie mają identyfikatorów domyślnych, dlatego należy wybrać unikatowy tekst na każdym ekranie. W przypadku złożonych scenariuszy można określić wiele warunków, na przykład unikatowy tekst i pole z określonym warunkiem.

Po wybraniu pól rozpoznawania przejdź do następnego trybu.

### <a name="conditions-for-identifying-repeated-screens"></a>Warunki identyfikacji powtarzających się ekranów

Aby łącznik mógł nawigować i rozróżnić między ekranami, zazwyczaj można znaleźć unikatowy tekst na ekranie, którego można użyć jako identyfikatora na przechwyconych ekranach. W przypadku powtarzających się ekranów mogą być potrzebne dodatkowe metody identyfikacji. Załóżmy na przykład, że istnieją dwa ekrany, które wyglądają tak samo, oprócz jednego ekranu zwraca prawidłową wartość, podczas gdy drugi ekran zwraca komunikat o błędzie.

W narzędziu projektowym można dodać *atrybuty rozpoznawania*, na przykład tytuł ekranu, taki jak "Pobierz saldo konta" przy użyciu edytora rozpoznawania ekranu. Jeśli masz ścieżkę rozwidlenia, a obie gałęzie zwracają ten sam ekran, ale z różnymi wynikami, potrzebne są inne atrybuty rozpoznawania. W czasie wykonywania łącznik używa tych atrybutów do określenia bieżącej gałęzi i rozwidlenia. Poniżej przedstawiono warunki, których można użyć:

* Określona wartość: Ta wartość jest zgodna z określonym ciągiem w określonej lokalizacji.
* NIE określona wartość: Ta wartość jest niezgodna z określonym ciągiem w określonej lokalizacji.
* Puste: to pole jest puste.
* Niepuste: to pole nie jest puste.

Aby dowiedzieć się więcej, zobacz [przykładowy plan nawigacji](#example-plan) w dalszej części tego tematu.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definiowanie planów nawigacji

W tym trybie należy zdefiniować przepływ lub etapy nawigowania po ekranach aplikacji mainframe dla określonego zadania. Na przykład czasami może istnieć więcej niż jedna ścieżka, którą aplikacja może wykonać, gdy jedna ścieżka generuje prawidłowy wynik, podczas gdy druga ścieżka generuje błąd. Dla każdego ekranu Określ naciśnięcia klawiszy, które są niezbędne do przejścia do następnego ekranu, na przykład `CICSPROD <enter>` .

> [!TIP]
> W przypadku automatyzowania kilku zadań wykorzystujących te same ekrany łączenia i rozłączania narzędzie projektowania oferuje specjalne typy planów łączenia i rozłączania. Podczas definiowania tych planów można je dodać do początku i końca planu nawigacji.

### <a name="guidelines-for-plan-definitions"></a>Wskazówki dotyczące definicji planu

* Dołącz wszystkie ekrany, rozpoczynając od połączenia i kończąc z rozłączaniem.

* Można utworzyć plan samodzielny lub użyć planów łączenia i rozłączania, które umożliwiają ponowne użycie serii ekranów wspólnych dla wszystkich transakcji.

  * Ostatni ekran w planie połączenia musi być tym samym ekranem co pierwszy ekran w planie nawigacji.

  * Pierwszy ekran w planie rozłączenia musi być tym samym ekranem co ostatni ekran w planie nawigacji.

* Przechwycone ekrany mogą zawierać wiele powtarzających się ekranów, więc wybierz i Użyj tylko jednego wystąpienia dowolnego Powtórzonego ekranu w planie. Poniżej przedstawiono kilka przykładów powtarzających się ekranów:

  * Ekran logowania, na przykład ekran **MSG-10**
  * Ekran powitalny dla CICS
  * Ekran "Wyczyść" lub **pusty**

<a name="create-plans"></a>

### <a name="create-plans"></a>Tworzenie planów

1. Na pasku narzędzi Narzędzia do projektowania 3270 wybierz opcję **Nawigacja** , aby przejść do trybu nawigacji.

1. Aby rozpocząć plan, w okienku **nawigacji** wybierz pozycję **nowy plan**.

1. W obszarze **Wybierz nową nazwę planu** wprowadź nazwę planu. Z listy **Typ** wybierz typ planu:

   | Typ planu | Opis |
   |-----------|-------------|
   | **Proces** | W przypadku planów autonomicznych lub połączonych |
   | **Połącz** | Plany połączeń |
   | **Rozłącz** | Plany rozłączenia |
   |||

1. W okienku **ekrany hosta** przeciągnij przechwycone miniatury na powierzchnię planu nawigacji w okienku **nawigacji** .

   Aby przedstawić pusty ekran, w którym wprowadzasz nazwę transakcji, Użyj ekranu "pusty".

1. Rozmieść ekrany w kolejności opisującej definiowane zadanie.

1. Aby zdefiniować ścieżkę przepływu między ekranami, w tym rozwidleniami i sprzężeniami, na pasku narzędzi Narzędzia do projektowania wybierz pozycję **przepływ**.

1. Wybierz pierwszy ekran w przepływie. Przeciągnij i narysuj połączenie z następnym ekranem w przepływie.

1. Dla każdego ekranu podaj wartości właściwości **klucz pomocy** (identyfikator uwagi) i Właściwość **FIXED Text** , która przenosi przepływ do następnego ekranu.

   Może być tylko klucz pomocy lub zarówno klucz pomocy, jak i stały tekst.

Po zakończeniu planu nawigacji można [zdefiniować metody w następnym trybie](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Przykład

W tym przykładzie Załóżmy, że uruchamiasz transakcję CICS o nazwie "WBGB", która zawiera następujące kroki: 

* Na pierwszym ekranie wprowadź nazwę i konto.
* Na drugim ekranie zostanie wyświetlone saldo konta.
* Opuszczasz ekran "pusty".
* Użytkownik wyloguje się z CICS na ekranie "MSG-10".

Załóżmy również, że powtarzasz te kroki, ale wprowadzisz nieprawidłowe dane, aby można było przechwycić ekran, który zawiera błąd. Oto przechwycone ekrany:

* MSG-10
* CICS — Zapraszamy!
* Pusty
* WBGB_1 (dane wejściowe)
* WBGB_2 (błąd)
* Empty_1
* MSG-10_1

Mimo że wiele ekranów w tym miejscu otrzymuje unikalne nazwy, niektóre ekrany są tego samego ekranu, na przykład "MSG-10" i "Empty". Dla Powtórzonego ekranu należy użyć tylko jednego wystąpienia dla tego ekranu w planie. Poniżej przedstawiono przykłady pokazujące, w jaki sposób plan autonomiczny, plan połączenia, plan rozłączenia i połączony plan mogą wyglądać:

* Plan autonomiczny

  ![Autonomiczny plan nawigacji](./media/connectors-create-api-3270/standalone-plan.png)

* Połącz plan

  ![Połącz plan](./media/connectors-create-api-3270/connect-plan.png)

* Odłączanie planu

  ![Odłączanie planu](./media/connectors-create-api-3270/disconnect-plan.png)

* Połączony plan

  ![Połączony plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Przykład: Identyfikowanie powtórzonych ekranów

W przypadku łącznika do nawigowania i rozróżniania ekranów zwykle znajduje się na ekranie unikatowy tekst, którego można użyć jako identyfikatora na przechwyconych ekranach. W przypadku powtarzających się ekranów mogą być potrzebne dodatkowe metody identyfikacji. Przykładowy plan zawiera rozwidlenie, w którym można uzyskać podobne ekrany. Jeden ekran zwraca saldo konta, podczas gdy drugi ekran zwraca komunikat o błędzie.

Narzędzie projektowania pozwala dodawać atrybuty rozpoznawania, na przykład tytuł ekranu o nazwie "Pobierz saldo konta" przy użyciu edytora rozpoznawania ekranu. W przypadku podobnych ekranów potrzebne są inne atrybuty. W czasie wykonywania łącznik używa tych atrybutów do określenia gałęzi i rozwidlenia.

* W gałęzi, która zwraca prawidłowe dane wejściowe, który jest ekranem z saldem konta, można dodać pole, które ma warunek "niepuste".

* W gałęzi, która zwraca z błędem, można dodać pole, które ma warunek "Empty".

<a name="define-method"></a>

## <a name="define-methods"></a>Definiuj metody

W tym trybie należy zdefiniować metodę, która jest skojarzona z planem nawigacji. Dla każdego parametru metody należy określić typ danych, na przykład ciąg, liczbę całkowitą, datę lub godzinę itd. Gdy skończysz, możesz przetestować metodę na hoście aktywnym i upewnić się, że metoda działa zgodnie z oczekiwaniami. Następnie można wygenerować plik metadanych lub plik XML programu Integration Designer (HIDX), który ma teraz definicje metod do użycia podczas tworzenia i uruchamiania akcji dla łącznika IBM 3270.

1. Na pasku narzędzi Narzędzia do projektowania 3270 wybierz pozycję **metody** , aby wprowadzić tryb metod. 

1. W okienku **nawigacji** wybierz ekran z polami wejściowymi, które chcesz.

1. Aby dodać pierwszy parametr wejściowy do metody, wykonaj następujące kroki:

   1. W okienku **przechwytywanie** na ekranie emulatora 3270 wybierz całe pole, a nie tylko tekst wewnątrz pola, które chcesz utworzyć jako pierwsze dane wejściowe.

      > [!TIP]
      > Aby wyświetlić wszystkie pola i upewnić się, że wybrano pole kompletne, w menu **Widok** wybierz pozycję **wszystkie pola**.

   1. Na pasku narzędzi narzędzia projektowania wybierz pozycję **pole wejściowe**. 

   Aby dodać więcej parametrów wejściowych, powtórz poprzednie kroki dla każdego parametru.

1. Aby dodać pierwszy parametr wyjściowy metody, wykonaj następujące kroki:

   1. W okienku **przechwytywanie** na ekranie emulatora 3270 wybierz całe pole, a nie tylko tekst wewnątrz pola, które ma zostać wybrane jako pierwsze wyjście.

      > [!TIP]
      > Aby wyświetlić wszystkie pola i upewnić się, że wybrano pole kompletne, w menu **Widok** wybierz pozycję **wszystkie pola**.

   1. Na pasku narzędzi narzędzia projektowania wybierz pozycję **pole wyjściowe**.

   Aby dodać więcej parametrów wyjściowych, powtórz poprzednie kroki dla każdego parametru.

1. Po dodaniu wszystkich parametrów metody Zdefiniuj następujące właściwości dla każdego parametru:

   | Nazwa właściwości | Możliwe wartości | 
   |---------------|-----------------|
   | **Typ danych** | Bajt, Data i godzina, liczba dziesiętna, int, Long, Short, String |
   | **Technika wypełnienia pola** | Parametry obsługują te typy wypełniania, wypełniając wartości puste, w razie potrzeby: <p><p>- **Typ**: Wprowadź znaki sekwencyjnie do pola. <p>- **Wypełnienie**: Zastąp zawartość pola znakami, wypełniając wartości puste, jeśli jest to konieczne. <p>- **EraseEofType**: Wyczyść pole, a następnie wprowadź znaki sekwencyjnie do pola. |
   | **Ciąg formatu** | Niektóre typy danych parametrów używają ciągu formatu, który informuje łącznik 3270 o sposobie konwersji tekstu z ekranu na typ danych .NET: <p><p>- **DateTime**: ciąg formatu daty i godziny jest zgodny z [niestandardowymi ciągami formatu data i godzina platformy .NET](/dotnet/standard/base-types/custom-date-and-time-format-strings). Na przykład Data `06/30/2019` używa ciągu formatu `MM/dd/yyyy` . <p>- **Decimal**: ciąg formatu dziesiętnego używa [klauzuli COBOL Picture](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_73/rzasb/picture.htm). Na przykład liczba `100.35` używa ciągu formatu `999V99` . |
   |||

## <a name="save-and-view-metadata"></a>Zapisz i Wyświetl metadane

Po zdefiniowaniu metody, ale przed przetestowaniem metody, Zapisz wszystkie informacje zdefiniowane do oddzielenia do pliku RAP (RD).
W dowolnym momencie można zapisywać w tym pliku RAP w dowolnym trybie. Narzędzie projektowania zawiera również przykładowy plik RAP, który można otworzyć i przejrzeć, przechodząc do folderu instalacyjnego narzędzia projektowania w tej lokalizacji i otwierając plik "WoodgroveBank. Rd":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Jednak w przypadku próby zapisania zmian w pliku przykładowego RAP lub wygenerowania pliku HIDX z przykładowego pliku RAP, gdy plik pozostaje w folderze instalacyjnym narzędzia projektowego, może zostać wyświetlony komunikat o błędzie "odmowa dostępu". Domyślnie narzędzie projektowania jest instalowane w folderze Program Files bez podwyższonego poziomu uprawnień. Jeśli wystąpi błąd, Wypróbuj jedno z następujących rozwiązań:

* Skopiuj przykładowy plik do innej lokalizacji.
* Uruchom Narzędzie projektowe jako administrator.
* Oznacz siebie jako właściciela dla folderu zestawu SDK.

## <a name="test-your-method"></a>Testowanie metody

1. Aby uruchomić metodę względem hosta na żywo, podczas gdy nadal w trybie metody, naciśnij klawisz F5 lub na pasku narzędzi narzędzia projektowania wybierz pozycję **test**.

   > [!TIP]
   > Tryby można zmienić w dowolnym momencie. W menu **plik** wybierz **tryb**, a następnie wybierz żądany tryb.

1. Wprowadź wartości parametrów i wybierz **przycisk OK**.

1. Aby przejść do następnego ekranu, wybierz pozycję **dalej**.

1. Gdy skończysz, wybierz pozycję **gotowe**, aby wyświetlić wartości parametrów wyjściowych.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generuj i Przekaż plik HIDX

Gdy wszystko będzie gotowe, wygeneruj plik HIDX, aby umożliwić przekazanie go do konta integracji. Narzędzie projektowania 3270 tworzy plik HIDX w nowym podfolderze, w którym zapisano plik RAP.

1. W narzędziu projektowanie 3270 z menu **Narzędzia** wybierz polecenie **Generuj definicje**. (Klawiatura: F6)

1. Przejdź do folderu, który zawiera plik RAP, i Otwórz podfolder utworzony przez narzędzie po wygenerowaniu pliku HIDX. Upewnij się, że narzędzie utworzyło plik HIDX.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Znajdź konto integracji.

1. Dodaj plik HIDX jako mapę do konta integracji, wykonując [następujące podobne kroki w celu dodania map](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ale po wybraniu typu mapy wybierz pozycję **hidx**.

W dalszej części tego tematu, gdy dodasz akcję IBM 3270 do aplikacji logiki po raz pierwszy, zostanie wyświetlony monit o utworzenie połączenia między aplikacją logiki a serwerem hosta, dostarczając informacje o połączeniu, takie jak nazwy konta integracji i serwera hosta. Po utworzeniu połączenia możesz wybrać wcześniej dodany plik HIDX, metodę do uruchomienia i parametry, które mają być używane.

Po zakończeniu wszystkich tych kroków możesz użyć akcji utworzonej w aplikacji logiki w celu nawiązania połączenia z komputerem typu IBM mainframe, ekranami dysków dla aplikacji, wprowadzać dane, zwracać wyniki itd. Możesz również kontynuować dodawanie innych akcji do aplikacji logiki w celu integracji z innymi aplikacjami, usługami i systemami.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Uruchamianie akcji IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok** **>** **Dodaj akcję**. 

1. W polu wyszukiwania wybierz pozycję **Enterprise**. W polu wyszukiwania wprowadź `3270` jako filtr. Z listy Akcje wybierz akcję o nazwie **Uruchom program mainframe w ramach połączenia protokołu TN3270**

   ![Wybierz akcję 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. Jeśli połączenie nie istnieje jeszcze, podaj niezbędne informacje dotyczące połączenia, a następnie wybierz pozycję **Utwórz**.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | <*Nazwa połączenia*> | Nazwa połączenia |
   | **Identyfikator konta integracji** | Tak | <*Integracja — nazwa konta*> | Nazwa konta integracji |
   | **Adres URL sygnatury dostępu współdzielonego konta integracji** | Tak | <*Integration-Account-SAS-URL*> | Adres URL sygnatury dostępu współdzielonego (SAS) konta integracji, który można wygenerować na podstawie ustawień konta integracji w Azure Portal. <p>1. w menu konto integracji w obszarze **Ustawienia** wybierz pozycję **adres URL wywołania zwrotnego**. <br>2. w okienku po prawej stronie Skopiuj **wygenerowaną wartość adresu URL wywołania zwrotnego** . |
   | **Server** (Serwer) | Tak | <*PROTOKOŁU TN3270-Server-Name*> | Nazwa serwera dla usługi protokołu TN3270 |
   | **Port** | Nie | <*PROTOKOŁU TN3270-serwer-port*> | Port używany przez serwer protokołu TN3270. Jeśli pole pozostanie puste, łącznik używa `23` jako wartości domyślnej. |
   | **Typ urządzenia** | Nie | <*IBM-Terminal-model*> | Nazwa lub numer modelu terminalu IBM do emulowania. Jeśli pole pozostanie puste, łącznik użyje wartości domyślnych. |
   | **Strona kodowa** | Nie | <*kod — numer strony*> | Numer strony kodowej dla hosta. Jeśli pole pozostanie puste, łącznik używa `37` jako wartości domyślnej. |
   | **Nazwa jednostki logicznej** | Nie | <*Nazwa jednostki logicznej*> | Określona nazwa jednostki logicznej do żądania od hosta |
   | **Włączyć protokół SSL?** | Nie | Włączone lub wyłączone | Włączać lub wyłączać szyfrowanie TLS. |
   | **Czy zweryfikować certyfikat SSL hosta?** | Nie | Włączone lub wyłączone | Włącz lub wyłącz weryfikację dla certyfikatu serwera. |
   ||||

   Na przykład:

   ![Connection properties (Właściwości połączenia)](./media/connectors-create-api-3270/connection-properties.png)

1. Podaj niezbędne informacje dotyczące akcji:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa hidx** | Tak | <*HIDX — nazwa pliku*> | Wybierz plik HIDX 3270, którego chcesz użyć. |
   | **Nazwa metody** | Tak | <*Nazwa metody*> | Wybierz metodę w pliku HIDX, który ma być używany. Po wybraniu metody zostanie wyświetlona lista **Dodaj nowy parametr** , aby można było wybrać parametry do użycia z tą metodą. |
   ||||

   Na przykład:

   **Wybierz plik HIDX**

   ![Wybierz plik HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Wybierz metodę**

   ![Select — metoda](./media/connectors-create-api-3270/select-method.png)

   **Wybierz parametry**

   ![Wybieranie parametrów](./media/connectors-create-api-3270/add-parameters.png)

1. Gdy skończysz, Zapisz i uruchom aplikację logiki.

   Po zakończeniu działania aplikacji logiki zostaną wyświetlone kroki z przebiegu. 
   Pomyślne kroki pokazują znaczniki zaznaczenia, podczas gdy nieudane kroki pokazują literę "X".

1. Aby przejrzeć dane wejściowe i wyjściowe dla każdego kroku, rozwiń ten krok.

1. Aby przejrzeć dane wyjściowe, wybierz pozycję **Zobacz nieprzetworzone wyjścia**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/si3270/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)

