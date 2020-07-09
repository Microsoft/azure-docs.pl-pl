---
title: 'Szybki Start: wdrażanie interfejsu API platformy Azure dla usługi FHIR przy użyciu Azure Portal'
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć usługę Azure API for FHIR i skonfigurować ustawienia przy użyciu Azure Portal.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e729597e9d83c4e6096fe52b577b052d94ca4799
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "84820252"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Szybki Start: wdrażanie interfejsu API platformy Azure dla usługi FHIR przy użyciu Azure Portal

W tym przewodniku szybki start dowiesz się, jak wdrożyć usługę Azure API for FHIR przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-new-resource"></a>Utwórz nowy zasób

Otwórz [Azure Portal](https://portal.azure.com) a następnie kliknij pozycję **Utwórz zasób** .

![Tworzenie zasobu](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Wyszukaj usługę Azure API for FHIR

Usługę Azure API for FHIR można znaleźć, wpisując ciąg "FHIR" w polu wyszukiwania:

![Wyszukaj interfejsy API usługi opieki zdrowotnej](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Tworzenie konta interfejsu API platformy Azure dla usługi FHIR

Wybierz pozycję **Utwórz** , aby utworzyć nowe konto interfejsu API platformy Azure dla usługi FHIR:

![Tworzenie konta interfejsu API platformy Azure dla usługi FHIR](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>Wprowadź szczegóły konta

Wybierz istniejącą grupę zasobów lub Utwórz nową, wybierz nazwę konta, a na koniec kliknij pozycję **Przegląd + Utwórz**:

![Nowe szczegóły dotyczące interfejsu API usługi opieki zdrowotnej](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

Potwierdź utworzenie i oczekiwanie na wdrożenie interfejsu API FHIR.

## <a name="additional-settings"></a>Ustawienia dodatkowe

Kliknij przycisk **Dalej: Ustawienia dodatkowe** , aby skonfigurować urząd, odbiorców, identyfikatory obiektów tożsamości, które powinny mieć dostęp do tego interfejsu API platformy Azure dla usługi FHIR, w razie potrzeby Włącz funkcję INTELIGENTNEj FHIR, a następnie skonfiguruj przepływność bazy danych:

- **Urząd:** Możesz określić inną dzierżawę usługi Azure AD od tej, do której użytkownik jest zalogowany jako urząd uwierzytelniania dla usługi.
- **Odbiorcy:** Najlepszym rozwiązaniem i ustawieniem domyślnym jest to, że odbiorcy są ustawiani na adres URL serwera FHIR. Można to zmienić w tym miejscu. Odbiorcy identyfikują odbiorcę, dla którego jest przeznaczony token. W tym kontekście powinna być ustawiona na coś reprezentującego interfejs API FHIR.
- **Dozwolone identyfikatory obiektów:** Można określić identyfikatory obiektów tożsamości, które powinny mieć dostęp do tego interfejsu API platformy Azure dla FHIR. Więcej informacji na temat znajdowania identyfikatora obiektu dla użytkowników i jednostek usługi znajduje się w przewodniku [Znajdowanie identyfikatorów obiektów tożsamości](find-identity-object-ids.md) .  
- **Inteligentny FHIR serwer proxy:** Można włączyć INTELIGENTNą FHIR serwer proxy. Aby uzyskać szczegółowe informacje na temat konfigurowania INTELIGENTNEgo serwera proxy FHIR zobacz samouczek [Azure API for FHIR Smart on FHIR proxy](https://docs.microsoft.com/azure/healthcare-apis/use-smart-on-fhir-proxy)  
- **Elastyczna przepływność (ru/s):** W tym miejscu możesz określić ustawienia przepływności dla źródłowej bazy danych dla interfejsu API platformy Azure dla FHIR. Można zmienić to ustawienie później w bloku baza danych. Aby uzyskać więcej informacji, zobacz stronę [Konfigurowanie ustawień bazy danych](configure-database.md) .


![Konfigurowanie dozwolonych identyfikatorów obiektów](media/quickstart-paas-portal/configure-audience.png)

## <a name="fetch-fhir-api-capability-statement"></a>Pobieranie instrukcji obsługi interfejsu API FHIR

Aby sprawdzić, czy nowe konto interfejsu API FHIR jest inicjowane, Pobierz instrukcję możliwości, wskazując przeglądarkę na `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, usługa Azure API dla FHIR i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów zawierającą konto interfejsu API platformy Azure dla usługi FHIR, wybierz pozycję **Usuń grupę zasobów**, a następnie Potwierdź nazwę grupy zasobów do usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono interfejs API platformy Azure dla usługi FHIR w ramach subskrypcji. Aby ustawić dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR, należy przejoć do dodatkowych ustawień przewodnika.

>[!div class="nextstepaction"]
>[Dodatkowe ustawienia w interfejsie API platformy Azure dla usługi FHIR](azure-api-for-fhir-additional-settings.md)
