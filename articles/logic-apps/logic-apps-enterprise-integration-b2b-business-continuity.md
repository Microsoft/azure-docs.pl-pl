---
title: Odzyskiwanie po awarii dla kont integracji
description: Skonfiguruj konta integracji i artefakty B2B z odzyskiwaniem po awarii między regionami w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 1e006dd690e9c008afada8a490da6c3238bc0791
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565415"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Skonfiguruj odzyskiwanie po awarii między regionami dla kont integracji w programie Azure Logic Apps

Obciążenia B2B obejmują transakcje pieniężne, takie jak zamówienia i faktury. W przypadku awarii, niezwykle ważne jest, aby zapewnić firmie szybkie odzyskiwanie, aby zaspokoić umowy SLA na poziomie firmy uzgodnione z partnerami. W tym artykule przedstawiono sposób tworzenia planu ciągłości biznesowej dla obciążeń B2B. 

* Gotowość do odzyskiwania po awarii 
* Przechodzenie w tryb failover do regionu pomocniczego podczas zdarzenia awarii 
* Wróć do regionu podstawowego po wystąpieniu awarii

## <a name="disaster-recovery-readiness"></a>Gotowość do odzyskiwania po awarii  

1. Określ region pomocniczy i Utwórz [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) w regionie pomocniczym.

2. Dodawanie partnerów, schematów i umów dotyczących wymaganych przepływów komunikatów, w których stan uruchomienia musi być replikowany do konta integracji regionu pomocniczego.

   > [!TIP]
   > Upewnij się, że istnieje spójność konwencji nazewnictwa artefaktu konta integracji w różnych regionach. 

3. Aby ściągnąć stan uruchomienia z regionu podstawowego, należy utworzyć aplikację logiki w regionie pomocniczym. 

   Ta aplikacja logiki powinna mieć *wyzwalacz* i *akcję*. 
   Wyzwalacz powinien łączyć się z kontem integracji podstawowego regionu i akcja powinna łączyć się z kontem integracji regionu pomocniczego. 
   Na podstawie przedziału czasu wyzwalacz sonduje tabelę stanu uruchomienia regionu podstawowego i ściąga nowe rekordy, jeśli istnieją. Akcja aktualizuje je do konta integracji regionu pomocniczego. 
   Dzięki temu można uzyskać przyrostowy stan czasu wykonywania z regionu podstawowego do regionu pomocniczego.

4. Ciągłość działania w ramach konta integracji Logic Apps jest przeznaczona do obsługi na podstawie protokołów B2B — X12, AS2 i EDIFACT. Aby znaleźć szczegółowe instrukcje, wybierz odpowiednie linki.

5. Zaleca się również wdrożenie wszystkich zasobów regionu podstawowego w regionie pomocniczym. 

   Zasoby regionu podstawowego obejmują Azure SQL Database lub Azure Cosmos DB, Azure Service Bus i Event Hubs platformy Azure używane do obsługi komunikatów, API Management platformy Azure i funkcji Azure Logic Apps w Azure App Service.   

6. Ustanów połączenie z regionu podstawowego do regionu pomocniczego. Aby ściągnąć stan uruchomienia z regionu podstawowego, należy utworzyć aplikację logiki w regionie pomocniczym. 

   Aplikacja logiki powinna mieć wyzwalacz i akcję. 
   Wyzwalacz powinien łączyć się z kontem integracji podstawowego regionu. 
   Akcja powinna łączyć się z kontem integracji regionu pomocniczego. 
   Na podstawie przedziału czasu wyzwalacz sonduje tabelę stanu uruchomienia regionu podstawowego i ściąga nowe rekordy, jeśli istnieją. 
   Akcja aktualizuje je do konta integracji regionu pomocniczego. 
   Ten proces umożliwia uzyskanie przyrostowego stanu środowiska uruchomieniowego z regionu podstawowego do regionu pomocniczego.

