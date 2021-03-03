---
title: Monitorowanie za pomocą wieloetapowych testów sieci Web — Application Insights platformy Azure
description: Konfigurowanie wieloetapowych testów sieci Web w celu monitorowania aplikacji sieci Web przy użyciu usługi Azure Application Insights
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 1d3597eaf54c40fb1f986d822af0dd6b8c8a7b2e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719852"
---
# <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

Można monitorować zarejestrowane sekwencje adresów URL i interakcji z witryną internetową za pomocą wieloetapowych testów sieci Web. Ten artykuł przeprowadzi Cię przez proces tworzenia wieloetapowego testu sieci Web za pomocą Visual Studio Enterprise.

> [!NOTE]
> Wieloetapowe testy sieci Web zależą od plików WebTest programu Visual Studio. Zostało [ogłoszone](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) , że program Visual Studio 2019 będzie ostatnią wersją z funkcjonalnością WebTest. Ważne jest, aby zrozumieć, że podczas gdy nie zostaną dodane żadne nowe funkcje, funkcja WebTest w programie Visual Studio 2019 nadal jest obsługiwana i będzie nadal obsługiwana w ramach cyklu życia produktu. Azure Monitor zespół produkcyjny zakwestionuje pytania dotyczące przyszłościowych [testów dostępności](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)wieloetapowej.  
> </br>
> Wieloetapowe testy sieci Web **nie są obsługiwane** w chmurze [Azure Government](../../azure-government/index.yml) .


## <a name="pre-requisites"></a>Wymagania wstępne

* Program Visual Studio 2017 Enterprise lub nowszy.
* Narzędzia do testowania wydajności i obciążenia sieci Web w programie Visual Studio.

Aby zlokalizować wymagania wstępne narzędzi do testowania. Uruchom debugowanie **Instalator programu Visual Studio**  >  **poszczególnych składników**  >  **i testowanie**  >  **narzędzi do testowania wydajności sieci Web i testów obciążenia**.

