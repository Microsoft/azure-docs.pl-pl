---
title: Przegląd — bezserwerowy platformy Azure dla aplikacji i rozwiązań opartych na chmurze
description: Dowiedz się, jak tworzyć aplikacje i rozwiązania oparte na chmurze bez obaw o infrastrukturę przy użyciu Azure Logic Apps i Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788099"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Bez serwera platformy Azure: omówienie tworzenia aplikacji i rozwiązań opartych na chmurze z użyciem Azure Logic Apps i Azure Functions

Aplikacje [bezserwerowe](https://azure.microsoft.com/solutions/serverless/) oferują korzyści, takie jak zwiększona szybkość opracowywania, zredukowany kod, prostota i skalowanie. W tym artykule opisano różne atrybuty rozwiązań bezserwerowych i ofert bezserwerowych platformy Azure.

## <a name="what-is-serverless"></a>Co to jest bezserwerowe?

Bezserwerowe nie oznacza, że nie ma żadnych serwerów, ale deweloperzy nie muszą martwić się o serwery. Duża część tradycyjnego programowania aplikacji odpowiada na pytania dotyczące skalowania, hostingu i monitorowania rozwiązań w celu spełnienia wymagań aplikacji. Bez użycia serwera te pytania są brane pod uwagę w ramach rozwiązania. Ponadto aplikacje bezserwerowe są rozliczane zgodnie z planem opartym na zużyciu. Jeśli aplikacja nie jest nigdy używana, opłaty nie są naliczane. Te funkcje ułatwiają deweloperom skoncentrowanie się wyłącznie na logice biznesowej rozwiązania.

Podstawowe usługi platformy Azure dla bezserwerowe są [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) i [Azure Functions](https://azure.microsoft.com/services/functions/). Oba rozwiązania są zgodne z opisanymi wcześniej zasadami i ułatwiają deweloperom tworzenie niezawodnych aplikacji w chmurze z minimalnym kodem.

## <a name="what-is-azure-logic-apps"></a>Co to jest usługa Azure Logic Apps?

[Azure Logic Apps](logic-apps-overview.md) pozwala uprościć i zaimplementować skalowalne integracje i przepływy pracy w chmurze. Ta usługa udostępnia projektantowi wizualnemu możliwość modelowania i automatyzowania procesu w postaci serii kroków o nazwie przepływ pracy. Istnieje wiele [łączników](../connectors/apis-list.md) w usługach w chmurze i lokalnych systemach, które szybko łączą aplikację bezserwerową z innymi interfejsami API. Każda aplikacja logiki rozpoczyna się od wyzwalacza, na przykład "gdy konto zostanie dodane do programu Dynamics CRM". Po uruchomieniu wyzwalacza przepływu pracy można uruchamiać kombinacje akcji, konwersji i logiki warunkowej. Logic Apps to doskonały wybór podczas organizowania różnych Azure Functions w procesie, szczególnie w przypadku, gdy proces wymaga współpracy z zewnętrznym systemem lub interfejsem API.

Aby rozpocząć pracę z Logic Apps, Zacznij od [utworzenia pierwszej aplikacji logiki](quickstart-create-first-logic-app-workflow.md). Aby uzyskać więcej informacji technicznych na temat Logic Apps, zobacz [Dokumentacja dla deweloperów](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Co to jest usługa Azure Functions?

Azure Functions to usługa służąca do łatwego uruchamiania fragmentów kodu lub "Functions" w chmurze. Można napisać tylko kod niezbędny dla bieżącego problemu, bez obaw o całą aplikację lub wymaganą infrastrukturę. Funkcje mogą jeszcze bardziej zwiększyć produktywność i można korzystać z wybranego języka programowania, takiego jak C#, F #, Node.js, Python lub PHP. Płacisz tylko za czas działania kodu i skalowanie systemu Azure w razie potrzeby.

Aby rozpocząć pracę z Azure Functions, Zacznij od [utworzenia pierwszej funkcji platformy Azure](../azure-functions/functions-get-started.md). Aby uzyskać więcej informacji technicznych na temat funkcji, zobacz [Dokumentacja dla deweloperów](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Jak kompilować i wdrażać aplikacje bezserwerowe na platformie Azure?

Platforma Azure oferuje rozbudowane narzędzia do tworzenia i wdrażania aplikacji bezserwerowych oraz zarządzania nimi. Możesz tworzyć aplikacje bezpośrednio w Azure Portal przy użyciu [narzędzi w programie Visual Studio](logic-apps-serverless-get-started-vs.md)lub [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Po skompilowaniu aplikacji możesz [szybko wdrożyć tę aplikację za pomocą szablonów Azure Resource Manager](logic-apps-deploy-azure-resource-manager-templates.md). Platforma Azure udostępnia również monitorowanie, do którego można uzyskać dostęp Azure Portal za pomocą interfejsu API lub zestawów SDK lub zintegrowanych narzędzi do Azure Monitor dzienników i Application Insights.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie aplikacji bezserwerowej w programie Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Tworzenie pulpitu nawigacyjnego usługi Customer Insights z użyciem bezserwerowego](logic-apps-scenario-social-serverless.md)
* [Tworzenie wdrożenia aplikacji logiki](logic-apps-azure-resource-manager-templates-overview.md)