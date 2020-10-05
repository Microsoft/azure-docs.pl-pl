---
title: 'Azure Portal: Wdróż serwer Open Source FHIR na platformie Azure — interfejs API platformy Azure dla FHIR'
description: Ten przewodnik Szybki Start wyjaśnia, jak wdrożyć serwer Microsoft Open Source FHIR przy użyciu Azure Portal.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90978574"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Szybki Start: Wdrażanie serwera FHIR "open source" przy użyciu Azure Portal

W tym przewodniku szybki start dowiesz się, jak wdrożyć serwer FHIR Open Source na platformie Azure przy użyciu Azure Portal. Będziemy używać prostych linków wdrażania w [repozytorium open source](https://github.com/Microsoft/fhir-server)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="github-open-source-repository"></a>Repozytorium open source w usłudze GitHub

Przejdź do [strony wdrożenia usługi GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) i Znajdź przyciski "wdróż na platformie Azure":

>[!div class="mx-imgBorder"]
>![Strona wdrożenia Open Source](media/quickstart-oss-portal/deployment-page-oss.png)

Kliknij przycisk wdrożenia i Otwórz Azure Portal.

## <a name="fill-in-deployment-parameters"></a>Wypełnij parametry wdrożenia

Wybierz, aby utworzyć nową grupę zasobów i nadaj jej nazwę. Tylko inne wymagane parametry są nazwą usługi i hasłem administratora SQL.

>[!div class="mx-imgBorder"]
>![Niestandardowe parametry wdrożenia](media/quickstart-oss-portal/deployment-custom-parameters.png)

Po wypełnieniu szczegółów możesz rozpocząć wdrażanie.

## <a name="validate-fhir-server-is-running"></a>Sprawdź, czy serwer FHIR jest uruchomiony

Po zakończeniu wdrażania możesz wskazać przeglądarce, aby `https://SERVICENAME.azurewebsites.net/metadata` uzyskać instrukcję możliwości. Potrwa to chwilę, zanim serwer zacznie reagować po raz pierwszy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów zawierającą zasoby, które zostały zainicjowane, wybierz pozycję **Usuń grupę zasobów**, a następnie Potwierdź nazwę grupy zasobów do usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer Microsoft Open Source FHIR dla platformy Azure w ramach subskrypcji. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API FHIR przy użyciu programu Poster, zapoznaj się z samouczkiem dotyczącym programu.
 
>[!div class="nextstepaction"]
>[Dostęp do interfejsu API FHIR za pomocą programu Poster](access-fhir-postman-tutorial.md)