![Zrzut ekranu przedstawiający interfejs użytkownika Instalatora programu Visual Studio z pojedynczymi składnikami, które są zaznaczone z CheckBox obok elementu narzędzi do testowania wydajności sieci Web i obciążenia](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Wieloetapowe testy sieci Web mają dodatkowe koszty związane z nimi. Aby dowiedzieć się więcej, zapoznaj się z [oficjalnym przewodnikiem cen](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Rejestrowanie wieloetapowego testu sieci Web 

> [!WARNING]
> Nie zalecamy już korzystania z rejestratora wieloetapowego. Rejestrator został opracowany dla statycznych stron HTML z podstawowymi interakcjami i nie zapewnia funkcjonalnego środowiska dla nowoczesnych stron sieci Web.

Aby uzyskać wskazówki dotyczące tworzenia testów sieci Web programu Visual Studio, zapoznaj się z [oficjalną dokumentacją programu Visual studio 2019](/visualstudio/test/how-to-create-a-web-service-test).

## <a name="upload-the-web-test"></a>Przekaż test sieci Web

1. W portalu Application Insights w okienku dostępność wybierz pozycję **Utwórz** test testowy  >  **Typ**  >  **wieloetapowego testu sieci Web**.

2. Ustaw lokalizacje testu, częstotliwość i parametry alertu.

### <a name="frequency--location"></a>Częstotliwość & lokalizacji

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Częstotliwość testów**| Ustawia częstotliwość uruchamiania testu z każdej lokalizacji testowej. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.|
|**Lokalizacje testów**| Są miejsca, w których serwery wysyłają żądania sieci Web do adresu URL. **Minimalna liczba zalecanych lokalizacji testowych to pięć** , aby upewnić się, że problemy w witrynie sieci Web można odróżnić od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

### <a name="success-criteria"></a>Kryteria sukcesu

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testu** |Zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest liczony jako powodzenie. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.|
| **Dopasowanie zawartości** | Ciąg, na przykład "Witaj!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu. **Zgodność zawartości obsługuje tylko znaki angielskie** |

### <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Niemal w czasie rzeczywistym (wersja zapoznawcza)** | Zalecamy korzystanie z alertów niemal w czasie rzeczywistym. Konfigurowanie tego typu alertu odbywa się po utworzeniu testu dostępności.  |
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3/5 lokalizacji. Optymalna relacja między progiem lokalizacji alertu a liczbą lokalizacji testowych to liczba lokalizacji testu w **lokalizacji alertów**  =  **-2 z co najmniej pięcioma lokalizacjami testów.**|

## <a name="configuration"></a>Konfigurowanie

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Podłączanie czasu i liczb losowych do testu

Załóżmy, że testujesz narzędzie, które pobiera dane zależne od czasu (np. ceny akcji) z zewnętrznego źródła. Podczas rejestrowania testu sieci Web należy używać określonych godzin, ale ustawionych jako parametry testu: StartTime (Godzina rozpoczęcia) i EndTime (Godzina zakończenia).

![Zrzut ekranu aplikacji My awesome stock](./media/availability-multistep/app-insights-72webtest-parameters.png)

Po uruchomieniu testu parametr EndTime powinien zawsze zawierać aktualny czas, a StartTime wartość czasu 15 minut wcześniej.

Wtyczka Data/godzina testu sieci Web umożliwia obsługę czasów Sparametryzuj.

1. Dodaj wtyczkę testu sieci Web do każdej wartości parametru zmiennej, która jest potrzebna. Na pasku narzędzi testu sieci Web wybierz polecenie **Dodaj wtyczkę testu sieci Web**.
    
    ![Dodaj wtyczkę testu sieci Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    W tym przykładzie użyte zostaną dwa wystąpienia wtyczki typu Data i godzina. Jedno wystąpienie odpowiada wartości „15 minut temu”, a drugie „teraz”.

2. Otwórz właściwości każdej wtyczki. Nadaj jej nazwę i skonfiguruj, aby używać czasu bieżącego. W jednej z wtyczek ustaw właściwość Dodaj minuty = -15.

    ![Parametry kontekstu](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. W parametrach testu sieci Web użyj {{nazwa wtyczki}}, aby odwoływać się do nazwy wtyczki.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Teraz przekaż test do portalu. Wartości dynamiczne zostaną zastosowane w każdym przebiegu testu.

### <a name="dealing-with-sign-in"></a>Obsługa logowania

Jeśli użytkownicy logują się do aplikacji, dostępne są różne opcje symulowania logowania, które pozwolą przetestować strony dostępne po zalogowaniu. Zastosowane podejście zależy od typu zabezpieczeń zapewnianych przez aplikację.

We wszystkich przypadkach należy utworzyć konto w ramach aplikacji tylko na potrzeby testowania. Jeśli to możliwe, należy ograniczyć uprawnienia tego konta testowego, aby nie było możliwości, że testy sieci Web będą miały wpływ na rzeczywistych użytkowników.

**Prosta nazwa użytkownika i hasło** Zarejestruj test sieci Web w zwykły sposób. Najpierw usuń pliki cookie.

**Uwierzytelnianie SAML**

|Nazwa właściwości| Opis|
|----|-----|
| Identyfikator URI odbiorców | Identyfikator URI odbiorców dla tokenu SAML.  Jest to identyfikator URI dla Access Control Service (ACS) — w tym przestrzeni nazw ACS i nazwy hosta. |
| Hasło certyfikatu | Hasło certyfikatu klienta, które przyznaje dostęp do osadzonego klucza prywatnego. |
| Certyfikat klienta  | Wartość certyfikatu klienta z kluczem prywatnym w formacie kodowanym algorytmem Base64. |
| Identyfikator nazwy | Identyfikator nazwy dla tokenu |
| Nie po | Przedział czasu, dla którego token będzie prawidłowy.  Wartość domyślna to 5 minut. |
| Nie wcześniej niż | Obiekt TimeSpan, dla którego token utworzony w przeszłości będzie prawidłowy (do rozróżniania czasu).  Wartość domyślna to (wartość ujemna) 5 minut. |
| Nazwa parametru kontekstowego elementu docelowego | Parametr kontekstowy, który będzie otrzymywał wygenerowane potwierdzenie. |


**Klucz tajny klienta** Jeśli aplikacja ma trasę logowania, która obejmuje klucz tajny klienta, Użyj tej trasy. Azure Active Directory (AAD) to przykład usługi, która umożliwia logowanie za pomocą klucza tajnego klienta. W usłudze AAD klucz tajny klienta jest kluczem aplikacji.

Poniżej przedstawiono przykładowy test internetowy aplikacji internetowej platformy Azure przy użyciu klucza aplikacji:

![Przykładowy zrzut ekranu](./media/availability-multistep/client-secret.png)

Pobierz token z usługi AAD przy użyciu klucza tajnego klienta (AppKey).
Wyodrębnij token elementu nośnego z odpowiedzi.
Wywołaj interfejs API przy użyciu tokenu elementu nośnego w nagłówku autoryzacji.
Upewnij się, że test sieci Web jest rzeczywistym klientem, czyli ma własną aplikację w usłudze AAD i użyj jej klucza clientId + App. Twoja usługa w ramach testu ma również własną aplikację w usłudze AAD: identyfikator URI identyfikatora appID tej aplikacji jest odzwierciedlany w teście sieci Web w polu zasób.

### <a name="open-authentication"></a>Uwierzytelnianie otwarte
Przykładem uwierzytelniania otwartego jest logowanie przy użyciu konta Microsoft lub Google. Wiele aplikacji, które używają protokołu OAuth, zapewnia alternatywną obsługę klucza tajnego klienta, więc przede wszystkim należy zbadać tę możliwość.

Jeśli w ramach testu należy zalogować się przy użyciu protokołu OAuth, ogólne podejście jest następujące:

Użyj narzędzia takiego jak Fiddler, aby sprawdzić ruch między przeglądarką sieci Web, witryną uwierzytelniającą a aplikacją.
Wykonaj co najmniej dwa logowania na różnych komputerach lub w różnych przeglądarkach albo w długich odstępach czasu (umożliwi to wygaśnięcie tokenów).
Porównując różne sesje, zidentyfikuj token przekazywany z powrotem z witryny uwierzytelniającej, który jest następnie przekazywany do serwera aplikacji po zalogowaniu.
Nagraj test sieci Web przy użyciu programu Visual Studio.
Sparametryzuj tokeny, ustawiając parametr, gdy token jest zwracany z witryny uwierzytelniającej i używając go w zapytaniu do tej witryny. Program Visual Studio podejmie próby parametryzacji testu, ale parametryzacja tokenów nie przebiegnie poprawnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Dedykowany artykuł dotyczący [rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Następne kroki

* [Alerty dostępności](availability-alerts.md)
* [Testy sieci Web ping dla adresu URL](monitor-web-app-availability.md)
