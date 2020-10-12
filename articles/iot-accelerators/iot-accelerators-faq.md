---
title: Akceleratory rozwiązań IoT — często zadawane pytania — Azure | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące akceleratorów rozwiązań IoT. Zawiera linki do repozytoriów usługi GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: abab80f8c790a170ec30fa228dd86576079a73a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462551"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Często zadawane pytania dotyczące akceleratorów rozwiązań IoT

Zobacz również często zadawane [pytania powiązane z konkretną fabryką](iot-accelerators-faq-cf.md) i [często zadawane pytania dotyczące monitorowania zdalnego](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Gdzie mogę znaleźć kod źródłowy akceleratorów rozwiązań?

Kod źródłowy jest przechowywany w następujących repozytoriach usługi GitHub:

* [Akcelerator rozwiązań do zdalnego monitorowania (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Akcelerator rozwiązań zdalnego monitorowania (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Akcelerator rozwiązania do konserwacji predykcyjnej](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Akcelerator rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jakich zestawów SDK można używać do tworzenia klientów urządzeń dla akceleratorów rozwiązań?

Linki do różnych zestawów SDK urządzeń (C, .NET, Java, Node.js, Python) IoT można znaleźć w Microsoft Azure repozytoriów GitHub [zestawów SDK usługi IoT](https://github.com/Azure/azure-iot-sdks) .

Jeśli używasz urządzenia DevKit, możesz znaleźć zasoby i przykłady w repozytorium usługi [IoT DEVKIT SDK](https://github.com/Microsoft/devkit-sdk) w witrynie GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Czy nowa architektura mikrousług jest dostępna dla wszystkich trzech akceleratorów rozwiązań?

Obecnie tylko rozwiązanie do monitorowania zdalnego używa architektury mikrousług, ponieważ obejmuje to najszersze scenariusze.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jakie korzyści oferuje Nowa architektura oparta na mikrousługach w ramach usługi Open Source?

W ciągu ostatnich dwóch lat architektura chmury została znacznie rozwijająca się. Mikrousługi okazały się doskonałym wzorcem do osiągnięcia skalowalności i elastyczności, bez obniżania szybkości tworzenia oprogramowania. Ten wzorzec architektury jest używany w kilku usługach firmy Microsoft wewnętrznie z doskonałymi wynikami niezawodności i skalowalności. Firma Microsoft wprowadza te informacje do ćwiczeń w akceleratorach rozwiązań, aby klienci mogli z nich korzystać.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi i chcę zmienić mapowanie katalogu między subskrypcją a konkretną dzierżawą usługi Azure AD. Jak mogę wykonać to zadanie?

Zobacz [, aby dodać istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Chcę zmienić administratora usługi lub Co-Administrator po zalogowaniu się przy użyciu konta organizacyjnego

Zobacz artykuł pomocy technicznej [Zmiana administratora usługi i Co-Administrator po zalogowaniu się przy użyciu konta organizacyjnego](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego widzę ten błąd? "Twoje konto nie ma odpowiednich uprawnień do utworzenia rozwiązania. Skontaktuj się z administratorem konta lub spróbuj użyć innego konta.

Zapoznaj się z poniższym diagramem, aby uzyskać wskazówki:

![Schemat blokowy uprawnień](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Jeśli ten błąd będzie nadal występować po zweryfikowaniu, jesteś administratorem globalnym dzierżawy usługi Azure AD i współadministratorem subskrypcji, skontaktuj się z administratorem konta, aby usunąć użytkownika i ponownie przypisać wymagane uprawnienia w tej kolejności. Najpierw Dodaj użytkownika jako administratora globalnego, a następnie Dodaj użytkownika jako współadministratora subskrypcji platformy Azure. Jeśli problemy będą nadal występować, skontaktuj się z pomocą [techniczną &](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego widzę ten błąd, jeśli mam subskrypcję platformy Azure? "Subskrypcja platformy Azure jest wymagana do utworzenia wstępnie skonfigurowanych rozwiązań. Możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut ".

Jeśli masz pewność, że masz subskrypcję platformy Azure, sprawdź poprawność mapowania dzierżawy dla subskrypcji i sprawdź, czy na liście rozwijanej wybrano poprawną dzierżawę. Jeśli dzierżawa jest poprawna, wykonaj poprzedni diagram i sprawdź poprawność mapowania subskrypcji i tej dzierżawy usługi Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Gdzie można znaleźć informacje o poprzedniej wersji rozwiązania do zdalnego monitorowania?

Poprzednia wersja akceleratora rozwiązania do zdalnego monitorowania była znana jako IoT Suite wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego. Zarchiwizowaną dokumentację można znaleźć pod adresem [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/) .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Czy nowy akcelerator rozwiązania jest dostępny w tym samym regionie geograficznym co istniejące rozwiązanie?

Tak, nowe zdalne monitorowanie jest dostępne w tych samych regionach geograficznych.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaka jest różnica między usuwaniem grupy zasobów w Azure Portal a kliknięciem przycisku Usuń w akceleratorze rozwiązania w azureiotsolutions.com?

* Po usunięciu akceleratora rozwiązania w [azureiotsolutions.com](https://www.azureiotsolutions.com/)należy usunąć wszystkie zasoby, które zostały wdrożone podczas tworzenia akceleratora rozwiązania. W przypadku dodania dodatkowych zasobów do grupy zasobów te zasoby również zostaną usunięte.
* Po usunięciu grupy zasobów w [Azure Portal](https://portal.azure.com)można usunąć tylko zasoby w tej grupie zasobów. Należy również usunąć aplikację Azure Active Directory skojarzoną z akceleratorem rozwiązania.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Czy mogę nadal korzystać z istniejących inwestycji w Akceleratory rozwiązań usługi Azure IoT?

Tak. Każde rozwiązanie, które już istnieje, nadal działa w ramach subskrypcji platformy Azure, a kod źródłowy pozostaje dostępny w serwisie GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień IoT Hub można zainicjować w ramach subskrypcji?

Domyślnie można udostępnić [10 centrów IoT na subskrypcję](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Aby zgłosić ten limit, możesz utworzyć [bilet pomocy technicznej systemu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . W związku z tym, ponieważ każdy akcelerator rozwiązania Inicjuje nowe IoT Hub, można udostępnić maksymalnie 10 akceleratorów rozwiązań w danej subskrypcji.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień Azure Cosmos DB można zainicjować w ramach subskrypcji?

50. Możesz utworzyć [bilet pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby zgłosić ten limit, ale domyślnie można udostępnić tylko 50 wystąpień Cosmos DB na subskrypcję.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. W ramach subskrypcji platformy Azure można utworzyć tylko dwie mapy Bing na poziomie 1 dla przedsiębiorstw. Zdalne rozwiązanie do monitorowania jest obsługiwane domyślnie z planem wewnętrznej transakcji na poziomie 1. W związku z tym można udostępnić maksymalnie dwa rozwiązania do zdalnego monitorowania w ramach subskrypcji bez żadnych modyfikacji.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Czy mogę utworzyć Akcelerator rozwiązania, jeśli mam Microsoft Azure dla DreamSpark?

> [!NOTE]
> Microsoft Azure dla DreamSpark jest teraz znany jako Microsoft Imagine dla studentów.

Obecnie nie można utworzyć akceleratora rozwiązania za pomocą [Microsoft Azure dla konta DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) . Możesz jednak utworzyć [bezpłatne konto wersji próbnej dla platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, które umożliwią utworzenie akceleratora rozwiązań.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak mogę usunąć dzierżawę usługi Azure AD?

Zobacz przewodnik po wpisie w blogu Eric golpe [, aby usunąć dzierżawę usługi Azure AD](https://docs.microsoft.com/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości akceleratora rozwiązania do monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)
* [Omówienie akceleratora rozwiązania do konserwacji predykcyjnej](iot-accelerators-predictive-overview.md)
* [Wdróż Akcelerator rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
