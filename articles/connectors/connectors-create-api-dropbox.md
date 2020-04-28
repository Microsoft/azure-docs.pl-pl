---
title: Łączenie z usługą Dropbox
description: Automatyzowanie zadań i przepływów pracy, które przesyłają pliki w usłudze Dropbox i zarządzają nimi przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665755"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Przekazywanie plików i zarządzanie nimi w usłudze Dropbox przy użyciu Azure Logic Apps

Za pomocą łącznika usługi Dropbox i Azure Logic Apps można tworzyć zautomatyzowane przepływy pracy, które przesyłają pliki i zarządzają nimi na koncie usługi Dropbox. 

W tym artykule pokazano, jak nawiązać połączenie z usługą Dropbox z poziomu aplikacji logiki, a następnie dodać tę funkcję, **gdy zostanie utworzony plik** wyzwalacza, a w polu Dropbox **pobierana zawartość pliku przy użyciu ścieżki** .

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto usługi Dropbox](https://www.dropbox.com/), którego możesz zarejestrować się bezpłatnie. Poświadczenia konta są niezbędne do utworzenia połączenia między aplikacją logiki a kontem usługi Dropbox.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Na potrzeby tego przykładu potrzebujesz pustej aplikacji logiki.

## <a name="add-trigger"></a>Dodawanie wyzwalacza

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wprowadź ciąg "Dropbox" jako filtr.
Z listy Wyzwalacze wybierz ten wyzwalacz: **po utworzeniu pliku**

   ![Wybierz wyzwalacz usługi Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Zaloguj się przy użyciu poświadczeń konta usługi Dropbox i Autoryzuj dostęp do danych usługi Dropbox w celu Azure Logic Apps.

1. Podaj wymagane informacje dla wyzwalacza. 

   W tym przykładzie wybierz folder, w którym chcesz śledzić tworzenie plików. Aby przeglądać foldery, wybierz ikonę folderu obok pola **folder** .

## <a name="add-action"></a>Dodawanie akcji

Teraz Dodaj akcję, która pobiera zawartość z dowolnego nowego pliku.

1. W obszarze wyzwalacza wybierz pozycję **Następny krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wprowadź ciąg "Dropbox" jako filtr.
Z listy Akcje wybierz tę akcję: **Pobierz zawartość pliku przy użyciu ścieżki**

1. Jeśli nie masz jeszcze autoryzacji Azure Logic Apps dostępu do usługi Dropbox, Autoryzuj dostęp teraz.

1. Aby przejść do ścieżki pliku, której chcesz użyć, obok pola **ścieżka pliku** wybierz przycisk wielokropka (**...**). 

   Możesz również kliknąć wewnątrz pola **ścieżka pliku** i z listy zawartość dynamiczna wybierz pozycję **ścieżka pliku**, której wartość jest dostępna jako dane wyjściowe wyzwalacza dodanego w poprzedniej sekcji.

1. Gdy skończysz, Zapisz aplikację logiki.

1. Aby wyzwolić aplikację logiki, Utwórz nowy plik w usłudze Dropbox.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/dropbox/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
