---
title: Zapoznaj się z opiniami dotyczącymi ofert Azure Apps — Microsoft Commercial Marketplace
description: Jak obsłużyć opinie dotyczące oferty aplikacji platformy Azure w zespole Microsoft Azure Marketplaceego przeglądu. Możesz uzyskać dostęp do opinii w usłudze Azure DevOps przy użyciu poświadczeń Centrum partnerskiego.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: aecc64c5fa923bc29d34efd3969c2c12f30153c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320106"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Obsługa opinii na temat przeglądu ofert aplikacji platformy Azure

W tym artykule wyjaśniono, jak uzyskać dostęp do opinii z zespołu Microsoft Azure Marketplace przegląd w [usłudze Azure DevOps](https://azure.microsoft.com/services/devops/). Jeśli w ramach kroku **przeglądu firmy Microsoft** zostaną znalezione problemy krytyczne, możesz zalogować się do tego systemu, aby wyświetlić szczegółowe informacje o tych problemach (Przejrzyj informacje zwrotne). Po rozwiązaniu wszystkich problemów musisz ponownie przesłać ofertę, aby nadal publikować ją w witrynie Azure Marketplace. Na poniższym diagramie przedstawiono sposób, w jaki proces opinii odnosi się do procesu publikowania.

![Przejrzyj proces opinii](./media/review-feedback-process.png)

Zazwyczaj do przeglądu problemów odwołuje się odwołanie do żądania ściągnięcia. Wszystkie żądania ściągnięcia są połączone z elementem online Azure DevOps, który zawiera szczegółowe informacje o problemie. Na poniższej ilustracji przedstawiono przykład środowiska Centrum partnerskiego w przypadku znalezienia problemów podczas przeprowadzania przeglądu. 

![Stan publikowania](./media/publishing-status.png)

Żądanie ściągnięcia zawierające szczegółowe informacje o przekazywaniu zostanie wymienione w łączu "Wyświetl raport o certyfikacji". W przypadku złożonych sytuacji zespoły przeglądające i pomocy technicznej mogą również wysłać wiadomość e-mail.

## <a name="azure-devops-access"></a>Dostęp do usługi Azure DevOps

Wszyscy użytkownicy z dostępem do roli "deweloper" w centrum partnerskim będą mieli dostęp do wyświetlania elementów żądania ściągnięcia, do których odwołuje się opinia przegląd.

## <a name="reviewing-the-pull-request"></a>Przeglądanie żądania ściągnięcia

Aby przejrzeć problemy udokumentowane w żądaniu ściągnięcia, należy wykonać poniższą procedurę.

1. W sekcjach **Microsoft Recenzja** formularza kroki publikowania wybierz link żądania ściągnięcia, aby uruchomić przeglądarkę, i przejdź do strony **Przegląd** (Strona główna) dla tego żądania ściągnięcia. Na poniższej ilustracji przedstawiono przykładową stronę główną problemu krytycznego dla oferty przykładowej aplikacji contoso. Ta strona zawiera przydatne informacje podsumowujące dotyczące problemów z przeglądem znalezionych w aplikacji platformy Azure.

    [![Strona główna żądania ściągnięcia](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kliknij ten obraz, aby rozwinąć.*

1. Obowiązkowe Po prawej stronie okna, w sekcji **zasady**, wybierz komunikat problemu (w tym przykładzie: **Sprawdzanie poprawności zasad nie powiodło**się), aby zbadać szczegóły tego problemu, w tym skojarzone pliki dziennika. Błędy są zwykle wyświetlane w dolnej części plików dziennika.

1. W menu po lewej stronie strony głównej wybierz pozycję **pliki** , aby wyświetlić listę plików wchodzących w skład zasobów technicznych dla tej oferty. Recenzenci firmy Microsoft powinni dodać komentarze opisujące wykryte problemy krytyczne. W poniższym przykładzie wykryto dwa problemy.

    [![Strona główna żądania ściągnięcia](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kliknij ten obraz, aby rozwinąć.*

1. Zaznacz każdy węzeł komentarza w drzewie po lewej stronie, aby przejść do komentarza w kontekście otaczającego kodu. Popraw kod źródłowy w projekcie zespołu, aby rozwiązać problem opisany przez komentarz.

>[!Note]
>Nie możesz edytować zasobów technicznych swojej oferty w środowisku usługi Azure DevOps dla zespołu. W przypadku wydawców jest to środowisko tylko do odczytu dla zawartego kodu źródłowego. Można jednak zostawić odpowiedzi na komentarze, aby skorzystać z zespołu ds. przeglądu firmy Microsoft.

   W poniższym przykładzie Wydawca sprawdził, poprawił i odpowiedział na pierwszy problem.

   ![Pierwsza poprawka i odpowiedź komentarza](./media/first-comment-reply.png)

## <a name="next-steps"></a>Następne kroki

Po usunięciu krytycznych problemów udokumentowanych w przeglądzie żądania ściągnięcia należy [ponownie opublikować ofertę aplikacji platformy Azure](./create-new-azure-apps-offer.md#publish).
