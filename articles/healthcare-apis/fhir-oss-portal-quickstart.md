---
title: 'Azure Portal: Wdróż serwer Open Source FHIR na platformie Azure — interfejs API platformy Azure dla FHIR'
description: Ten przewodnik Szybki Start wyjaśnia, jak wdrożyć serwer Microsoft Open Source FHIR przy użyciu Azure Portal.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5109c9a7c6432e42c6841b89cc28bde3e92c74aa
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "84820186"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Szybki Start: Wdrażanie serwera FHIR "open source" przy użyciu Azure Portal

W tym przewodniku szybki start dowiesz się, jak wdrożyć serwer FHIR Open Source na platformie Azure przy użyciu Azure Portal. Będziemy używać prostych linków wdrażania w [repozytorium open source](https://github.com/Microsoft/fhir-server)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="github-open-source-repository"></a>Repozytorium open source w usłudze GitHub

Przejdź do [strony wdrożenia usługi GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) i Znajdź przyciski "wdróż na platformie Azure":

![Strona wdrożenia Open Source](media/quickstart-oss-portal/deployment-page-oss.png)

Kliknij przycisk wdrożenia i Otwórz Azure Portal.

## <a name="fill-in-deployment-parameters"></a>Wypełnij parametry wdrożenia

Wybierz, aby utworzyć nową grupę zasobów i nadaj jej nazwę. Tylko inny wymagany parametr jest nazwą usługi.

![Niestandardowe parametry wdrożenia](media/quickstart-oss-portal/deployment-custom-parameters.png)

Należy zauważyć, że wdrożenie pobierze kod źródłowy bezpośrednio z repozytorium "open source" w witrynie GitHub. Jeśli masz rozwidlenie repozytorium, możesz wskazać swoje własne dla i konkretnego rozgałęzienia.

Po wypełnieniu szczegółów możesz rozpocząć wdrażanie.

## <a name="validate-fhir-server-is-running"></a>Sprawdź, czy serwer FHIR jest uruchomiony

Po zakończeniu wdrażania możesz wskazać przeglądarce, aby `https://SERVICENAME.azurewebsites.net/metadata` uzyskać instrukcję możliwości. Potrwa to chwilę, zanim serwer zacznie reagować po raz pierwszy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów zawierającą zasoby, które zostały zainicjowane, wybierz pozycję **Usuń grupę zasobów**, a następnie Potwierdź nazwę grupy zasobów do usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer Microsoft Open Source FHIR dla platformy Azure w ramach subskrypcji. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API FHIR przy użyciu programu Poster, zapoznaj się z samouczkiem dotyczącym programu.
 
>[!div class="nextstepaction"]
>[Dostęp do interfejsu API FHIR za pomocą programu Poster](access-fhir-postman-tutorial.md)