---
title: Szczegóły dotyczące identyfikatora dzierżawy, identyfikatora obiektu i powiązania partnera w portalu Azure Marketplace
description: Jak znaleźć szczegóły identyfikatora dzierżawy, identyfikatora obiektu i powiązania partnera w witrynie Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: c35e42aaf5e4bd31a54f807969c3671ecc5668ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91814451"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Znajdź identyfikator dzierżawy, identyfikator obiektu i Szczegóły powiązania partnera

W tym artykule opisano, jak znaleźć identyfikator dzierżawy, identyfikator obiektu i Szczegóły powiązania partnera wraz z odpowiednimi identyfikatorami subskrypcji.

Jeśli chcesz uzyskać zrzuty ekranu tych elementów w Azure Cloud Shell, które mają być używane na potrzeby debugowania, przejdź do obszaru [Znajdowanie powiązania dzierżawy, obiektu i identyfikatora partnera na potrzeby debugowania](#find-ids-for-debugging).

>[!Note]
> Tylko właściciel subskrypcji ma uprawnienia do wykonania tych kroków.

## <a name="find-tenant-id"></a>Znajdź identyfikator dzierżawy

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com/).
2. Wybierz pozycję **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

3. Wybierz pozycję **grupy**. Identyfikator dzierżawy znajduje się w polu **Informacje o dzierżawie** .

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

## <a name="find-subscriptions-and-roles"></a>Znajdź subskrypcje i role

1. Przejdź do Azure Portal i wybierz **Azure Active Directory** zgodnie z opisem w krokach 1 i 2 powyżej.
2. Wybierz pozycję **Subskrypcje**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

3. Wyświetl subskrypcje i role.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

## <a name="find-partner-id"></a>Znajdź identyfikator partnera

1. Przejdź do strony subskrypcje zgodnie z opisem w poprzedniej sekcji.
2. Wybierz subskrypcję.
3. W obszarze **rozliczenia**wybierz pozycję **Informacje o partnerze**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

## <a name="find-user-object-id"></a>Znajdź użytkownika (identyfikator obiektu)

1. Zaloguj się do [portalu administracyjnego pakietu Office 365](https://portal.office.com/adminportal/home) przy użyciu konta w odpowiedniej dzierżawie z odpowiednimi prawami administracyjnymi.
2. W menu po lewej stronie rozwiń sekcję **centra administracyjne** u dołu, a następnie wybierz opcję Azure Active Directory, aby uruchomić konsolę administracyjną w nowym oknie przeglądarki.
3. Wybierz pozycję **Użytkownicy**.
4. Wyszukaj żądanego użytkownika lub wyszukaj go, a następnie wybierz nazwę konta, aby wyświetlić informacje o profilu konta użytkownika.
5. Identyfikator obiektu znajduje się w sekcji tożsamość po prawej stronie.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

6. Znajdź **przypisania ról** , wybierając pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie, a następnie pozycję **przypisania ról**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

## <a name="find-ids-for-debugging"></a>Znajdowanie identyfikatorów na potrzeby debugowania

W tej sekcji opisano, jak znaleźć skojarzenie dzierżawy, obiektu i identyfikatora partnera na potrzeby debugowania.

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com/).
2. Otwórz Azure Cloud Shell, wybierając ikonę programu PowerShell w prawym górnym rogu.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

3. Wybierz pozycję **PowerShell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

4. Wybierz pole **subskrypcja** , z którym ma zostać połączony partner, a następnie **Utwórz magazyn**. Jest to jednorazowa akcja; Jeśli masz już skonfigurowany magazyn, przejdź do następnego kroku.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

5. Utworzenie magazynu (lub już jego) spowoduje otwarcie okna Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

6. Aby uzyskać szczegółowe informacje o skojarzeniu partnera, zainstaluj rozszerzenie za pomocą tego polecenia:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell będzie mieć uwagę, jeśli rozszerzenie jest już zainstalowane:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

7. Sprawdź szczegóły partnera przy użyciu tego polecenia:<br>`az managementpartner show --partner-id xxxxxx`<br>Przykład: `az managementpartner show --partner-id 4760962`.<br>Przyciągnij zrzut ekranu wyników poleceń, który będzie wyglądać podobnie do tego:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Ikona Azure Active Directory w Azure Portal.":::

>[!NOTE]
>Jeśli wiele subskrypcji wymaga zrzutu ekranu, użyj tego polecenia, aby przełączać się między subskrypcjami:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Następne kroki

- [Obsługiwane kraje i regiony](sell-from-countries.md)