---
title: Migrowanie aplikacji z BizTalk Services do Azure Logic Apps
description: Jak przenieść aplikacje i rozwiązania z Microsoft Azure BizTalk Services (serwera usługi MAB) do Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 6c07ab4b18c017bd29723d2640129b8e67374e3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87837384"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migruj swoje aplikacje i rozwiązania z BizTalk Services do Azure Logic Apps

Trwa wycofywanie Microsoft Azure BizTalk Services (serwera usługi MAB). Aby przenieść rozwiązania integracji serwera usługi MAB do [Azure Logic Apps](../logic-apps/logic-apps-overview.md), postępuj zgodnie ze wskazówkami w tym artykule. 

## <a name="introduction"></a>Wprowadzenie

BizTalk Services składa się z dwóch podusług:

* Połączenia hybrydowe BizTalk Services firmy Microsoft
* Integracja oparta na mostkach EAI i EDI

[Azure App Service połączenia hybrydowe](../app-service/app-service-hybrid-connections.md) zastępuje BizTalk Services połączenia hybrydowe. Usługa Azure Połączenia hybrydowe jest dostępna w Azure App Service za pomocą Azure Portal. Ta usługa udostępnia Menedżer połączeń hybrydowych, dzięki czemu można zarządzać istniejącymi BizTalk Services połączeniami hybrydowymi, a także z nowymi połączeniami hybrydowymi tworzonymi w portalu. 

[Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje integrację z usługą EAI i EDI na mostku ze wszystkimi tymi samymi możliwościami w BizTalk Services i nie tylko. Ta usługa zapewnia oparte na użyciu funkcje przepływu pracy i aranżacji w skali chmury umożliwiające szybkie i łatwe tworzenie złożonych rozwiązań integracji za pośrednictwem przeglądarki lub programu Visual Studio.

Ta tabela mapuje BizTalk Services możliwości Logic Apps.

| BizTalk Services   | Logic Apps            | Przeznaczenie                      |
| ------------------ | --------------------- | ---------------------------- |
| Łącznik          | Łącznik             | Wyślij i Odbierz dane   |
| Bridge             | Aplikacja logiki             | Procesor potoku           |
| Weryfikuj etap     | Akcja walidacji kodu XML | Sprawdzanie poprawności dokumentu XML względem schematu | 
| Etap wzbogacania       | Tokeny danych           | Podwyższanie poziomu właściwości do komunikatów lub dla decyzji dotyczących routingu |
| Przekształć etap    | Akcja przekształcenia      | Konwertowanie komunikatów XML z jednego formatu na inny |
| Dekodowanie etapu       | Akcja dekodowania pliku prostego | Konwertuj z prostego pliku do formatu XML |
| Zakoduj etap       | Akcja kodowania pliku prostego | Konwertuj z formatu XML do pliku prostego |
| Inspektor komunikatów  | Azure Functions lub API Apps | Uruchamianie niestandardowego kodu w ramach integracji |
| Akcja trasy       | Warunek lub przełącznik | Kierowanie komunikatów do jednego z określonych łączników |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services artefakty

BizTalk Services ma kilka rodzajów artefaktów. 

## <a name="connectors"></a>Łączniki

Łączniki BizTalk Services ułatwiają wysyłanie i odbieranie danych, w tym mostków dwukierunkowych, które umożliwiają interakcje żądania/odpowiedzi oparte na protokole HTTP. Logic Apps używa tej samej terminologii i ma setki łączników, które służą do tego samego celu, łącząc się z szeroką gamę technologii i usług. Na przykład łączniki są dostępne dla usług Cloud SaaS i PaaS, takich jak OneDrive, Office 365, Dynamics CRM i inne, a także lokalnych systemów za pomocą lokalnej bramy danych, która zastępuje usługę adaptera BizTalk dla BizTalk Services. Źródła w BizTalk Services są ograniczone do kolejki FTP, SFTP i Service Bus lub subskrypcji tematu.

![Diagram przedstawiający przepływ BizTalk Services.](media/logic-apps-move-from-mabs/sources.png)

Domyślnie każdy mostek ma punkt końcowy HTTP, który jest skonfigurowany przy użyciu adresu środowiska uruchomieniowego i względnych właściwości adresu dla mostka. Aby osiągnąć te same wyniki przy użyciu Logic Apps, użyj akcji [żądania i odpowiedzi](../connectors/connectors-native-reqres.md) .

## <a name="xml-processing-and-bridges"></a>Przetwarzanie i mostki XML

W BizTalk Services, most jest analogiczny do potoku przetwarzania. Mostek może pobrać dane z łącznika, wykonywać pewne prace z danymi i wysyłać wyniki do innego systemu. Logic Apps jest taka sama poprzez obsługę tych samych wzorców interakcji opartych na potoku jako BizTalk Services, a także udostępnia inne wzorce integracji. [Mostek XML Request-Reply](/previous-versions/azure/hh689781(v=azure.100)) w BizTalk Services jest znany jako potok VETER, który składa się z etapów, które wykonują następujące zadania:

* (V) Weryfikuj
* (E) wzbogacanie
* (T) transformacja
* (E) wzbogacanie
* (R) trasa

Ten obraz pokazuje, jak przetwarzanie jest podzielone między żądaniem a odpowiedzią, która zapewnia kontrolę nad żądaniem i ścieżkami odpowiedzi oddzielnie, na przykład przy użyciu różnych map dla każdej ścieżki:

![Zrzut ekranu pokazujący sposób dzielenia przetwarzania między żądaniem a odpowiedzią.](media/logic-apps-move-from-mabs/xml-request-reply.png)

Ponadto mostek XML One-Way dodaje etapy dekodowania i kodowania na początku i na końcu przetwarzania. Pass-Through Bridge zawiera jeden etap wzbogacania.

### <a name="message-processing-decoding-and-encoding"></a>Przetwarzanie komunikatów, dekodowanie i kodowanie

W BizTalk Services można odbierać różne typy komunikatów XML i określać pasujący schemat dla odebranego komunikatu. Ta operacja jest wykonywana w obszarze *typy komunikatów* na etapie przetwarzania odbierania. Etap dekodowania używa wykrytego typu komunikatu do zdekodowania komunikatu przy użyciu podanego schematu. Jeśli schemat jest schematem pliku prostego, ten etap konwertuje przychodzący plik płaski do formatu XML. 

Logic Apps oferuje podobne funkcje. Użytkownik otrzymuje plik prosty za pośrednictwem różnych protokołów przy użyciu różnych wyzwalaczy łączników (systemu plików, FTP, HTTP i tak dalej), a następnie używa akcji [dekodowania pliku prostego](../logic-apps/logic-apps-enterprise-integration-flatfile.md) do konwersji danych przychodzących do formatu XML. Istniejące płaskie schematy plików można przenieść bezpośrednio do Logic Apps bez wprowadzania żadnych zmian, a następnie przekazać schematy do konta integracji.

### <a name="validation"></a>Walidacja

Po przekonwertowaniu danych przychodzących na kod XML (lub jeśli w kodzie XML był otrzymany format wiadomości), sprawdzanie poprawności przebiega w celu ustalenia, czy komunikat jest zgodny ze schematem XSD. Aby wykonać to zadanie w Logic Apps, użyj akcji [walidacji kodu XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) . Możesz użyć tych samych schematów z BizTalk Services bez wprowadzania żadnych zmian.

### <a name="transform-messages"></a>Przekształcanie komunikatów

W BizTalk Services etap przekształcania konwertuje jeden format wiadomości XML na inny. Ta czynność jest wykonywana przez zastosowanie mapy przy użyciu mapowania opartego na TRFM. W Logic Apps proces jest podobny. Akcja przekształcenia wykonuje mapę z konta integracji. Główną różnicą jest to, że mapy w Logic Apps są w formacie XSLT. XSLT obejmuje możliwość wielokrotnego użycia istniejącego XSLT, w tym map utworzonych dla BizTalk Server zawierających functoids. 

### <a name="routing-rules"></a>Reguły routingu

BizTalk Services wykonuje decyzję routingu dotyczącą tego, który punkt końcowy lub łącznik ma wysyłać wiadomości przychodzące lub dane. Możliwość wyboru ze wstępnie skonfigurowanych punktów końcowych jest możliwa przy użyciu opcji filtrowania routingu:

![Zrzut ekranu pokazujący opcję Filtr routingu.](media/logic-apps-move-from-mabs/route-filter.png)

W BizTalk Services, jeśli istnieją tylko dwie opcje, użycie *warunku* jest najlepszym sposobem na konwersję filtrów routingu w programie BizTalk Services. Jeśli jest więcej niż dwa, użyj **przełącznika**.

Logic Apps zapewnia zaawansowane możliwości logiki oraz zaawansowany przepływ sterowania i Routing przy użyciu [instrukcji warunkowych](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [instrukcji switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Wzbogacanie

W trakcie przetwarzania BizTalk Services na etapie wzbogacania są dodawane właściwości do kontekstu komunikatu skojarzonego z otrzymanymi danymi. Na przykład promowanie właściwości do użycia na potrzeby routingu z wyszukiwania bazy danych lub przez wyodrębnienie wartości przy użyciu wyrażenia XPath. Logic Apps zapewnia dostęp do wszystkich danych kontekstowych przed poprzednimi akcjami, co ułatwia replikację tego samego zachowania. Na przykład za pomocą `Get Row` akcji połączenia SQL zwracane są dane z bazy danych SQL Server i używane są dane w akcji decyzji dotyczącej routingu. Podobnie właściwości przychodzących Service Bus komunikatów umieszczonych w kolejce przez wyzwalacz są adresowane, a także XPath przy użyciu wyrażenia języka definicji przepływu pracy XPath.

### <a name="run-custom-code"></a>Uruchamianie kodu niestandardowego

BizTalk Services umożliwia [Uruchamianie kodu niestandardowego](/previous-versions/azure/dn232389(v=azure.100)) , który jest przekazywany we własnych zestawach. Ta funkcja jest implementowana przez interfejs [IMessageInspector]() . Każdy etap w mostku zawiera dwie właściwości (na karcie Enter i w Inspektorze zakończenia), który udostępnia utworzony typ .NET, który implementuje ten interfejs. Kod niestandardowy umożliwia wykonywanie bardziej złożonego przetwarzania danych i umożliwia ponowne użycie istniejącego kodu w zestawach, które wykonują wspólną logikę biznesową. 

Logic Apps oferuje dwa podstawowe sposoby wykonywania kodu niestandardowego: Azure Functions i API Apps. Azure Functions można tworzyć i wywoływać z aplikacji logiki. Zobacz [Dodawanie i uruchamianie niestandardowego kodu dla aplikacji logiki za Azure Functions](../logic-apps/logic-apps-azure-functions.md). Użyj API Apps, część Azure App Service, aby utworzyć własne wyzwalacze i akcje. Dowiedz się więcej o [tworzeniu niestandardowego interfejsu API, który ma być używany z Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Jeśli masz niestandardowy kod w zestawach, które są wywoływane z BizTalk Services, możesz przenieść ten kod do Azure Functions lub utworzyć niestandardowe interfejsy API przy użyciu API Apps, w zależności od implementacji. Na przykład jeśli masz kod, który zawija inną usługę, dla której Logic Apps nie ma łącznika, następnie Utwórz aplikację interfejsu API i użyj akcji udostępnianych przez aplikację interfejsu API w aplikacji logiki. Jeśli masz funkcje pomocnika lub biblioteki, Azure Functions najprawdopodobniej najlepiej pasują do siebie.

### <a name="edi-processing-and-trading-partner-management"></a>Przetwarzanie EDI i zarządzanie partnerami handlowymi

BizTalk Services i Logic Apps obejmują przetwórstwo EDI i B2B z obsługą AS2 (Applicability Statement 2), X12 i EDIFACT. W BizTalk Services tworzenia mostków EDI oraz tworzenia i zarządzania partnerami handlowymi oraz umowami w dedykowanym portalu śledzenia i zarządzania.
W Logic Apps można uzyskać tę funkcję za pomocą [pakiet integracyjny dla przedsiębiorstw (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP zawiera [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i akcje B2B dla przetwarzania EDI i B2B. Do tworzenia i zarządzania [partnerami handlowymi](../logic-apps/logic-apps-enterprise-integration-partners.md) i [umowami](../logic-apps/logic-apps-enterprise-integration-agreements.md)służy również konto integracji. Po utworzeniu konta integracji można połączyć co najmniej jedną aplikację logiki z kontem. Następnie można użyć akcji B2B, aby uzyskać dostęp do informacji o partnerze handlowym z aplikacji logiki. Dostępne są następujące akcje:

* Kodowanie AS2
* Dekodowanie AS2
* Kodowanie X12
* Dekodowanie X12
* Kodowanie EDIFACT
* Dekodowanie EDIFACT

W przeciwieństwie do BizTalk Services, te akcje są oddzielone od protokołów transportowych. Dlatego podczas tworzenia aplikacji logiki masz większą elastyczność, w której łączniki używane do wysyłania i odbierania danych. Na przykład można odbierać pliki X12 jako załączniki z poczty e-mail, a następnie przetwarzać te pliki w aplikacji logiki. 

## <a name="manage-and-monitor"></a>Zarządzanie i monitorowanie

W BizTalk Services dedykowany portal udostępniał funkcje śledzenia umożliwiające monitorowanie i rozwiązywanie problemów. Logic Apps zapewnia bogatsze możliwości śledzenia i monitorowania [w celu monitorowania aplikacji logiki w Azure Portal](../logic-apps/monitor-logic-apps.md)i obejmuje aplikację mobilną, która umożliwia śledzenie rzeczy podczas przenoszenia.

## <a name="high-availability"></a>Wysoka dostępność

Aby zapewnić wysoką dostępność (HA) w BizTalk Services, można udostępnić obciążenie przetwarzania za pomocą więcej niż jednego wystąpienia w określonym regionie. Logic Apps zapewnia w regionie HA bez dodatkowych kosztów. 

W BizTalk Services, odzyskiwanie po awarii poza regionem do przetwarzania B2B wymaga procesu tworzenia kopii zapasowej i przywracania. W celu zapewnienia ciągłości działania Logic Apps zapewnia międzyregionowe i pasywne [funkcje](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)odzyskiwania po awarii, które umożliwiają synchronizowanie danych B2B między kontami integracji w różnych regionach.

## <a name="next-steps"></a>Następne kroki

* [Co to jest usługa Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub szybko rozpocząć pracę przy użyciu [wstępnie utworzonego szablonu](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Wyświetl wszystkie dostępne łączniki](../connectors/apis-list.md) , których można używać w usłudze Logic Apps