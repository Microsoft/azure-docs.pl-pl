---
title: Tworzenie Logic Apps w wersji zapoznawczej w Azure Portal
description: Twórz i uruchamiaj przepływy pracy na potrzeby scenariuszy automatyzacji i integracji z Azure Logic Apps wersji zapoznawczej w Azure Portal.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 3cf5047dbb79f6d8b35b0fe089069a20ab4a50a6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736375"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Twórz stanowe i bezstanowe przepływy pracy w Azure Portal z podglądem Azure Logic Apps

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzięki usłudze [Azure Logic Apps Preview](logic-apps-overview-preview.md)możesz tworzyć rozwiązania do automatyzacji i integracji dla aplikacji, danych, usług w chmurze i systemów przez tworzenie i uruchamianie aplikacji logiki, które obejmują [ *stanowe* i *bezstanowe* przepływy pracy](logic-apps-overview-preview.md#stateful-stateless) w Azure Portal, rozpoczynając od nowego typu zasobu **aplikacji logiki (wersja zapoznawcza)** . Przy użyciu tego nowego typu aplikacji logiki można utworzyć wiele przepływów pracy, które są obsługiwane przez przeprojektowaną wersję środowiska uruchomieniowego Azure Logic Apps Preview, która zapewnia przenośność, lepszą wydajność i elastyczność wdrażania i uruchamiania w różnych środowiskach hostingu, a nie tylko na platformie Azure, ale również kontenerów platformy Docker. Aby dowiedzieć się więcej na temat nowego typu aplikacji logiki, zobacz [omówienie Azure Logic Apps wersji zapoznawczej](logic-apps-overview-preview.md).

![Zrzut ekranu, który pokazuje Azure Portal za pomocą projektanta przepływu pracy dla zasobu "aplikacja logiki (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

W Azure Portal możesz zacząć od utworzenia nowego zasobu **aplikacji logiki (wersja zapoznawcza)** . Można również rozpocząć od [utworzenia projektu w Visual Studio Code z rozszerzeniem Azure Logic Apps (wersja zapoznawcza)](create-stateful-stateless-workflows-visual-studio-code.md), obie podejścia zapewniają możliwość wdrażania i uruchamiania aplikacji logiki w tym samym rodzaju środowiskach hostingu.

W tym czasie można nadal utworzyć oryginalny typ aplikacji logiki. Chociaż środowiska programistyczne w portalu różnią się między oryginalnymi i nowymi typami aplikacji logiki, Twoja subskrypcja platformy Azure może zawierać oba typy. Możesz wyświetlać i uzyskiwać dostęp do wszystkich wdrożonych aplikacji logiki w ramach subskrypcji platformy Azure, ale aplikacje są zorganizowane w ich własne kategorie i sekcje.

W tym artykule przedstawiono sposób tworzenia aplikacji logiki i przepływu pracy w Azure Portal przy użyciu typu zasobu **aplikacja logiki (wersja zapoznawcza)** i wykonywanie tych zadań wysokiego poziomu:

* Utwórz nowy zasób aplikacji logiki i Dodaj pusty przepływ pracy.

* Dodaj wyzwalacz i akcję.

* Wyzwalanie przebiegu przepływu pracy.

* Wyświetl historię uruchomienia i wyzwalacza przepływu pracy.

* Włącz lub Otwórz Application Insights po wdrożeniu.

* Włącz historię uruchamiania dla bezstanowych przepływów pracy.

> [!NOTE]
> Informacje o bieżących znanych problemach można znaleźć na [stronie Logic Apps znane problemy w wersji zapoznawczej w witrynie GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* [Konto usługi Azure Storage](../storage/common/storage-account-overview.md) , ponieważ zasób **aplikacji logiki (wersja zapoznawcza)** jest obsługiwany przez Azure Functions i ma [wymagania dotyczące magazynu podobne do aplikacji funkcji](../azure-functions/storage-considerations.md). Możesz użyć istniejącego konta magazynu lub można utworzyć konto magazynu z wyprzedzeniem lub podczas tworzenia aplikacji logiki.

  > [!NOTE]
  > [Aplikacje logiki stanowej](logic-apps-overview-preview.md#stateful-stateless) wykonują transakcje magazynu, takie jak używanie kolejek do planowania i przechowywania stanów przepływu pracy w tabelach i obiektach Blob. Te transakcje wiążą się z [opłatami za usługę Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Aby uzyskać więcej informacji o tym, jak stanowe Aplikacje logiki są przechowywane w magazynie zewnętrznym, zobacz [stanowe i bezstanowe](logic-apps-overview-preview.md#stateful-stateless).

* Do wdrożenia w kontenerze platformy Docker potrzebny jest istniejący obraz kontenera Docker. Na przykład można utworzyć ten obraz za pomocą [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)lub [wystąpienia kontenera platformy Azure](../container-instances/container-instances-overview.md). 

* Aby móc utworzyć tę samą przykładową aplikację logiki w tym artykule, potrzebujesz konta e-mail programu Outlook w usłudze Office 365, które używa konta służbowego firmy Microsoft do logowania.

  Jeśli zdecydujesz się użyć innego [łącznika poczty e-mail, który jest obsługiwany przez Azure Logic Apps](/connectors/), na przykład Outlook.com lub [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), możesz nadal postępować zgodnie z przykładem, a ogólna ogólna procedura jest taka sama, ale interfejs użytkownika i opcje mogą się różnić. Jeśli na przykład używasz łącznika Outlook.com, zamiast tego Zaloguj się przy użyciu osobistego konto Microsoft.

* Aby przetestować przykładową aplikację logiki utworzoną w tym artykule, potrzebne jest narzędzie, które może wysyłać wywołania do wyzwalacza żądania, który jest pierwszym krokiem w przykładowej aplikacji logiki. Jeśli nie masz takiego narzędzia, możesz pobrać, zainstalować i użyć programu [Poster](https://www.postman.com/downloads/).

* Jeśli utworzysz aplikację logiki przy użyciu ustawień, które obsługują używanie [Application Insights](../azure-monitor/app/app-insights-overview.md), możesz opcjonalnie włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas tworzenia aplikacji logiki lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale po utworzeniu aplikacji logiki lub po wdrożeniu można utworzyć ten zasób [z wyprzedzeniem](../azure-monitor/app/create-workspace-resource.md).

## <a name="create-the-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W polu wyszukiwania Azure Portal wpisz `logic app preview` , a następnie wybierz pozycję **aplikacja logiki (wersja zapoznawcza)**.

   ![Zrzut ekranu przedstawiający pole wyszukiwania Azure Portal z wyszukiwanym terminem "wersja zapoznawcza aplikacji logiki" oraz wybranym zasobem "aplikacja logiki (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. Na stronie **aplikacja logiki (wersja zapoznawcza)** wybierz pozycję **Dodaj**.

1. Na stronie **Tworzenie aplikacji logiki (wersja zapoznawcza)** na karcie **podstawowe** podaj te informacje o aplikacji logiki.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Azure — nazwa subskrypcji*> | Subskrypcja platformy Azure do użycia w aplikacji logiki. |
   | **Grupa zasobów** | Tak | <*Azure-Resource-Group-Name*> | Grupa zasobów platformy Azure, w której tworzysz aplikację logiki i powiązane zasoby. Ta nazwa zasobu musi być unikatowa w różnych regionach i może zawierać tylko litery, cyfry, łączniki ( **-** ), podkreślenia (**_**), nawiasy (**()**) i kropki (**.**). <p><p>Ten przykład tworzy grupę zasobów o nazwie `Fabrikam-Workflows-RG` . |
   | **Nazwa aplikacji logiki** | Tak | <*Logic-App-Name*> | Nazwa, która ma być używana w aplikacji logiki. Ta nazwa zasobu musi być unikatowa w różnych regionach i może zawierać tylko litery, cyfry, łączniki ( **-** ), podkreślenia (**_**), nawiasy (**()**) i kropki (**.**). <p><p>W tym przykładzie jest tworzona aplikacja logiki o nazwie `Fabrikam-Workflows` . <p><p>**Uwaga**: Nazwa aplikacji logiki automatycznie pobiera sufiks, `.azurewebsites.net` ponieważ zasób **aplikacji logiki (wersja zapoznawcza)** jest obsługiwany przez Azure Functions, który używa tej samej konwencji nazewnictwa aplikacji. |
   | **Publikowanie** | Tak | <*wdrożenie — środowisko*> | Miejsce docelowe wdrożenia aplikacji logiki. Środowisko można wdrożyć na platformie Azure, wybierając kolejno opcje **przepływ pracy** lub **kontener platformy Docker**. <p><p>W tym przykładzie jest używany **przepływ pracy**, który wdraża zasób **aplikacji logiki (wersja zapoznawcza)** do Azure Portal. <p><p>**Uwaga**: przed wybraniem **kontenera Docker** upewnij się, że utworzono obraz kontenera Docker. Na przykład można utworzyć ten obraz za pomocą [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)lub [wystąpienia kontenera platformy Azure](../container-instances/container-instances-overview.md). Dzięki temu po wybraniu **kontenera Docker** można [określić kontener, który ma być używany w ustawieniach aplikacji logiki](#set-docker-container). |
   | **Region** | Tak | <*Platforma Azure — region*> | Region platformy Azure, który ma być używany podczas tworzenia grupy zasobów i zasobów. <p><p>W tym przykładzie zastosowano **zachodnie stany USA**. |
   |||||

   Oto przykład:

   ![Zrzut ekranu przedstawiający stronę Azure Portal i "Tworzenie aplikacji logiki (wersja zapoznawcza)".](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Następnie na karcie **hosting** podaj te informacje o rozwiązaniu magazynu i planowaniu hostingu do użycia w aplikacji logiki.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Konto magazynu** | Tak | <*Azure-Storage-account-name*> | [Konto usługi Azure Storage](../storage/common/storage-account-overview.md) do użycia w przypadku transakcji magazynu. Ta nazwa zasobu musi być unikatowa w różnych regionach i zawierać 3-24 znaków z tylko cyframi i małymi literami. Wybierz istniejące konto lub Utwórz nowe konto. <p><p>Ten przykład tworzy konto magazynu o nazwie `fabrikamstorageacct` . |
   | **Typ planu** | Tak | <*Azure-hosting — Plan*> | [Plan hostingu](../app-service/overview-hosting-plans.md) służący do wdrażania aplikacji logiki, która jest w wersji [**Premium**](../azure-functions/functions-premium-plan.md) lub [ **plan usługi App Service** (dedykowany)](../azure-functions/dedicated-plan.md). Wybór ma wpływ na możliwości i warstwy cenowe, które są później dostępne dla Ciebie. <p><p>Ten przykład używa **planu usługi App Service**. <p><p>**Uwaga**: podobnie jak w przypadku Azure Functions, typ zasobu **aplikacja logiki (wersja zapoznawcza)** wymaga planu hostingu i warstwy cenowej. Plany zużycia nie są obsługiwane ani niedostępne dla tego typu zasobu. Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami: <p><p>- [Azure Functions skalowanie i hosting](../azure-functions/functions-scale.md) <br>- [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/app-service/) <p><p>Na przykład plan usługi Functions w warstwie Premium zapewnia dostęp do funkcji sieciowych, takich jak łączenie i integrowanie prywatnie z sieciami wirtualnymi platformy Azure, podobnie jak Azure Functions podczas tworzenia i wdrażania aplikacji logiki. Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami: <p><p>- [Opcje sieci Azure Functions](../azure-functions/functions-networking-options.md) <br>- [Azure Logic Apps uruchamianie dowolnych możliwości sieciowych za pomocą wersji zapoznawczej Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Plan systemu Windows** | Tak | <*Nazwa planu*> | Nazwa planu do użycia. Wybierz istniejący plan lub podaj nazwę nowego planu. <p><p>Ten przykład używa nazwy `Fabrikam-Service-Plan` . |
   | **Jednostka SKU i rozmiar** | Tak | <*Cennik — warstwa*> | [Warstwa cenowa](../app-service/overview-hosting-plans.md) używana do hostowania aplikacji logiki. Do wybranych przez siebie typu planu została wybrana wartość. Aby zmienić warstwę domyślną, wybierz pozycję **Zmień rozmiar**. Następnie można wybrać inne warstwy cenowe na podstawie obciążenia, którego potrzebujesz. <p><p>W tym przykładzie zastosowano bezpłatną **warstwę cenową F1** dla obciążeń **deweloperskich/testowych** . Aby uzyskać więcej informacji, zobacz [szczegóły cennika App Service](https://azure.microsoft.com/pricing/details/app-service/). |
   |||||

1. Następnie, jeśli ustawienia tworzenia i wdrażania obsługują używanie [Application Insights](../azure-monitor/app/app-insights-overview.md), można opcjonalnie włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki.

   1. Na karcie **monitorowanie** w obszarze **Application Insights** ustaw opcję **Włącz Application Insights** na **wartość tak** , jeśli nie została jeszcze wybrana.

   1. Dla ustawienia **Application Insights** Wybierz istniejące wystąpienie Application Insights lub jeśli chcesz utworzyć nowe wystąpienie, wybierz pozycję **Utwórz nowy** i podaj nazwę, której chcesz użyć.

1. Po sprawdzeniu przez platformę Azure ustawień aplikacji logiki na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

   Na przykład:

   ![Zrzut ekranu przedstawiający Azure Portal i nowe ustawienia zasobów aplikacji logiki.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Jeśli po wybraniu opcji **Utwórz**, Otwórz i przejrzyj szczegóły błędu, zostanie wyświetlony komunikat o błędzie. Na przykład jeśli wybrany region osiągnie limit przydziału dla zasobów, które próbujesz utworzyć, może być konieczne wypróbowanie innego regionu.

   Po zakończeniu wdrażania na platformie Azure aplikacja logiki jest automatycznie aktywna i uruchomiona, ale nie wykonuje jeszcze żadnych działań, ponieważ nie istnieją żadne przepływy pracy.

1. Na stronie ukończenie wdrożenia wybierz pozycję **Przejdź do zasobu** , aby można było rozpocząć Kompilowanie przepływu pracy. W przypadku wybrania **kontenera Docker** do wdrażania aplikacji logiki wykonaj [kroki, aby podać informacje o tym kontenerze platformy Docker](#set-docker-container).

   ![Zrzut ekranu pokazujący Azure Portal i gotowe wdrożenie.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Określ kontener platformy Docker na potrzeby wdrożenia

Przed rozpoczęciem tych kroków potrzebny jest obraz kontenera Docker. Na przykład można utworzyć ten obraz za pomocą [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)lub [wystąpienia kontenera platformy Azure](../container-instances/container-instances-overview.md). Następnie można podać informacje o kontenerze platformy Docker po utworzeniu aplikacji logiki.

1. W Azure Portal przejdź do zasobu aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **centrum wdrażania**.

1. W okienku **centrum wdrażania** postępuj zgodnie z instrukcjami dotyczącymi udostępniania i zarządzania szczegółami kontenera Docker.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Dodawanie pustego przepływu pracy

1. Gdy na platformie Azure zostanie otwarty zasób, w menu aplikacji logiki wybierz pozycję **przepływy pracy**. Na pasku narzędzi **przepływy pracy** wybierz pozycję **Dodaj**.

   ![Zrzut ekranu pokazujący menu zasobów aplikacji logiki z wybraną opcją "przepływy pracy", a następnie polecenie "Dodaj" na pasku narzędzi.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Po otwarciu okienka **nowego przepływu pracy** Podaj nazwę dla przepływu pracy, a następnie wybierz typ przepływu pracy [ **stanowego** lub **bezstanowego**](logic-apps-overview-preview.md#stateful-stateless) . Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   Ten przykład dodaje pusty przepływ stanowy o nazwie `Fabrikam-Stateful-Workflow` . Domyślnie przepływ pracy jest włączony, ale nie ma nic robić, dopóki wyzwalacz i akcje nie zostaną dodane.

   ![Zrzut ekranu pokazujący nowo dodany pusty przepływ stanowy "Fabrikam-stanowy przepływ pracy".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Następnie Otwórz pusty przepływ pracy w projektancie, aby można było dodać wyzwalacz i akcję.

   1. Z listy przepływów pracy wybierz pusty przepływ pracy.

   1. W menu przepływ pracy w obszarze **deweloper** wybierz pozycję **Projektant**.

      Na powierzchni projektanta zostanie wyświetlony monit **Wybierz operację** , która jest domyślnie zaznaczona, aby okienko **Dodawanie wyzwalacza** było również otwarte.

      ![Zrzut ekranu pokazujący otwarty projektant przepływu pracy z opcją "Wybierz operację" wybraną na powierzchni projektanta.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Dodawanie wyzwalacza i akcji

Ten przykład tworzy przepływ pracy, który ma następujące kroki:

* Wbudowany [wyzwalacz żądania](../connectors/connectors-native-reqres.md), **gdy odebrane zostanie żądanie HTTP**, które odbiera wywołania przychodzące lub żądania i tworzy punkt końcowy, do którego mogą być wywoływane inne usługi lub Aplikacje logiki.

* [Akcja programu Outlook pakietu Office 365](../connectors/connectors-create-api-office365-outlook.md), **Wyślij wiadomość e-mail**.

* Wbudowana [Akcja odpowiedzi](../connectors/connectors-native-reqres.md)służąca do wysyłania odpowiedzi i powrotu danych z powrotem do obiektu wywołującego.

### <a name="add-the-request-trigger"></a>Dodawanie wyzwalacza żądania

Aby można było dodać wyzwalacz do pustego przepływu pracy, należy się upewnić, że projektant przepływu pracy jest otwarty oraz że na powierzchni projektanta wybrano monit **Wybieranie operacji** .

1. Obok powierzchni projektanta, w okienku **Dodawanie wyzwalacza** , w polu wyszukiwania **Wybierz operację** Sprawdź, czy wybrana jest karta **wbudowana** . Na tej karcie przedstawiono wyzwalacze uruchamiane natywnie w Azure Logic Apps.

1. W polu wyszukiwania **Wybierz operację** wprowadź `when a http request` , a następnie wybierz wbudowany wyzwalacz żądania o nazwie, **gdy odebrane zostanie żądanie HTTP**.

   ![Zrzut ekranu, który pokazuje projektanta i * * Dodaj okienko wyzwalacz * * z wybranym wyzwalaczem "po odebraniu żądania HTTP".](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Gdy wyzwalacz pojawi się w projektancie, zostanie otwarte okienko Szczegóły wyzwalacza, aby wyświetlić właściwości wyzwalacza, ustawienia i inne akcje.

   ![Zrzut ekranu przedstawiający projektanta z wybranym wyzwalaczem "po odebraniu żądania HTTP" i otwartym okienku szczegółów wyzwalacza.](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Jeśli okienko szczegółów nie jest wyświetlane, upewnij się, że wyzwalacz został wybrany w projektancie.

1. Jeśli musisz usunąć element z projektanta, [wykonaj następujące kroki w celu usunięcia elementów z projektanta](#delete-from-designer).

1. Aby zapisać swoją służbę, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Po zapisaniu przepływu pracy po raz pierwszy, gdy przepływ pracy rozpoczyna się od wyzwalacza żądania, usługa Logic Apps automatycznie generuje adres URL dla punktu końcowego, który jest tworzony przez wyzwalacz żądania. Później podczas testowania przepływu pracy wysyłamy żądanie do tego adresu URL, który uruchamia wyzwalacz i uruchamia przebieg przepływu pracy.

### <a name="add-the-office-365-outlook-action"></a>Dodawanie akcji programu Outlook pakietu Office 365

1. W projektancie w obszarze dodany wyzwalacz wybierz pozycję **nowy krok**.

   Zostanie wyświetlony monit **Wybierz operację** w projektancie, a okienko **Dodawanie akcji** zostanie otwarte ponownie, aby można było wybrać następną akcję.

   > [!NOTE]
   > Jeśli w okienku **Dodaj akcję** zostanie wyświetlony komunikat o błędzie "nie można odczytać właściwości" filtr "z niezdefiniowanej", zapisać przepływ pracy, załadować ponownie strony, ponownie otworzyć przepływ pracy i spróbować ponownie.

1. W okienku **Dodaj akcję** w polu wyszukiwania **Wybierz operację** wybierz pozycję **Azure**. Na tej karcie są wyświetlane łączniki zarządzane, które są dostępne i wdrożone na platformie Azure.

   > [!NOTE]
   > Jeśli w okienku **Dodaj akcję** zostanie wyświetlony komunikat o błędzie, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` Zapisz swój przepływ pracy, Załaduj ponownie stronę, Otwórz ponownie przepływ pracy i ponów próbę dodania akcji.

   W tym przykładzie zostanie użyta akcja programu Outlook pakietu Office 365 o nazwie **Wyślij wiadomość e-mail (wersja 2)**.

   ![Zrzut ekranu, który pokazuje projektanta i * * Dodaj akcję * * z wybraną akcją Office 365 Outlook "Wyślij wiadomość e-mail".](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. W okienku szczegółów akcji na karcie **Tworzenie połączenia** wybierz pozycję **Zaloguj** , aby utworzyć połączenie z kontem e-mail.

   ![Zrzut ekranu, który pokazuje projektanta i okienko szczegółów "Wyślij wiadomość e-mail (v2)" z wybraną pozycją "Zaloguj się".](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. Po wyświetleniu monitu o zgodę na dostęp do Twojego konta e-mail Zaloguj się przy użyciu poświadczeń konta.

   > [!NOTE]
   > Jeśli zostanie wyświetlony komunikat o błędzie, `Failed with error: 'The browser is closed.'. Please sign in again` Sprawdź, czy przeglądarka blokuje pliki cookie innych firm. Jeśli te pliki cookie są blokowane, spróbuj dodać `https://portal.azure.com` do listy witryn, które mogą używać plików cookie. Jeśli używasz trybu incognito, upewnij się, że pliki cookie innych firm nie są blokowane podczas pracy w tym trybie.
   > 
   > W razie potrzeby Załaduj ponownie stronę, Otwórz przepływ pracy, ponownie Dodaj akcję poczty e-mail i spróbuj utworzyć połączenie.

   Po utworzeniu połączenia przez platformę Azure w projektancie zostanie wyświetlona Akcja **Wyślij wiadomość e-mail** , która jest domyślnie zaznaczona. Jeśli akcja nie jest zaznaczona, zaznacz akcję tak, aby okienko szczegółów było również otwarte.

1. W okienku szczegółów akcji na karcie **Parametry** podaj wymagane informacje dotyczące akcji, na przykład:

   ![Zrzut ekranu, który pokazuje projektanta i okienko szczegółów "Wyślij wiadomość e-mail" z wybraną kartą "parametry".](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Do** | Tak | <*adres e-mail użytkownika*> | Odbiorca wiadomości e-mail, który może być Twoim adresem e-mail do celów testowych. Ten przykład używa fikcyjnej poczty e-mail `sophiaowen@fabrikam.com` . |
   | **Temat** | Tak | `An email from your example workflow` | Temat wiadomości e-mail |
   | **Treść** | Tak | `Hello from your example workflow!` | Zawartość wiadomości e-mail |
   ||||

   > [!NOTE]
   > Podczas wprowadzania zmian w okienku szczegółów na stronie **Ustawienia**, **wynik statyczny** lub **Uruchom po** kartach upewnij się, że wybrano pozycję **gotowe** , aby zatwierdzić te zmiany przed przełączeniem kart lub zmianę fokusu projektanta. W przeciwnym razie Projektant nie będzie mógł zachować wprowadzonych zmian.

1. Zapisz pracę. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Następnie, aby przetestować przepływ pracy, ręcznie Wyzwól uruchomienie.

## <a name="trigger-the-workflow"></a>Wyzwalanie przepływu pracy

W tym przykładzie przepływ pracy jest uruchamiany, gdy wyzwalacz żądania odbiera żądanie przychodzące, które jest wysyłane do adresu URL dla punktu końcowego tworzonego przez wyzwalacz. Po zapisaniu przepływu pracy po raz pierwszy usługa Logic Apps automatycznie wygenerowała ten adres URL. Dlatego przed wysłaniem tego żądania do wyzwolenia przepływu pracy należy znaleźć ten adres URL.

1. W Projektancie przepływu pracy wybierz wyzwalacz żądania, który jest nazwany **podczas odbierania żądania HTTP**.

1. Po otwarciu okienka szczegółów na karcie **Parametry** Znajdź właściwość **http post adresu URL** . Aby skopiować wygenerowany adres URL, wybierz **adres URL kopiowania** (ikona Kopiuj plik) i Zapisz adres URL w innym miejscu. Adres URL jest następujący:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Zrzut ekranu, który pokazuje projektanta z wyzwalaczem żądania i adresem URL punktu końcowego w właściwości "HTTP POST URL".](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   W tym przykładzie adres URL wygląda następująco:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Adres URL punktu końcowego można również znaleźć w okienku **Przegląd** aplikacji logiki we właściwości **adres URL przepływu pracy** .
   >
   > 1. W menu zasób wybierz pozycję **Przegląd**.
   > 1. W okienku **Przegląd** Znajdź właściwość **adres URL przepływu pracy** .
   > 1. Aby skopiować adres URL punktu końcowego, przesuń wskaźnik myszy na koniec tekstu adresu URL punktu końcowego, a następnie wybierz opcję **Kopiuj do schowka** (ikona Kopiuj plik).

1. Aby przetestować adres URL, wysyłając żądanie, Otwórz program [Poster](https://www.postman.com/downloads/) lub preferowane narzędzie do tworzenia i wysyłania żądań.

   Ten przykład jest kontynuowany przy użyciu programu Poster. Aby uzyskać więcej informacji, zobacz temat [poster wprowadzenie](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na pasku narzędzi programu Poster wybierz pozycję **Nowy**.

      ![Zrzut ekranu pokazujący, że wybrano nowy przycisk.](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. W okienku **Utwórz nowe** w obszarze **bloki konstrukcyjne** wybierz pozycję **żądanie**.

   1. W oknie **Zapisywanie żądania** w obszarze **Nazwa żądania** Podaj nazwę żądania, na przykład `Test workflow trigger` .

   1. W obszarze **Wybierz kolekcję lub folder, w którym chcesz zapisać**, wybierz pozycję **Utwórz kolekcję**.

   1. W obszarze **wszystkie kolekcje** Podaj nazwę kolekcji, która ma zostać utworzona do organizowania żądań, naciśnij klawisz ENTER, a następnie wybierz pozycję **zapisz, aby <*nazwę* > kolekcji**. Ten przykład używa `Logic Apps requests` jako nazwy kolekcji.

      Zostanie otwarte okienko żądania programu post, dzięki któremu można wysłać żądanie do adresu URL punktu końcowego dla wyzwalacza żądania.

      ![Zrzut ekranu przedstawiający program ogłaszający przy otwartym okienku żądania](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. W okienku żądanie w polu adres obok listy metod, która obecnie **jest wyświetlana jako** domyślna metoda żądania, wklej wcześniej skopiowany adres URL, a następnie wybierz pozycję **Wyślij**.

      ![Zrzut ekranu, na którym jest wyświetlany adres URL post i punkt końcowy w polu adres z wybranym przyciskiem Wyślij](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Po uruchomieniu wyzwalacza, przykładowy przepływ pracy i wysyła wiadomość e-mail, która wygląda podobnie do tego przykładu:

      ![Zrzut ekranu przedstawiający wiadomości e-mail w programie Outlook zgodnie z opisem w przykładzie](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

W przypadku przepływu pracy stanowej po każdym uruchomieniu przepływu pracy można wyświetlić historię uruchamiania, w tym stan ogólnego przebiegu, dla wyzwalacza oraz dla każdej akcji wraz z danymi wejściowymi i wyjściowymi. W Azure Portal, historia uruchamiania i historie wyzwalacza są wyświetlane na poziomie przepływu pracy, a nie na poziomie aplikacji logiki. Aby przejrzeć historie wyzwalacza poza kontekstem uruchamiania historii, zobacz [Przegląd historii wyzwalaczy](#view-trigger-histories).

1. W Azure Portal w menu przepływu pracy wybierz pozycję **Monitoruj**.

   Okienko **monitorowanie** zawiera historię uruchamiania dla tego przepływu pracy.

   ![Zrzut ekranu przedstawiający okienko "Monitor" przepływu pracy i historię uruchomienia.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Jeśli najnowszy stan uruchomienia nie zostanie wyświetlony, na pasku narzędzi okienka **monitorowanie** wybierz pozycję **Odśwież**. Nie ma żadnego przebiegu dla wyzwalacza, który został pominięty z powodu niewypełnienia kryteriów lub wyszukiwania brakujących danych.

   | Stan uruchomienia | Opis |
   |------------|-------------|
   | **Zostało przerwane** | Przebieg został zatrzymany lub nie został zakończony z powodu problemów zewnętrznych, na przykład awarii systemu lub subskrypcji platformy Azure. |
   | **Zerwan** | Uruchomienie zostało wyzwolone i uruchomione, ale Odebrano żądanie anulowania. |
   | **Niepowodzenie** | Co najmniej jedna akcja w przebiegu nie powiodła się. Nie skonfigurowano żadnych kolejnych akcji w przepływie pracy w celu obsłużenia błędu. |
   | **Uruchomienie** | Uruchomienie zostało wyzwolone i jest w toku, ale ten stan może również pojawić się dla przebiegu, który jest ograniczany ze względu na [limity akcji](logic-apps-limits-and-config.md) lub [bieżący plan cenowy](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Porada**: w przypadku skonfigurowania [rejestrowania diagnostycznego](monitor-logic-apps-log-analytics.md)można uzyskać informacje o wszelkich zdarzeniach związanych z ograniczaniem. |
   | **Powodzenie** | Przebieg zakończył się pomyślnie. Jeśli jakakolwiek akcja zakończyła się niepowodzeniem, kolejna Akcja w przepływie pracy obsłuży ten błąd. |
   | **Przekroczono limit czasu** | Przekroczono limit czasu uruchamiania, ponieważ bieżący czas trwania przekracza limit czasu trwania przebiegu, który jest kontrolowany przez [ustawienie **przechowywania historii uruchamiania w dniach**](logic-apps-limits-and-config.md#run-duration-retention-limits). Czas trwania przebiegu jest obliczany przy użyciu limitu czasu rozpoczęcia i czas trwania uruchomienia w tym czasie. <p><p>**Uwaga**: Jeśli czas trwania przebiegu przekracza *Limit przechowywania bieżącej historii przebiegów*, który jest również kontrolowany przez ustawienie trwa [ **przechowywanie historii uruchamiania w dniach**](logic-apps-limits-and-config.md#run-duration-retention-limits), uruchomienie jest usuwane z historii uruchamiania przez codzienne zadanie oczyszczania. Bez względu na to, czy czas trwania lub zakończeniu przebiegu, okres przechowywania jest zawsze obliczany przy użyciu czasu rozpoczęcia i *bieżącego* limitu przechowywania. W związku z tym, jeśli skracasz limit czasu trwania dla uruchomienia w locie, przekroczenie limitu czasu przebiegu. Jednak przebieg jest wyświetlany lub zostaje usunięty z historii uruchamiania w zależności od tego, czy czas trwania przebiegu przekroczył limit przechowywania. |
   | **Oczekiwanie** | Uruchomienie nie zostało uruchomione lub zostało wstrzymane, na przykład z powodu wcześniejszego wystąpienia przepływu pracy, które nadal działa. |
   |||

1. Aby sprawdzić stan każdego kroku w przebiegu, wybierz przebieg, który chcesz przejrzeć.

   Zostanie otwarty widok Szczegóły uruchamiania i zostanie wyświetlony stan każdego kroku w przebiegu.

   ![Zrzut ekranu pokazujący widok Szczegóły uruchamiania ze stanem każdego kroku w przepływie pracy.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   Oto możliwe stany, które każdy krok w przepływie pracy może:

   | Stan akcji | Ikona | Opis |
   |---------------|------|-------------|
   | **Zostało przerwane** | ![Ikona stanu akcji "przerwane"][aborted-icon] | Akcja została zatrzymana lub nie została zakończona z powodu problemów zewnętrznych, na przykład awarii systemu lub subskrypcji platformy Azure. |
   | **Zerwan** | ![Ikona stanu akcji "anulowana"][cancelled-icon] | Akcja była uruchomiona, ale otrzymała żądanie anulowania. |
   | **Niepowodzenie** | ![Ikona stanu akcji "Niepowodzenie"][failed-icon] | Akcja nie powiodła się. |
   | **Uruchomienie** | ![Ikona stanu akcji "uruchomiona"][running-icon] | Akcja jest obecnie uruchomiona. |
   | **Pominięto** | ![Ikona stanu akcji "pominięto"][skipped-icon] | Akcja została pominięta, ponieważ Poprzednia akcja nie powiodła się. Akcja ma `runAfter` warunek, który wymaga, aby poprzednia akcja została zakończona pomyślnie, zanim będzie można uruchomić bieżącą akcję. |
   | **Powodzenie** | ![Ikona stanu akcji "powodzenie"][succeeded-icon] | Akcja zakończyła się pomyślnie. |
   | **Powodzenie z ponownymi próbami** | ![Ikona stanu akcji "powodzenie z ponownymi próbami"][succeeded-with-retries-icon] | Akcja zakończyła się powodzeniem, ale tylko po wykonaniu jednej lub kilku ponownych prób. Aby przejrzeć historię ponownych prób, w widoku szczegółów historii uruchamiania wybierz tę akcję, aby wyświetlić dane wejściowe i wyjściowe. |
   | **Przekroczono limit czasu** | ![Ikona stanu akcji "Przekroczono limit czasu"][timed-out-icon] | Akcja została zatrzymana z powodu przekroczenia limitu czasu określonego przez ustawienia tej akcji. |
   | **Oczekiwanie** | ![Ikona stanu akcji "oczekiwanie"][waiting-icon] | Dotyczy akcji elementu webhook, która oczekuje na żądanie przychodzące od wywołującego. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Aby przejrzeć dane wejściowe i wyjściowe dla określonego kroku, wybierz ten krok.

   ![Zrzut ekranu pokazujący wejścia i wyjścia w wybranej akcji "Wyślij wiadomość e-mail".](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Aby dodatkowo przejrzeć nieprzetworzone dane wejściowe i wyjściowe dla tego kroku, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe** lub **Pokaż nieprzetworzone wyjścia**.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Przejrzyj historie wyzwalacza

Dla przepływu pracy stanowej można przejrzeć historię wyzwalacza dla każdego przebiegu, łącznie ze stanem wyzwalacza wraz z danymi wejściowymi i wyjściowymi niezależnie od [kontekstu historii uruchamiania](#view-run-history). W Azure Portal historia wyzwalacza i historia uruchamiania są wyświetlane na poziomie przepływu pracy, a nie na poziomie aplikacji logiki. Aby znaleźć te dane historyczne, wykonaj następujące kroki:

1. W Azure Portal w menu przepływu pracy w obszarze **deweloper** wybierz pozycję **Wyzwalaj historie**.

   W okienku **historie wyzwalaczy** wyświetlane są historie wyzwalania dla przebiegów przepływu pracy.

1. Aby przejrzeć określoną historię wyzwalacza, wybierz identyfikator tego uruchomienia.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Włącz lub Otwórz Application Insights po wdrożeniu

Podczas wykonywania przepływu pracy aplikacja logiki emituje dane telemetryczne wraz z innymi zdarzeniami. Za pomocą tych danych telemetrycznych można uzyskać lepszy wgląd w działanie przepływu pracy oraz sposób działania Logic Apps środowiska uruchomieniowego na różne sposoby. Przepływ pracy można monitorować przy użyciu [Application Insights](../azure-monitor/app/app-insights-overview.md), który zapewnia niemal w czasie rzeczywistym dane telemetryczne (metryki na żywo). Ta funkcja ułatwia badanie błędów i problemów z wydajnością w łatwiejszy sposób w przypadku używania tych danych do diagnozowania problemów, konfigurowania alertów i tworzenia wykresów.

Jeśli ustawienia tworzenia i wdrażania aplikacji logiki obsługują używanie [Application Insights](../azure-monitor/app/app-insights-overview.md), można opcjonalnie włączyć rejestrowanie diagnostyczne i śledzenie dla aplikacji logiki. Można to zrobić podczas tworzenia aplikacji logiki w Azure Portal lub po wdrożeniu. Musisz mieć wystąpienie Application Insights, ale po utworzeniu aplikacji logiki lub po wdrożeniu można utworzyć ten zasób [z wyprzedzeniem](../azure-monitor/app/create-workspace-resource.md).

Aby włączyć Application Insights w wdrożonej aplikacji logiki lub otworzyć pulpit nawigacyjny Application Insights, jeśli jest już włączony, wykonaj następujące kroki:

1. W Azure Portal Znajdź wdrożoną aplikację logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Application Insights**.

1. Jeśli Application Insights nie jest włączona, w okienku **Application Insights** wybierz pozycję **Włącz Application Insights**. Po zaktualizowaniu okienka w dolnej części wybierz pozycję **Zastosuj**.

   Jeśli Application Insights jest włączona, w okienku **Application Insights** wybierz pozycję **Wyświetl Application Insights dane**.

Po otwarciu Application Insights można przejrzeć różne metryki dla aplikacji logiki. Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami:

* [Azure Logic Apps uruchamianie w dowolnym miejscu i monitorze z Application Insights — część 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps uruchamianie w dowolnym miejscu i monitorze z Application Insights — część 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Włącz historię uruchamiania dla bezstanowych przepływów pracy

Aby ułatwić debugowanie bezstanowego przepływu pracy, można włączyć historię uruchamiania dla tego przepływu pracy, a następnie wyłączyć historię uruchamiania po zakończeniu. Wykonaj następujące kroki dla Azure Portal lub jeśli pracujesz w Visual Studio Code, zobacz [Tworzenie stanowych przepływów pracy i bezstanowych w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz zasób **aplikacji logiki (wersja zapoznawcza)** .

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Konfiguracja**.

1. Na karcie **Ustawienia aplikacji** wybierz pozycję **nowe ustawienie aplikacji**.

1. W okienku **Dodawanie/Edytowanie ustawienia aplikacji** w polu **Nazwa** wprowadź tę nazwę opcji operacji: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. W polu **wartość** Wprowadź następującą wartość: `WithStatelessRunHistory`

   Na przykład:

   ![Zrzut ekranu pokazujący zasób Azure Portal i Logic App (wersja zapoznawcza) z otwartym okienkiem "Konfiguracja" > "nowe ustawienie aplikacji" < "Dodaj/Edytuj ustawienie aplikacji" i "przepływy pracy". {yourWorkflowName}. Opcja "OperationOptions" ma wartość "WithStatelessRunHistory".](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Aby zakończyć to zadanie, wybierz **przycisk OK**. Na pasku narzędzi okienka **konfiguracji** wybierz pozycję **Zapisz**.

1. Aby wyłączyć historię uruchamiania po zakończeniu, należy ustawić `Workflows.{yourWorkflowName}.OperationOptions` Właściwość na `None` lub usunąć właściwość i jej wartość.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Usuwanie elementów z projektanta

Aby usunąć element z projektanta, wykonaj jedną z następujących czynności:

* Zaznacz element, otwórz menu skrótów elementu (Shift + F10) i wybierz polecenie **Usuń**. Aby potwierdzić, wybierz pozycję **OK**.

* Wybierz element, a następnie naciśnij klawisz Delete. Aby potwierdzić, wybierz pozycję **OK**.

* Wybierz element, aby okienko szczegółów było otwierane dla tego elementu. W prawym górnym rogu okienka Otwórz menu wielokropka (**...**), a następnie wybierz pozycję **Usuń**. Aby potwierdzić, wybierz pozycję **OK**.

  ![Zrzut ekranu pokazujący wybrany element w Projektancie z otwartym okienkiem szczegółów oraz poleceniem "Usuń" z wybranymi elipsami.](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Jeśli menu wielokropka nie jest widoczne, rozwiń odpowiednie okno przeglądarki, aby okienko szczegółów pokazywało przycisk wielokropka (**...**) w prawym górnym rogu.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Rozwiązywanie problemów i błędów

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>W selektorze projektanta brakuje nowych wyzwalaczy i akcji dla wcześniej utworzonych przepływów pracy

Wersja zapoznawcza Azure Logic Apps obsługuje wbudowane akcje dla operacji funkcji platformy Azure, operacji związanych z operacjami ciekłymi i operacjami XML, takich jak **Sprawdzanie poprawności kodu XML** i **przekształcanie XML**. Jednak w przypadku wcześniej utworzonych aplikacji logiki te akcje mogą nie być wyświetlane w projektancie, aby wybrać, czy aplikacja logiki korzysta z nieaktualnej wersji pakietu rozszerzenia `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Aby rozwiązać ten problem, wykonaj następujące kroki, aby usunąć nieaktualną wersję, dzięki czemu pakiet rozszerzeń będzie można automatycznie zaktualizować do najnowszej wersji.

> [!NOTE]
> To konkretne rozwiązanie dotyczy tylko zasobów **aplikacji logiki (wersja zapoznawcza)** utworzonych przy użyciu Azure Portal, a nie aplikacji logiki tworzonych i wdrażanych przy użyciu Visual Studio Code i rozszerzenia Azure Logic Apps (wersja zapoznawcza). Zobacz [obsługiwane wyzwalacze i akcje w projektancie w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. W Azure Portal Zatrzymaj aplikację logiki.

   1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

   1. Na pasku narzędzi okienka **Przegląd** wybierz pozycję **Zatrzymaj**.

1. W menu aplikacji logiki w obszarze **Narzędzia programistyczne** wybierz pozycję **Narzędzia zaawansowane**.

1. W okienku **Narzędzia zaawansowane** wybierz pozycję **Przejdź**, co spowoduje otwarcie środowiska kudu dla aplikacji logiki.

1. Na pasku narzędzi kudu Otwórz menu **konsoli debugowania** , a następnie wybierz polecenie **cmd**. 

   Zostanie otwarte okno konsoli, aby można było przejść do folderu pakietu przy użyciu wiersza polecenia. Lub można przeglądać strukturę katalogów, która pojawia się w oknie konsoli.

1. Przejdź do następującego folderu, który zawiera foldery z wersjami dla istniejącego pakietu:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Usuń folder wersji istniejącego pakietu. W oknie konsoli można uruchomić to polecenie, w którym zastąpią `{bundle-version}` istniejącą wersję:

   `rm -rf {bundle-version}`

   Na przykład: `rm -rf 1.1.3`

   > [!TIP]
   > Jeśli zostanie wyświetlony błąd, taki jak "odmowa uprawnień" lub "plik w użyciu", Odśwież stronę w przeglądarce i spróbuj ponownie wykonać poprzednie kroki do momentu usunięcia folderu.

1. W Azure Portal Wróć na stronę **omówienia** aplikacji logiki, a następnie wybierz pozycję **Uruchom ponownie**.

   Portal automatycznie pobiera i używa najnowszego pakietu.

## <a name="next-steps"></a>Następne kroki

Chcemy poznać Twoją opinię dotyczącą Twoich doświadczeń dzięki tej publicznej wersji zapoznawczej.

* W przypadku usterek lub problemów [Utwórz swoje problemy w serwisie GitHub](https://github.com/Azure/logicapps/issues).
* Aby uzyskać odpowiedzi na pytania, żądania, komentarze i inne opinie, [Skorzystaj z tej formy opinii](https://aka.ms/lafeedback).
