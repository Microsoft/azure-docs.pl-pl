---
title: Zarządzanie danymi, arkuszami i tabelami w usłudze Excel Online
description: Zarządzanie danymi w arkuszach i tabelach w usłudze Excel Online dla firm lub Excel Online dla usługi OneDrive przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400727"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Zarządzanie danymi w usłudze Excel Online za pomocą Azure Logic Apps

Za pomocą usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i łącznika usługi [Excel Online dla firm](/connectors/excelonlinebusiness/) lub [programu Excel Online for OneDrive](/connectors/excelonline/) można tworzyć automatyczne zadania i przepływy pracy oparte na danych w usłudze Excel Online dla firm lub w usłudze OneDrive. Ten łącznik zawiera akcje pomagające w pracy z danymi i zarządzaniu arkuszami kalkulacyjnymi, na przykład:

* Utwórz nowe arkusze i tabele.
* Pobierz arkusze, tabele i wiersze oraz zarządzaj nimi.
* Dodaj pojedyncze wiersze i kolumny kluczy.

Następnie można użyć danych wyjściowych z tych akcji z akcjami dla innych usług. Na przykład jeśli używasz akcji, która tworzy arkusze w każdym tygodniu, możesz użyć innej akcji, która wysyła wiadomość e-mail z potwierdzeniem za pomocą łącznika Office 365 Outlook.

Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Łączniki [programu Excel Online dla firm](/connectors/excelonlinebusiness/) i [programu Excel Online dla usługi OneDrive](/connectors/excelonline/) współpracują z Azure Logic Apps i różnią się od [łącznika programu Excel w usłudze powerapps](/connectors/excel/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto](https://www.office.com/) służbowe dla Twojego konta służbowego lub osobistego konto Microsoft

  Dane programu Excel mogą istnieć jako plik wartości rozdzielanych przecinkami (CSV) w folderze magazynu, na przykład w usłudze OneDrive. 
  Można również użyć tego samego pliku CSV z łącznikiem o [płaskim pliku](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do danych usługi Excel Online. Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, wybierz oddzielny wyzwalacz, na przykład wyzwalacz **cykliczny** .

## <a name="add-excel-action"></a>Dodaj akcję programu Excel

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie została jeszcze otwarta.

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wprowadź ciąg "Excel" jako filtr. Na liście Akcje wybierz żądaną akcję.

   > [!NOTE]
   > Projektant aplikacji logiki nie może załadować tabel, które mają 100 lub więcej kolumn. Jeśli to możliwe, zmniejsz liczbę kolumn w zaznaczonej tabeli, aby projektant mógł załadować tabelę.

1. Jeśli zostanie wyświetlony monit, zaloguj się do konta służbowego.

   Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie z usługą Excel Online i uzyskać dostęp do danych.

1. Kontynuuj dostarczanie niezbędnych szczegółów wybranej akcji i kompilowanie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w plikach OpenAPI łącznika (dawniej Swagger), zobacz następujące strony referencyjne łącznika:

* [Excel Online dla firm](/connectors/excelonlinebusiness/)
* [Excel Online dla usługi OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
