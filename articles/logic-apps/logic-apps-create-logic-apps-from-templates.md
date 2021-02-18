---
title: Szybsze tworzenie przepływów pracy aplikacji logiki przy użyciu wbudowanych szablonów
description: Szybko Twórz przepływy pracy aplikacji logiki przy użyciu wstępnie utworzonych szablonów dostarczonych przez Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 42c592e6aede4537dc983fd2cff043a878f81f1c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593099"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Tworzenie przepływów pracy aplikacji logiki na podstawie wbudowanych szablonów

Aby szybciej rozpocząć tworzenie przepływów pracy, Logic Apps udostępnia szablony, które są wstępnie skompilowanymi aplikacjami logiki, które są zgodne z powszechnie używanymi wzorcami. Użyj tych szablonów jako dostarczonych lub edytuj je w celu dopasowania do Twojego scenariusza.

Oto kilka kategorii szablonów:

| Typ szablonu | Opis | 
| ------------- | ----------- | 
| Szablony chmury przedsiębiorstwa | Do integracji obiektów blob platformy Azure, Dynamics CRM, Salesforce, Box i zawiera inne łączniki dla potrzeb chmury przedsiębiorstwa. Można na przykład użyć tych szablonów do organizowania potencjalnych klientów lub tworzenia kopii zapasowej danych plików firmy. | 
| Osobiste szablony produktywności | Zwiększ produktywność osobistą przez ustawienie codziennych przypomnień, przekształcenie ważnych elementów roboczych na listy zadań do wykonania i zautomatyzowanie długich zadań w dół do jednego etapu zatwierdzania przez użytkownika. | 
| Szablony chmury konsumenckiej | Do integrowania usług mediów społecznościowych, takich jak Twitter, zapasy i poczta e-mail. Przydatne w przypadku wzmacniania inicjatyw marketingowych mediów społecznościowych. Szablony te obejmują również zadania, takie jak kopiowanie w chmurze, które zwiększają produktywność dzięki zapisywaniu czasu na tradycyjnie powtarzające się zadania. | 
| Szablony pakietu integracyjnego dla przedsiębiorstw | Do konfigurowania potoków VETER (walidacji, wyodrębniania, przekształcania, wzbogacania, tras), otrzymywania dokumentu X12 EDI przez AS2 i przekształcania w formacie XML oraz obsługi komunikatów X12, EDIFACT i AS2. | 
| Szablony wzorców protokołów | Do implementowania wzorców protokołów, takich jak żądanie-odpowiedź za pośrednictwem protokołu HTTP i integracji w ramach protokołu FTP i SFTP. Użyj tych szablonów jako dostarczonych lub skompiluj je na potrzeby złożonych wzorców protokołów. | 
||| 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Tworzenie aplikacji logiki przy użyciu szablonów

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do [Azure Portal](https://portal.azure.com "Azure Portal").

2. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób**  >  **integracja dla przedsiębiorstw**  >  **aplikacji logiki**.

   ![Azure Portal, Nowy, Integracja w przedsiębiorstwie, Aplikacja logiki](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Utwórz własną aplikację logiki z ustawieniami podanymi w tabeli pod tą ilustracją:

   ![Podawanie szczegółów aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | *Twoja nazwa aplikacji logiki* | Podaj unikatową nazwę aplikacji logiki. | 
   | **Subskrypcja** | *nazwa_Twojej_susbkrypcji_Azure* | Wybierz subskrypcję platformy Azure, której chcesz użyć. | 
   | **Grupa zasobów** | *nazwa_Twojej_grupy_zasobów_Azure* | Utwórz lub wybierz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) dla tej aplikacji logiki i zorganizuj wszystkie zasoby skojarzone z tą aplikacją. | 
   | **Lokalizacja** | *region_Twojego_centrum_danych_Azure* | Wybierz region centrum danych do wdrożenia swojej aplikacji logiki, na przykład Zachodnie stany USA. | 
   | **Log Analytics** | **Wyłączone** (domyślnie) lub **na** | Skonfiguruj [rejestrowanie diagnostyczne](../logic-apps/monitor-logic-apps-log-analytics.md) dla aplikacji logiki przy użyciu [dzienników Azure monitor](../azure-monitor/logs/log-query-overview.md). Wymaga, aby masz już obszar roboczy Log Analytics. | 
   |||| 

4. Gdy wszystko będzie gotowe, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**. Dzięki temu aplikacja logiki automatycznie pojawi się na pulpicie nawigacyjnym platformy Azure i zostanie otwarta po wdrożeniu. Wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Jeśli nie chcesz przypinać aplikacji logiki, to aby można było kontynuować, musisz ją ręcznie znaleźć i otworzyć po wdrożeniu.

   Gdy platforma Azure wdroży aplikację logiki, zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo. 
   Pod wideo znajdziesz szablony typowych wzorców aplikacji logiki. 

5. Przewiń zawartość wideo z wprowadzeniem i typowe wyzwalacze do **szablonów**. Wybierz wstępnie utworzony szablon. Na przykład:

   ![Wybieranie szablonu aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Aby utworzyć aplikację logiki od podstaw, wybierz pozycję **pusta aplikacja logiki**.

   Po wybraniu prekompilowanego szablonu można wyświetlić więcej informacji na temat tego szablonu. 
   Na przykład:

   ![Wybieranie wstępnie skompilowanego szablonu](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Aby kontynuować wybrany szablon, wybierz opcję **Użyj tego szablonu**. 

7. Na podstawie łączników w szablonie zostanie wyświetlony monit o wykonanie jednej z następujących czynności:

   * Zaloguj się przy użyciu swoich poświadczeń do systemów lub usług, do których odwołuje się szablon.

   * Utwórz połączenia dla dowolnych usług lub systemów, do których odwołuje się szablon. Aby utworzyć połączenie, podaj nazwę połączenia i w razie potrzeby wybierz zasób, którego chcesz użyć. 

   * Jeśli te połączenia zostały już skonfigurowane, wybierz pozycję **Kontynuuj**.

   Na przykład:

   ![Tworzenie połączeń](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Gdy skończysz, aplikacja logiki zostanie otwarta i będzie wyświetlana w projektancie Logic Apps.

   > [!TIP]
   > Aby powrócić do przeglądarki szablonów, wybierz pozycję **Szablony** na pasku narzędzi projektanta. Ta akcja odrzuca wszystkie niezapisane zmiany, dlatego komunikat ostrzegawczy informujący o konieczności potwierdzenia Twojego żądania.

8. Kontynuuj Kompilowanie aplikacji logiki.

   > [!NOTE] 
   > Wiele szablonów zawiera łączniki, które mogą już wstępnie wypełnić wymagane właściwości. Jednak niektóre szablony mogą nadal wymagać podania wartości, aby można było poprawnie wdrożyć aplikację logiki. Jeśli spróbujesz wdrożyć bez wykonywania pól właściwości brakujące, zostanie wyświetlony komunikat o błędzie. 

## <a name="update-logic-apps-with-templates"></a>Aktualizowanie aplikacji logiki za pomocą szablonów

1. W [Azure Portal](https://portal.azure.com "Azure Portal")Znajdź i Otwórz aplikację logiki w programie Logic App Designer.

2. Na pasku narzędzi projektanta wybierz pozycję **Szablony**. Ta akcja odrzuca wszystkie niezapisane zmiany, dlatego zostanie wyświetlony komunikat ostrzegawczy, aby potwierdzić, że chcesz kontynuować. Aby potwierdzić, wybierz **przycisk OK**. Na przykład:

   ![Wybieranie "szablonów"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Przewiń zawartość wideo z wprowadzeniem i typowe wyzwalacze do **szablonów**. Wybierz wstępnie utworzony szablon. Na przykład:

   ![Wybieranie szablonu aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Po wybraniu prekompilowanego szablonu można wyświetlić więcej informacji na temat tego szablonu. 
   Na przykład:

   ![Wybieranie wstępnie skompilowanego szablonu](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Aby kontynuować wybrany szablon, wybierz opcję **Użyj tego szablonu**. 

5. Na podstawie łączników w szablonie zostanie wyświetlony monit o wykonanie jednej z następujących czynności:

   * Zaloguj się przy użyciu swoich poświadczeń do systemów lub usług, do których odwołuje się szablon.

   * Utwórz połączenia dla dowolnych usług lub systemów, do których odwołuje się szablon. Aby utworzyć połączenie, podaj nazwę połączenia i w razie potrzeby wybierz zasób, którego chcesz użyć. 

   * Jeśli te połączenia zostały już skonfigurowane, wybierz pozycję **Kontynuuj**.

   ![Tworzenie połączeń](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Aplikacja logiki zostanie teraz otwarta i pojawi się w projektancie Logic Apps.

8. Kontynuuj Kompilowanie aplikacji logiki. 

   > [!TIP]
   > Jeśli zmiany nie zostały zapisane, możesz odrzucić swoją służbę i wrócić do poprzedniej aplikacji logiki. Na pasku narzędzi projektanta wybierz pozycję **Odrzuć**.

> [!NOTE] 
> Wiele szablonów zawiera łączniki, które mogą już wstępnie wypełnić wymagane właściwości. Jednak niektóre szablony mogą nadal wymagać podania wartości, aby można było poprawnie wdrożyć aplikację logiki. Jeśli spróbujesz wdrożyć bez wykonywania pól właściwości brakujące, zostanie wyświetlony komunikat o błędzie.

## <a name="deploy-logic-apps-built-from-templates"></a>Wdróż Aplikacje logiki skompilowane z szablonów

Po wprowadzeniu zmian do szablonu można zapisać zmiany. Ta akcja powoduje również automatyczne opublikowanie aplikacji logiki.

Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

![Zapisywanie i publikowanie aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat tworzenia aplikacji logiki poprzez przykłady, scenariusze, historie klientów i instruktaże.

> [!div class="nextstepaction"]
> [Przejrzyj przykłady, scenariusze i przewodniki dotyczące aplikacji logiki](../logic-apps/logic-apps-examples-and-scenarios.md)
