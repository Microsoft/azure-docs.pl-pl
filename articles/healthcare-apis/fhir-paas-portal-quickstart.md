---
title: 'Szybki Start: wdrażanie interfejsu API platformy Azure dla usługi FHIR przy użyciu Azure Portal'
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć usługę Azure API for FHIR i skonfigurować ustawienia przy użyciu Azure Portal.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023469"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Szybki Start: wdrażanie interfejsu API platformy Azure dla usługi FHIR przy użyciu Azure Portal

W tym przewodniku szybki start dowiesz się, jak wdrożyć usługę Azure API for FHIR przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-new-resource"></a>Utwórz nowy zasób

Otwórz [Azure Portal](https://portal.azure.com) a następnie kliknij pozycję **Utwórz zasób** .

![Tworzenie zasobu](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Wyszukaj usługę Azure API for FHIR

Usługę Azure API for FHIR można znaleźć, wpisując ciąg "FHIR" w polu wyszukiwania:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Wyszukaj interfejsy API usługi opieki zdrowotnej":::

## <a name="create-azure-api-for-fhir-account"></a>Tworzenie konta interfejsu API platformy Azure dla usługi FHIR

Wybierz pozycję **Utwórz** , aby utworzyć nowe konto interfejsu API platformy Azure dla usługi FHIR:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Tworzenie konta interfejsu API platformy Azure dla usługi FHIR":::

## <a name="enter-account-details"></a>Wprowadź szczegóły konta

Wybierz istniejącą grupę zasobów lub Utwórz nową, wybierz nazwę konta, a na koniec kliknij pozycję **Przegląd + Utwórz**:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Nowe szczegóły dotyczące interfejsu API usługi opieki zdrowotnej":::

Potwierdź utworzenie i oczekiwanie na wdrożenie interfejsu API FHIR.

## <a name="additional-settings-optional"></a>Ustawienia dodatkowe (opcjonalnie)

Możesz również kliknąć przycisk **Dalej: Ustawienia dodatkowe** , aby wyświetlić ustawienia uwierzytelniania. Domyślną konfiguracją interfejsu API platformy Azure dla usługi FHIR jest [użycie funkcji RBAC platformy Azure do przypisywania ról płaszczyzny danych](configure-azure-rbac.md). Po skonfigurowaniu w tym trybie "urząd" dla usługi FHIR zostanie ustawiony na dzierżawę Azure Active Directory subskrypcji:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Domyślne ustawienia uwierzytelniania":::

Należy zauważyć, że pole umożliwiające wprowadzanie dozwolonych identyfikatorów obiektów jest wyszarzone, ponieważ w tym przypadku korzystamy z usługi Azure RBAC do konfigurowania przypisań ról.

Jeśli chcesz skonfigurować usługę FHIR do korzystania z dzierżawy zewnętrznej lub pomocniczej Azure Active Directory, możesz zmienić Urząd i wprowadzić identyfikatory obiektów dla użytkowników i grup, które powinny mieć dostęp do serwera. Aby uzyskać więcej informacji, zobacz Przewodnik po [konfiguracji lokalnej kontroli RBAC](configure-local-rbac.md) .

## <a name="fetch-fhir-api-capability-statement"></a>Pobieranie instrukcji obsługi interfejsu API FHIR

Aby sprawdzić, czy nowe konto interfejsu API FHIR jest inicjowane, Pobierz instrukcję możliwości, wskazując przeglądarkę na `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, usługa Azure API dla FHIR i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów zawierającą konto interfejsu API platformy Azure dla usługi FHIR, wybierz pozycję **Usuń grupę zasobów**, a następnie Potwierdź nazwę grupy zasobów do usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono interfejs API platformy Azure dla usługi FHIR w ramach subskrypcji. Aby ustawić dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR, należy przejoć do dodatkowych ustawień przewodnika. Jeśli wszystko jest gotowe do rozpoczęcia korzystania z interfejsu API platformy Azure dla usługi FHIR, Przeczytaj więcej na temat sposobu rejestrowania aplikacji.

>[!div class="nextstepaction"]
>[Dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Przegląd rejestracji aplikacji](fhir-app-registration.md)
