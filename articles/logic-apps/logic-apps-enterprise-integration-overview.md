---
title: Integracja przedsiębiorstw B2B
description: Dowiedz się więcej o budowania zautomatyzowanych przepływów pracy B2B na rzecz integracji dla przedsiębiorstw przy użyciu Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: 09d253aae4c054db5efdc252f62986044e1d366b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771861"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Rozwiązania integracji dla przedsiębiorstw B2B z Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw

W przypadku rozwiązań między firmami (B2B) i bezproblemowej komunikacji między organizacjami można tworzyć zautomatyzowane, skalowalne przepływy pracy integracji dla przedsiębiorstw przy użyciu rozwiązania Pakiet integracyjny dla przedsiębiorstw (EIP) z Azure Logic Apps [.](../logic-apps/logic-apps-overview.md) Mimo że organizacje używają różnych protokołów i formatów, mogą wymieniać wiadomości w formie elektronicznej. Program EIP przekształca różne formaty w format, który systemy organizacji mogą przetwarzać i obsługuje standardowe protokoły branżowe, w tym [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)i [EDIFACT.](../logic-apps/logic-apps-enterprise-integration-edifact.md) Możesz również zwiększyć bezpieczeństwo komunikatów, używając zarówno szyfrowania, jak i podpisów cyfrowych. Program EIP obsługuje te [łączniki integracji dla przedsiębiorstw](../connectors/managed.md#enterprise-connectors) i następujące standardy branżowe:

* Electronic Data Interchange (EDI)
* Enterprise Application Integration (EAI)

Jeśli znasz usługę Microsoft BizTalk Server lub Azure BizTalk Services, funkcja EIP jest zgodna z podobnymi pojęciami, dzięki czemu funkcje są łatwe w użyciu. Jednak jedną główną różnicą jest to, że program EIP jest oparty na architekturze opartej na "kontach integracji", aby uprościć przechowywanie artefaktów używanych w komunikacji B2B i zarządzanie nimi. Te konta są kontenerami opartymi na chmurze, które przechowują wszystkie artefakty, takie jak partnerzy, umowy, schematy, mapy i certyfikaty. 

## <a name="why-use-the-enterprise-integration-pack"></a>Dlaczego warto używać Pakiet integracyjny dla przedsiębiorstw?

* Dzięki adresowi EIP możesz przechowywać wszystkie artefakty w jednym miejscu — na koncie integracji.

* Przepływy pracy B2B można tworzyć i integrować z aplikacjami typu oprogramowanie jako usługa (SaaS) innych firm, aplikacjami lokalnymi i niestandardowymi przy użyciu Azure Logic Apps i łączników.

* Możesz utworzyć niestandardowy kod dla aplikacji logiki za pomocą usługi Azure Functions.

## <a name="how-do-i-get-started"></a>Jak rozpocząć?

Przed rozpoczęciem tworzenia przepływów pracy aplikacji logiki B2B przy użyciu programu EIP potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Konto [integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z artefaktami, których chcesz użyć

* Do tworzenia map i schematów można użyć narzędzi [Microsoft Azure Logic Apps Integracja dla przedsiębiorstw Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) i Visual Studio 2015.

Po utworzeniu konta integracji i dodaniu artefaktów możesz rozpocząć tworzenie przepływów pracy B2B przy użyciu tych artefaktów, tworząc aplikację logiki w Azure Portal. Jeśli jesteś nowym użytkownikom aplikacji logiki, spróbuj [utworzyć podstawową aplikację logiki.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Aby pracować z tymi artefaktami, upewnij się, że najpierw połączysz konto integracji z aplikacją logiki. Następnie aplikacja logiki może uzyskać dostęp do konta integracji. Aplikacje logiki można również tworzyć i wdrażać oraz zarządzać nimi za pomocą programu Visual Studio lub [PowerShell.](/powershell/module/az.logicapp)

Poniżej podano instrukcje wysokiego poziomu, które należy wykonać, aby rozpocząć tworzenie aplikacji logiki B2B:

![Wymagania wstępne dotyczące tworzenia aplikacji logiki B2B](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Wypróbuj teraz

[Wdrażanie w pełni operacyjnej przykładowej aplikacji logiki, która wysyła i odbiera komunikaty AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partnerów handlowych](logic-apps-enterprise-integration-partners.md)
* [Tworzenie umów](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Dodawanie schematów](logic-apps-enterprise-integration-schemas.md)
* [Dodawanie map](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrowanie z usługi BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
