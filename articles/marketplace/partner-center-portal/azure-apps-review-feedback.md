---
title: Obsługa opinii na temat przeglądu oferty Azure Apps w komercyjnej witrynie Marketplace
description: Jak obsłużyć opinię na temat przeglądu oferty usługi Azure Apps w celu uzyskania listy lub sprzedaży w witrynie Azure Marketplace, AppSource lub za pośrednictwem programu Cloud Solution Provider (CSP) przy użyciu portalu Marketplace w witrynie Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279797"
---
# <a name="handling-review-feedback"></a>Obsługa opinii dla przeglądu

W tym artykule wyjaśniono, jak uzyskać dostęp do środowiska Azure DevOps, które jest używane przez zespół przegląd Microsoft Azure Marketplace. Jeśli w ramach kroku **przeglądu firmy Microsoft** zostaną znalezione problemy krytyczne, możesz zalogować się do tego systemu, aby wyświetlić szczegółowe informacje o tych problemach (Przejrzyj informacje zwrotne). Po naprawieniu wszystkich tych problemów musisz ponownie przesłać swoją ofertę, aby nadal publikować ją w portalu Azure Marketplace. Na poniższym diagramie przedstawiono sposób, w jaki proces opinii odnosi się do procesu publikowania.

![Przejrzyj proces opinii](./media/review-feedback-process.png)

Zazwyczaj do przeglądu problemów odwołuje się żądanie ściągnięcia. Wszystkie żądania ściągnięcia są połączone z elementem online [Azure DevOps](https://azure.microsoft.com/services/devops/) (wcześniej o Visual Studio Team Services nazwie VSTS), który zawiera szczegółowe informacje o problemie. Na poniższej ilustracji przedstawiono przykład środowiska Centrum partnerskiego w przypadku znalezienia problemów podczas przeprowadzania przeglądu. 

![Stan publikowania](./media/publishing-status.png)

Żądanie ściągnięcia zawierające szczegółowe informacje o przekazywaniu zostanie wymienione w łączu "Wyświetl raport o certyfikacji". W przypadku złożonych sytuacji zespoły przeglądające i pomocy technicznej mogą również wysłać wiadomość e-mail.

## <a name="azure-devops-access"></a>Dostęp do usługi Azure DevOps

Wszyscy użytkownicy z dostępem do roli "deweloper" w centrum partnerskim będą mieli dostęp do wyświetlania elementów żądania ściągnięcia, do których odwołuje się opinia przegląd.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Przeglądanie żądania ściągnięcia

Aby przejrzeć problemy udokumentowane w żądaniu ściągnięcia, należy wykonać poniższą procedurę.

1. W sekcjach **Microsoft Recenzja** formularza publikowanie kroków kliknij link żądania ściągnięcia, aby uruchomić przeglądarkę, i przejdź do strony **Przegląd** (Strona główna) dla tego żądania ściągnięcia. Na poniższej ilustracji przedstawiono przykładową stronę główną problemu krytycznego dla oferty przykładowej aplikacji contoso. Ta strona zawiera przydatne informacje podsumowujące dotyczące problemów z przeglądem znalezionych w aplikacji platformy Azure.

    [![Strona główna żądania ściągnięcia](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kliknij obraz, aby rozwinąć.*

1. Obowiązkowe Po prawej stronie okna, w sekcji **zasady**, kliknij komunikat problemu (w tym przykładzie: **Sprawdzanie poprawności zasad nie powiodło**się), aby zbadać szczegóły tego problemu, w tym skojarzone pliki dziennika. Błędy są zwykle wyświetlane w dolnej części plików dziennika.
1. W menu po lewej stronie strony głównej wybierz pozycję **pliki** , aby wyświetlić listę plików wchodzących w skład zasobów technicznych dla tej oferty. Recenzenci firmy Microsoft powinni dodać komentarze opisujące wykryte problemy krytyczne. W poniższym przykładzie wykryto dwa problemy.

    [![Strona główna żądania ściągnięcia](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kliknij obraz, aby rozwinąć.*

1. Kliknij każdy węzeł komentarza w drzewie po lewej stronie, aby przejść do komentarza w kontekście otaczającego kodu. Popraw kod źródłowy w projekcie zespołu, aby rozwiązać problem opisany przez komentarz.

>[!Note]
>Nie możesz edytować zasobów technicznych swojej oferty w środowisku usługi Azure DevOps dla zespołu. W przypadku wydawców jest to środowisko tylko do odczytu dla zawartego kodu źródłowego. Można jednak zostawić odpowiedzi na komentarze, aby skorzystać z zespołu ds. przeglądu firmy Microsoft.

   W poniższym przykładzie Wydawca sprawdził, poprawił i odpowiedział na pierwszy problem.

   ![Pierwsza poprawka i odpowiedź komentarza](./media/first-comment-reply.png)

## <a name="next-steps"></a>Następne kroki

Po usunięciu krytycznych problemów udokumentowanych w przeglądzie żądania ściągnięcia należy [ponownie opublikować ofertę aplikacji platformy Azure](./create-new-azure-apps-offer.md#publish).
