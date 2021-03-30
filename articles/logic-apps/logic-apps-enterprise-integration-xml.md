---
title: Wiadomości XML i pliki proste
description: Przetwarzaj, Weryfikuj i Przekształcaj komunikaty XML w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792155"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wiadomości XML i pliki proste w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

W [Azure Logic Apps](logic-apps-overview.md)można przetwarzać komunikaty XML wysyłane i odbierane przy użyciu pakiet integracyjny dla przedsiębiorstw. Jeśli użyto BizTalk Server, Pakiet integracyjny dla przedsiębiorstw oferuje podobne funkcje do przekształcania i weryfikowania komunikatów, pracy z plikami prostymi, a nawet do wzbogacania lub wyodrębniania określonych właściwości z wiadomości przy użyciu XPath. Jeśli jesteś nowym miejscem w tym miejscu, te funkcje rozszerzają sposób przetwarzania komunikatów w przepływie pracy aplikacji logiki. Na przykład jeśli masz scenariusz biznesowy (B2B) i pracujesz z określonymi schematami XML, możesz użyć Pakiet integracyjny dla przedsiębiorstw, aby zwiększyć sposób przetwarzania tych komunikatów przez firmę.

Na przykład Pakiet integracyjny dla przedsiębiorstw obejmuje następujące funkcje:

* [Sprawdzanie poprawności kodu XML](logic-apps-enterprise-integration-xml-validation.md): sprawdzanie poprawności przychodzącej lub wychodzącej wiadomości XML względem określonego schematu.

* [Przekształcanie kodu XML](logic-apps-enterprise-integration-transform.md): konwertowanie lub dostosowywanie wiadomości XML na podstawie wymagań lub wymagań partnera przy użyciu usługi Maps.

* [Kodowanie pliku prostego i płaskie dekodowanie plików](logic-apps-enterprise-integration-flatfile.md): Koduj lub Dekoduj plik prosty.

  Na przykład system SAP akceptuje i wysyła pliki IDOC w formacie pliku prostego. Wiele platform integracji tworzy komunikaty XML, w tym Logic Apps. Dzięki temu można utworzyć aplikację logiki, która używa kodera prostego pliku do "konwersji" plików XML na pliki płaskie.

* [XPath](workflow-definition-language-functions-reference.md#xpath): wzbogacanie komunikatu i wyodrębnienie określonych właściwości z wiadomości. Następnie można użyć wyodrębnionych właściwości, aby skierować komunikat do miejsca docelowego lub do pośredniego punktu końcowego.

## <a name="sample"></a>Przykład

[Wdróż w pełni działającą aplikację logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (przykład GitHub) przy użyciu funkcji XML w Azure Logic Apps.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
