---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 137aca7c6c857ee6e833c359b710e1c1848d15ed
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562822"
---
## <a name="deleting-personal-information"></a>Usuwanie danych osobowych

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Informacje osobiste dotyczą usługi Import/Export (za pośrednictwem portalu i interfejsu API) podczas operacji importowania i eksportowania. Dane używane w tych procesach obejmują:

- Nazwa kontaktu
- Numer telefonu
- Poczta e-mail
- Adres
- City (Miasto)
- Kod pocztowy
- Stan
- Kraj/Województwo/Region
- Identyfikator dysku
- Numer konta operatora
- Numer śledzenia dostawy

Po utworzeniu zadania importu/eksportu użytkownicy podają informacje kontaktowe i adres wysyłkowy. Dane osobowe są przechowywane w maksymalnie dwóch różnych lokalizacjach: w zadaniu i opcjonalnie w ustawieniach portalu. Informacje osobiste są przechowywane tylko w ustawieniach portalu po zaznaczeniu pola wyboru z etykietą **Zapisz operatora i adres zwrotny jako domyślne** w sekcji *zwrotne informacje o wysyłce* procesu eksportu.

Osobiste informacje kontaktowe można usunąć w następujący sposób:

- Dane zapisane w ramach zadania są usuwane z zadania. Użytkownicy mogą usuwać zadania ręcznie i zadania zakończone są automatycznie usuwane po 90 dniach. Zadania można usunąć ręcznie za pośrednictwem interfejsu API REST lub Azure Portal. Aby usunąć zadanie w Azure Portal, przejdź do zadania importowania/eksportowania, a następnie kliknij przycisk *Usuń* na pasku poleceń. Aby uzyskać szczegółowe informacje na temat usuwania zadania importu/eksportu za pośrednictwem interfejsu API REST, zobacz [usuwanie zadania importu/eksportu](/previous-versions/azure/storage/common/storage-import-export-cancelling-and-deleting-jobs).

- Informacje kontaktowe zapisane w ustawieniach portalu można usunąć, usuwając ustawienia portalu. Ustawienia portalu można usunąć, wykonując następujące czynności:
  - Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
  - Kliknij ikonę *ustawień* ikona ustawień ![ platformy Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kliknij pozycję *Eksportuj wszystkie ustawienia* (aby zapisać bieżące ustawienia do `.json` pliku).
  - Kliknij pozycję *Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne* , aby usunąć wszystkie ustawienia, w tym zapisane informacje kontaktowe.

Aby uzyskać więcej informacji, zapoznaj się z zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter)