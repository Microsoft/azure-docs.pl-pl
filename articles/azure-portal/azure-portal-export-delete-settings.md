---
title: Eksportuj lub Usuń ustawienia Azure Portal
description: Dowiedz się, jak eksportować lub usuwać ustawienia użytkownika, prywatne pulpity nawigacyjne i ustawienia niestandardowe w Azure Portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76900761"
---
# <a name="export-or-delete-user-settings"></a>Eksportowanie i usuwanie ustawień użytkownika

Aby utworzyć niestandardowe środowisko, można użyć ustawień i funkcji w Azure Portal. Informacje o ustawieniach niestandardowych są przechowywane na platformie Azure. Możesz wyeksportować lub usunąć następujące dane użytkownika:

* Prywatne pulpity nawigacyjne w Azure Portal
* Ustawienia użytkownika, takie jak Ulubione subskrypcje lub katalogi, i ostatni zalogowany katalog
* Motywy i inne ustawienia portalu niestandardowego

Dobrym pomysłem jest wyeksportowanie i przejrzenie ustawień przed ich usunięciem. Ponowne kompilowanie pulpitów nawigacyjnych lub przeprowadzenie niestandardowych ustawień może być czasochłonne.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Eksportowanie lub usuwanie ustawień portalu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W nagłówku portalu wybierz pozycję ![](media/azure-portal-export-delete-settings/settings-icon.png) **Ustawienia ikona Ustawienia**.

1. Wybierz opcję **Eksportuj wszystkie ustawienia** lub **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne**.

    ![Opcje Azure Portal ustawień i ustawień](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      W poniższej tabeli opisano te akcje.

      | Akcja | Opis |
      | --- | --- |
      | **Eksportuj wszystkie ustawienia** | Tworzy plik *JSON* , który zawiera ustawienia użytkownika, takie jak motyw kolorów, Ulubione i prywatne pulpity nawigacyjne.|
      | **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne** | Usuwa wszystkie linki do prywatnych pulpitów nawigacyjnych i innych ustawień niestandardowych, które zostały wprowadzone do portalu. |

> [!NOTE]
> Ze względu na dynamiczny charakter ustawień użytkownika i ryzyko uszkodzenia danych nie można importować ustawień z pliku *JSON* .
>
>

## <a name="next-steps"></a>Następne kroki

* [Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach](azure-portal-dashboard-share-access.md)
* [Dodawanie, usuwanie i zmiana rozmieszczenia ulubionych](azure-portal-add-remove-sort-favorites.md)