Ciągłość działania w ramach konta integracji Logic Apps zapewnia pomoc techniczną na podstawie protokołów B2B X12, AS2 i EDIFACT. Aby uzyskać szczegółowe instrukcje dotyczące korzystania z X12 i AS2, zobacz [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) i [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) w tym artykule.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Przechodzenie w tryb failover do regionu pomocniczego podczas zdarzenia awarii

W przypadku awarii, gdy region podstawowy nie jest dostępny w celu zapewnienia ciągłości działania, kieruj ruch do regionu pomocniczego. Region pomocniczy ułatwia firmie szybkie odzyskiwanie funkcji w celu spełnienia wymagań dotyczących punktu odzyskiwania/RTO uzgodnionego przez partnerów. Ponadto minimalizuje wysiłki w celu przełączenia w tryb failover z jednego regionu do innego. 

Oczekiwane opóźnienie podczas kopiowania numerów kontrolnych z regionu podstawowego do regionu pomocniczego. Aby uniknąć wysyłania duplikatów wygenerowanych numerów kontrolek do partnerów w trakcie zdarzenia awarii, zalecenie polega na zwiększeniu liczby kontrolek w umowach dotyczących regionu pomocniczego za pomocą [poleceń cmdlet programu PowerShell](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Powracanie do zdarzenia po awarii w regionie podstawowym

Aby wrócić do regionu podstawowego, gdy jest dostępny, wykonaj następujące kroki:

1. Zatrzymaj akceptowanie komunikatów od partnerów w regionie pomocniczym.  

2. Zwiększ wygenerowane numery kontrolne dla wszystkich umów regionu podstawowego za pomocą [poleceń cmdlet programu PowerShell](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Kierowanie ruchu z regionu pomocniczego do regionu podstawowego.

4. Sprawdź, czy aplikacja logiki utworzona w regionie pomocniczym do ściągania stanu uruchomienia z regionu podstawowego jest włączona.

## <a name="x12"></a>X12 

Ciągłość działania w przypadku dokumentów EDI X12 jest oparta na numerach kontroli:

> [!TIP]
> Możesz również użyć [szablonu szybkiego startu X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) do tworzenia aplikacji logiki. Tworzenie podstawowych i pomocniczych kont integracji jest wymagane przed użyciem szablonu. Szablon pomaga utworzyć dwie aplikacje logiki, jeden dla odebranych numerów kontroli i drugi dla wygenerowanych numerów kontrolnych. Odpowiednie wyzwalacze i akcje są tworzone w usłudze Logic Apps, łącząc wyzwalacz z podstawowym kontem integracji i akcją do pomocniczego konta integracji.

**Wymagania wstępne**

Aby włączyć odzyskiwanie awaryjne dla wiadomości przychodzących, wybierz ustawienia opcji sprawdzanie duplikatów w ustawieniach odbioru umowy X12.

![Wybierz ustawienia zduplikowanych sprawdzania](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukaj w witrynie **X12** i wybierz pozycję **X12 — gdy numer kontrolny jest modyfikowany**.   

   ![Wyszukaj X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Wyzwalacz zostanie wyświetlony w celu nawiązania połączenia z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji podstawowego regionu.

3. Wprowadź nazwę połączenia, wybierz *konto integracji podstawowego regionu* z listy i wybierz pozycję **Utwórz**.   

   ![Zrzut ekranu pokazujący, gdzie wprowadzić nazwę połączenia i wybrać konto integracji podstawowego regionu. ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Ustawienie **Data i godzina rozpoczęcia synchronizacji numeru kontrolnego** jest opcjonalne. **Częstotliwość** można ustawić na wartość **Day**, **Hour**, **minuta** lub **sekundę** z interwałem.   

   ![Zrzut ekranu, który pokazuje datę i godzinę rozpoczęcia synchronizacji numeru kontrolnego.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Wybierz pozycję **Nowy krok** > **Dodaj akcję**.

   ![Zrzut ekranu pokazujący przycisk Nowy krok i opcję Dodaj akcję.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Wyszukaj w witrynie **X12** i wybierz pozycję **X12 — Dodaj lub zaktualizuj numery kontrolne**.   

   ![Dodaj lub zaktualizuj numery kontrolne](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Aby połączyć akcję z kontem integracji regionu pomocniczego, wybierz pozycję **Zmień połączenie**  >  **Dodaj nowe połączenie** , aby wyświetlić listę dostępnych kont integracji. Wprowadź nazwę połączenia i wybierz z listy *konto integracji regionu pomocniczego* , a następnie wybierz pozycję **Utwórz**. 

   ![Zrzut ekranu pokazujący, gdzie dodać nazwę konta integracji regionu pomocniczego.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Przejdź do wejść pierwotnych, klikając ikonę w prawym górnym rogu.

   ![Zrzut ekranu pokazujący ikonę, która ma zostać przełączona na pierwotną.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Wybierz pozycję treść z selektora zawartości dynamicznej i Zapisz aplikację logiki.

   ![Dynamiczne pola zawartości](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   W oparciu o przedział czasu wyzwalacz sonduje region podstawowy otrzymał tabelę numerów kontroli i ściąga nowe rekordy. 
   Akcja aktualizuje rekordy w ramach konta integracji regionu pomocniczego. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **pominięty**.   

   ![Tabela numerów kontrolek](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Na podstawie przedziału czasowego stan środowiska wykonawczego jest replikowany z regionu podstawowego do regionu pomocniczego. W przypadku awarii, gdy region podstawowy nie jest dostępny, kieruj ruch do regionu pomocniczego w celu zachowania ciągłości działania. 

## <a name="edifact"></a>EDIFACT 

Ciągłość działania w przypadku dokumentów EDI EDIFACT jest oparta na numerach kontrolnych.

**Wymagania wstępne**

Aby włączyć odzyskiwanie awaryjne dla wiadomości przychodzących, wybierz ustawienia opcji sprawdzanie duplikatów w ustawieniach odbioru umowy EDIFACT.

![Zrzut ekranu pokazujący Ustawienia sprawdzania duplikatów w ustawieniach odbioru umowy EDIFACT.](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukaj w witrynie **EDIFACT** i wybierz pozycję **EDIFACT — gdy numer kontrolny jest modyfikowany**.

   ![Wyszukaj EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Wyzwalacz zostanie wyświetlony w celu nawiązania połączenia z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji podstawowego regionu. 

3. Wprowadź nazwę połączenia, wybierz *konto integracji podstawowego regionu* z listy i wybierz pozycję **Utwórz**.    

   ![Nazwa konta integracji regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Ustawienie **Data i godzina rozpoczęcia synchronizacji numeru kontrolnego** jest opcjonalne. **Częstotliwość** można ustawić na wartość **Day**, **Hour**, **minuta** lub **sekundę** z interwałem.    

   ![Zrzut ekranu pokazujący ustawienia wartości daty i godziny.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Wybierz pozycję **Nowy krok** > **Dodaj akcję**.    

   ![Zrzut ekranu pokazujący, gdzie wybrać opcję Dodaj akcję.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Wyszukaj w witrynie **EDIFACT** i wybierz pozycję **EDIFACT — Dodaj lub zaktualizuj numery kontrolne**.   

   ![Zrzut ekranu pokazujący, gdzie dodać lub zaktualizować numery kontrolne.](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Aby połączyć akcję z kontem integracji regionu pomocniczego, wybierz pozycję **Zmień połączenie**  >  **Dodaj nowe połączenie** , aby wyświetlić listę dostępnych kont integracji. Wprowadź nazwę połączenia i wybierz z listy *konto integracji regionu pomocniczego* , a następnie wybierz pozycję **Utwórz**.

   ![Utwórz nazwę konta integracji regionu pomocniczego.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Przejdź do wejść pierwotnych, klikając ikonę w prawym górnym rogu.

   ![Zrzut ekranu, który podświetla ikonę, aby wybrać, Kiedy chcesz przełączyć się na pierwotne dane wejściowe.](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Wybierz pozycję treść z selektora zawartości dynamicznej i Zapisz aplikację logiki.   

   ![Zrzut ekranu pokazujący dynamiczny wybór zawartości, w którym można wybrać treść z.](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   W oparciu o przedział czasu wyzwalacz sonduje region podstawowy otrzymał tabelę numerów kontroli i ściąga nowe rekordy.
   Akcja aktualizuje rekordy do konta integracji regionu pomocniczego. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **pominięty**.

   ![Tabela numerów kontrolek](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Na podstawie przedziału czasowego stan środowiska wykonawczego jest replikowany z regionu podstawowego do regionu pomocniczego. W przypadku awarii, gdy region podstawowy nie jest dostępny, kieruj ruch do regionu pomocniczego w celu zachowania ciągłości działania. 

## <a name="as2"></a>AS2 

Ciągłość działania w przypadku dokumentów korzystających z protokołu AS2 jest oparta na IDENTYFIKATORze komunikatu i wartości MIC.

> [!TIP]
> Możesz również użyć [szablonu szybkiego startu AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) do tworzenia aplikacji logiki. Tworzenie podstawowych i pomocniczych kont integracji jest wymagane przed użyciem szablonu. Szablon pomaga utworzyć aplikację logiki, która ma wyzwalacz i akcję. Aplikacja logiki tworzy połączenie z poziomu wyzwalacza z podstawowym kontem integracji i akcją pomocniczego konta integracji.

1. Utwórz [aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.  

2. Wyszukaj w witrynie **AS2** i wybierz pozycję **AS2 — po utworzeniu wartości MIC**.   

   ![Wyszukaj AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Wyzwalacz zostanie wyświetlony w celu nawiązania połączenia z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji podstawowego regionu. 
   
3. Wprowadź nazwę połączenia, wybierz *konto integracji podstawowego regionu* z listy i wybierz pozycję **Utwórz**.

   ![Zrzut ekranu pokazujący, gdzie wprowadzić nazwę połączenia podczas tworzenia wartości MIC.](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. Ustawienie **daty i godziny początkowej synchronizacji wartości MIC** jest opcjonalne. **Częstotliwość** można ustawić na wartość **Day**, **Hour**, **minuta** lub **sekundę** z interwałem.   

   ![Data i godzina](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Wybierz pozycję **Nowy krok** > **Dodaj akcję**.  

   ![Nowy krok, Dodawanie akcji](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Wyszukaj w witrynie **AS2** i wybierz pozycję **AS2 — Dodaj lub zaktualizuj zawartość MIC**.  

   ![Dodanie lub aktualizacja mikrofonu](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Aby połączyć akcję z pomocniczym kontem integracji, wybierz pozycję **Zmień połączenie**  >  **Dodaj nowe połączenie** , aby wyświetlić listę dostępnych kont integracji. Wprowadź nazwę połączenia i wybierz z listy *konto integracji regionu pomocniczego* , a następnie wybierz pozycję **Utwórz**.

   ![Nazwa konta integracji regionu pomocniczego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Przejdź do wejść pierwotnych, klikając ikonę w prawym górnym rogu.

   ![Przełącz na pierwotne dane wejściowe](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Wybierz pozycję treść z selektora zawartości dynamicznej i Zapisz aplikację logiki.   

   ![Zawartość dynamiczna](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Na podstawie interwału czasowego wyzwalacz sonduje podstawową tabelę regionów i ściąga nowe rekordy. Akcja aktualizuje je do konta integracji regionu pomocniczego. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **pominięty**.  

   ![Tabela regionów podstawowych](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Na podstawie przedziału czasowego stan środowiska wykonawczego jest replikowany z regionu podstawowego do regionu pomocniczego. W przypadku awarii, gdy region podstawowy nie jest dostępny, kieruj ruch do regionu pomocniczego w celu zachowania ciągłości działania. 

## <a name="next-steps"></a>Następne kroki

[Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
