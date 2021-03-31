---
title: Integracja przedsiębiorstw B2B
description: Dowiedz się więcej o tworzeniu zautomatyzowanych przepływów pracy B2B dla integracji z przedsiębiorstwem przy użyciu Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: adf15904d4490647074d6e2b46ac7e9e871dd388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86536406"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Rozwiązania B2B Enterprise Integration z Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw

W przypadku rozwiązań Business-to-Business (B2B) i bezproblemowej komunikacji między organizacjami można tworzyć zautomatyzowane przepływy pracy integracji dla przedsiębiorstw przy użyciu Pakiet integracyjny dla przedsiębiorstw (EIP) z [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Chociaż organizacje używają różnych protokołów i formatów, mogą wymieniać wiadomości elektronicznie. Element EIP przekształca różne formaty w format, który systemy organizacji mogą przetwarzać i obsługuje protokoły standardowe branżowe, w tym [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)i [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Możesz również poprawić zabezpieczenia komunikatów przy użyciu szyfrowania i podpisów cyfrowych. Element EIP obsługuje te [Łączniki integracji przedsiębiorstwa](../connectors/apis-list.md#integration-account-connectors) oraz następujące standardy branżowe:

* Elektronicznej wymiany danych (EDI)
* Enterprise Application Integration (EAI)

Jeśli znasz już firmę Microsoft BizTalk Server lub platformę Azure BizTalk Services, element EIP jest zgodny z podobnymi pojęciami, co ułatwia korzystanie z funkcji. Jednak jedną istotną różnicą jest to, że element EIP jest architektury oparty na "kontach integracji", co upraszcza przechowywanie i zarządzanie artefaktami używanymi w komunikacji B2B. Te konta są kontenerami opartymi na chmurze, które przechowują wszystkie artefakty, takie jak partnerzy, umowy, schematy, mapy i certyfikaty. 

## <a name="why-use-the-enterprise-integration-pack"></a>Dlaczego warto używać Pakiet integracyjny dla przedsiębiorstw?

* Za pomocą EIP można przechowywać wszystkie artefakty w jednym miejscu — Twoim koncie integracji.

* Możesz tworzyć przepływy pracy B2B i integrować je z aplikacjami innych firm (SaaS), aplikacjami lokalnymi i aplikacjami niestandardowymi przy użyciu Azure Logic Apps i łączników.

* Możesz utworzyć niestandardowy kod dla aplikacji logiki za pomocą usługi Azure Functions.

## <a name="how-do-i-get-started"></a>Jak rozpocząć?

Przed rozpoczęciem tworzenia przepływów pracy aplikacji logiki B2B z elementem EIP potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z artefaktami, które mają być używane

* Aby utworzyć mapy i schematy, można użyć [Microsoft Azure Logic Apps integracja dla przedsiębiorstw narzędzia dla programu Visual studio 2015 2,0](https://aka.ms/vsmapsandschemas) i visual Studio 2015.

Po utworzeniu konta integracji i dodaniu artefaktów możesz rozpocząć tworzenie przepływów pracy B2B z tymi artefaktami, tworząc aplikację logiki w Azure Portal. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, spróbuj [utworzyć podstawową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby móc korzystać z tych artefaktów, upewnij się, że konto integracji zostało najpierw połączone z aplikacją logiki. Następnie aplikacja logiki będzie mogła uzyskać dostęp do Twojego konta integracji. Za pomocą programu Visual Studio lub [programu PowerShell](/powershell/module/az.logicapp)można także tworzyć i wdrażać aplikacje logiki oraz zarządzać nimi.

Poniżej przedstawiono ogólne kroki umożliwiające rozpoczęcie tworzenia aplikacji logiki B2B:

![Wymagania wstępne dotyczące tworzenia aplikacji logiki B2B](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Wypróbuj teraz

[Wdróż w pełni działającą przykładową aplikację logiki, która wysyła i odbiera komunikaty AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partnerów handlowych](logic-apps-enterprise-integration-partners.md)
* [Utwórz umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Dodawanie schematów](logic-apps-enterprise-integration-schemas.md)
* [Dodawanie map](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrowanie z usługi BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
