---
title: Dodatkowe ustawienia interfejsu API platformy Azure dla usługi FHIR
description: Przegląd dodatkowych ustawień, które można ustawić dla interfejsu API platformy Azure dla FHIR
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 5de1d99442f307fc7850114915851f994258b537
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87845905"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Dodatkowe ustawienia interfejsu API platformy Azure dla usługi FHIR

W tym przewodniku krok po kroku zapoznajemy dodatkowe ustawienia, które możesz chcieć ustawić w interfejsie API platformy Azure dla usługi FHIR. Istnieją dodatkowe strony, które przechodzenia do bardziej szczegółowych informacji.

## <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych

Interfejs API platformy Azure dla usługi FHIR używa bazy danych do przechowywania danych. Wydajność podstawowej bazy danych zależy od liczby jednostek żądań wybranych podczas aprowizacji usług lub w ustawieniach bazy danych po zainicjowaniu obsługi administracyjnej usługi.

Obsługa przepływności musi być zapewniona, aby zapewnić, że dla bazy danych są dostępne wystarczające zasoby systemowe. Ile jednostek ru potrzebujesz dla aplikacji, zależy od wykonywanych operacji. Operacje mogą być różne od prostych operacji odczytu i zapisu do bardziej złożonych zapytań.

Aby uzyskać więcej informacji na temat zmiany ustawień domyślnych, zobacz [Konfigurowanie ustawień bazy danych](configure-database.md).

## <a name="access-control"></a>Kontrola dostępu

Interfejs API platformy Azure dla usługi FHIR będzie zezwalał tylko autoryzowanym użytkownikom na dostęp do interfejsu API FHIR. Autoryzowanych użytkowników można skonfigurować przy użyciu dwóch różnych mechanizmów. Podstawowym i zalecanym sposobem konfiguracji kontroli dostępu jest użycie [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/), która jest dostępna za pośrednictwem bloku **kontroli dostępu (IAM)** . Usługa Azure RBAC działa tylko wtedy, gdy chcesz zabezpieczyć dostęp do płaszczyzny danych za pomocą dzierżawy Azure Active Directory skojarzonej z Twoją subskrypcją. Jeśli chcesz użyć innej dzierżawy, interfejs API platformy Azure dla FHIR oferuje mechanizm kontroli dostępu lokalnego FHIR płaszczyzny danych. Opcje konfiguracji nie są tak rozbudowane, gdy korzystasz z lokalnego mechanizmu RBAC. Aby uzyskać szczegółowe informacje, wybierz jedną z następujących opcji:

* [Azure RBAC dla płaszczyzny danych FHIR](configure-azure-rbac.md). Jest to preferowana opcja w przypadku korzystania z dzierżawy Azure Active Directory skojarzonej z Twoją subskrypcją.
* [Kontrola dostępu do lokalnej płaszczyzny danych FHIR](configure-local-rbac.md). Tej opcji należy używać tylko wtedy, gdy konieczne jest użycie zewnętrznej dzierżawy Azure Active Directory na potrzeby kontroli dostępu do płaszczyzny danych. 

## <a name="enable-diagnostic-logging"></a>Włączanie rejestrowania diagnostycznego
Możesz włączyć rejestrowanie diagnostyczne w ramach konfiguracji, aby umożliwić monitorowanie usługi i precyzyjne raportowanie do celów zgodności. Aby uzyskać szczegółowe informacje na temat konfigurowania rejestrowania diagnostycznego, zapoznaj się z naszymi [przewodnikami](enable-diagnostic-logging.md) dotyczącymi sposobu konfigurowania rejestrowania diagnostycznego oraz niektórych przykładowych zapytań. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Używanie nagłówków niestandardowych do dodawania danych do dzienników inspekcji
W interfejsie API platformy Azure dla FHIR możesz chcieć uwzględnić dodatkowe informacje w dziennikach, które pochodzą z systemu wywołującego. Aby to zrobić, możesz użyć niestandardowych nagłówków.

Możesz użyć niestandardowych nagłówków do przechwytywania kilku typów informacji. Na przykład:

* Informacje o tożsamości lub autoryzacji
* Pochodzenie obiektu wywołującego
* Organizacja źródłowa
* Szczegóły systemu klienta (elektroniczny rekord kondycji, Portal pacjenta)

Aby dodać te dane do dzienników inspekcji, zapoznaj się z tematem [Używanie niestandardowych nagłówków HTTP w celu dodawania danych do dzienników inspekcji](use-custom-headers.md) .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku jak można skonfigurować dodatkowe ustawienia dla interfejsu API platformy Azure dla FHIR.

Następnie zapoznaj się z serią samouczków, aby utworzyć aplikację sieci Web, która odczytuje dane FHIR.

>[!div class="nextstepaction"]
>[Wdrażanie aplikacji JavaScript](tutorial-web-app-fhir-server.md)