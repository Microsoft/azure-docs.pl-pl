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
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903993"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Często zadawane pytania dotyczące akceleratorów rozwiązań IoT

Zobacz również [często zadawane pytania dotyczące połączonej fabryki](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Gdzie mogę znaleźć kod źródłowy akceleratorów rozwiązań?

Kod źródłowy jest przechowywany w następujących repozytoriach usługi GitHub:

* [Akcelerator rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory)
* [Akcelerator rozwiązania dla symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Gdzie mogę znaleźć Akceleratory rozwiązań do monitorowania zdalnego i konserwacji predykcyjnej?

Od 10 grudnia 2020 akceleratory zdalnego monitorowania i konserwacji predykcyjnej zostały usunięte z lokacji [akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators) i nie są już dostępne dla nowych wdrożeń. Repozytoria GitHub dla obu akceleratorów zostały zarchiwizowane. Kod jest nadal dostępny dla każdego, kto ma dostęp, ale repozytoria nie zajmują żadnych nowych wkładów.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Co się stanie z istniejącymi wdrożeniami zdalnego monitorowania i konserwacji predykcyjnej?

Istniejące wdrożenia nie podlegają usunięciu akceleratorów zdalnego monitorowania i konserwacji predykcyjnej i będą nadal działać. Nie wpływa to również na repozytoria w przypadku rozwidlenia. Wszystkie repozytoria główne w serwisie GitHub zostały zarchiwizowane.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Jak mogę wdrożyć Akcelerator rozwiązania dla symulacji urządzenia?

Aby wdrożyć Akcelerator rozwiązania do symulacji urządzeń, zobacz repozytorium GitHub dotyczące [symulacji urządzeń](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) .

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Gdzie można znaleźć informacje o usuniętych akceleratorach rozwiązań?

Zobacz następujące strony w poprzedniej wersji witryny:

* [Zdalne monitorowanie](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Konserwacja zapobiegawcza](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jakich zestawów SDK można używać do tworzenia klientów urządzeń dla akceleratorów rozwiązań?

Linki do różnych zestawów SDK urządzeń (C, .NET, Java, Node.js, Python) IoT można znaleźć w Microsoft Azure repozytoriów GitHub [zestawów SDK usługi IoT](https://github.com/Azure/azure-iot-sdks) .

Jeśli używasz urządzenia DevKit, możesz znaleźć zasoby i przykłady w repozytorium usługi [IoT DEVKIT SDK](https://github.com/Microsoft/devkit-sdk) w witrynie GitHub.

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

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaka jest różnica między usuwaniem grupy zasobów w Azure Portal a kliknięciem przycisku Usuń w akceleratorze rozwiązania w azureiotsolutions.com?

* Po usunięciu akceleratora rozwiązania w [azureiotsolutions.com](https://www.azureiotsolutions.com/)należy usunąć wszystkie zasoby, które zostały wdrożone podczas tworzenia akceleratora rozwiązania. W przypadku dodania dodatkowych zasobów do grupy zasobów te zasoby również zostaną usunięte.
* Po usunięciu grupy zasobów w [Azure Portal](https://portal.azure.com)można usunąć tylko zasoby w tej grupie zasobów. Należy również usunąć aplikację Azure Active Directory skojarzoną z akceleratorem rozwiązania.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Czy mogę nadal korzystać z istniejących inwestycji w Akceleratory rozwiązań usługi Azure IoT?

Tak. Każde rozwiązanie, które już istnieje, nadal działa w ramach subskrypcji platformy Azure, a kod źródłowy pozostaje dostępny w serwisie GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień IoT Hub można zainicjować w ramach subskrypcji?

Domyślnie można udostępnić [10 centrów IoT na subskrypcję](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Aby zgłosić ten limit, możesz utworzyć [bilet pomocy technicznej systemu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . W związku z tym, ponieważ każdy akcelerator rozwiązania Inicjuje nowe IoT Hub, można udostępnić maksymalnie 10 akceleratorów rozwiązań w danej subskrypcji.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień Azure Cosmos DB można zainicjować w ramach subskrypcji?

50. Możesz utworzyć [bilet pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby zgłosić ten limit, ale domyślnie można udostępnić tylko 50 wystąpień Cosmos DB na subskrypcję.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Czy mogę utworzyć Akcelerator rozwiązania, jeśli mam Microsoft Azure dla DreamSpark?

> [!NOTE]
> Microsoft Azure dla DreamSpark jest teraz znany jako Microsoft Imagine dla studentów.

Obecnie nie można utworzyć akceleratora rozwiązania za pomocą [Microsoft Azure dla konta DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) . Możesz jednak utworzyć [bezpłatne konto wersji próbnej dla platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, które umożliwią utworzenie akceleratora rozwiązań.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak mogę usunąć dzierżawę usługi Azure AD?

Zobacz przewodnik po wpisie w blogu Eric golpe [, aby usunąć dzierżawę usługi Azure AD](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Wdróż Akcelerator rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)