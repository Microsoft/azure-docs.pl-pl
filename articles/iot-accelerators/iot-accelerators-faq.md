---
title: Akceleratory rozwiązań IoT — często zadawane pytania — Azure | Microsoft Docs
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące akceleratorów rozwiązań IoT. Zawiera on linki do repozytoriów GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 61993df77b0831926f16339a741a2553e80c2a0d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713844"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Często zadawane pytania dotyczące akceleratorów rozwiązań IoT

Zobacz też często [zadawane pytania dotyczące połączonej fabryki.](iot-accelerators-faq-cf.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Gdzie mogę znaleźć kod źródłowy akceleratorów rozwiązań?

Kod źródłowy jest przechowywany w następujących repozytoriach GitHub:

* [Akcelerator rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory)
* [Akcelerator rozwiązania do symulacji urządzeń](https://github.com/Azure/azure-iot-pcs-device-simulation)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Gdzie można znaleźć akceleratory rozwiązań do monitorowania zdalnego i konserwacji predykcyjnej?

Od 10 grudnia 2020 r. akceleratory zdalnego monitorowania i konserwacji predykcyjnej zostały usunięte z witryny [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) i nie są już dostępne dla nowych wdrożeń. Repozytoria GitHub dla obu akceleratorów zostały zarchiwizowane. Kod jest nadal dostępny dla wszystkich, ale repozytoria nie są w żaden sposób udostępniane.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Co się stanie z istniejącymi wdrożeniami zdalnego monitorowania i konserwacji predykcyjnej?

Usunięcie akceleratorów rozwiązań do monitorowania zdalnego i konserwacji predykcyjnej nie ma wpływu na istniejące wdrożenia i będzie nadal działać. Nie ma to również wpływu na repozytoria poszybowane. Repozytoria główne w usłudze GitHub zostały zarchiwizowane.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Jak mogę wdrożyć akcelerator rozwiązania do symulacji urządzeń?

Aby wdrożyć akcelerator rozwiązania do symulacji urządzeń, zobacz [repozytorium](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) GitHub symulacji urządzenia.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Gdzie można znaleźć informacje o usuniętych akceleratorach rozwiązań?

Zobacz następujące strony w witrynie poprzednich wersji:

* [Zdalne monitorowanie](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Konserwacja zapobiegawcza](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jakich zestawów SDK można użyć do opracowywania klientów urządzeń dla akceleratorów rozwiązań?

Linki do różnych zestawów SDK urządzeń IoT (C, .NET, Java, Node.js, Python) można znaleźć w repozytoriach GitHub zestawów SDK usługi [Microsoft Azure IoT.](https://github.com/Azure/azure-iot-sdks)

Jeśli używasz urządzenia DevKit, zasoby i przykłady można znaleźć w repozytorium GitHub zestawu [SDK IoT DevKit.](https://github.com/Microsoft/devkit-sdk)

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi i chcę zmienić mapowanie katalogu między moją subskrypcją a określoną dzierżawą usługi Azure AD. Jak mogę wykonać to zadanie?

Zobacz [Aby dodać istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Chcę zmienić administratora usługi lub konto Co-Administrator po zalogowaniu się przy użyciu konta organizacyjnego

Zobacz artykuł pomocy technicznej Changing Service Administrator and Co-Administrator when logged with an organizational account (Zmienianie administratora usługi [i Co-Administrator po zalogowaniu się przy użyciu konta organizacyjnego).](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego jest wyświetlany ten błąd? "Twoje konto nie ma odpowiednich uprawnień do tworzenia rozwiązania. Skontaktuj się z administratorem konta lub spróbuj użyć innego konta".

Wskazówki można uzyskać na poniższym diagramie:

![Schemat blokowy uprawnień](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Jeśli po zakończeniu sprawdzania, czy jesteś administratorem globalnym dzierżawy usługi Azure AD i współwłascą subskrypcji, niech administrator konta usunie użytkownika i ponownie przypisze wymagane uprawnienia w tej kolejności. Najpierw dodaj użytkownika jako administratora globalnego, a następnie dodaj go jako współzałożycieka subskrypcji platformy Azure. Jeśli problemy będą się powtarzać, skontaktuj [się z pomocą & pomocy technicznej.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego ten błąd jest wyświetlany, gdy mam subskrypcję platformy Azure? "Subskrypcja platformy Azure jest wymagana do tworzenia wstępnie skonfigurowanych rozwiązań. Konto bezpłatnej wersji próbnej możesz utworzyć w zaledwie kilka minut".

Jeśli masz pewność, że masz subskrypcję platformy Azure, zweryfikuj mapowanie dzierżawy dla swojej subskrypcji i sprawdź, czy na liście rozwijanej wybrano prawidłową dzierżawę. Jeśli sprawdzono, czy dzierżawa jest prawidłowa, postępuj zgodnie z powyższym diagramem i zweryfikuj mapowanie subskrypcji i tej dzierżawy usługi Azure AD.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaka jest różnica między usunięciem grupy zasobów w Azure Portal a kliknięciem przycisku Usuń na akceleratorze rozwiązania w azureiotsolutions.com?

* Usunięcie akceleratora rozwiązania w [programie azureiotsolutions.com](https://www.azureiotsolutions.com/)spowoduje usunięcie wszystkich zasobów, które zostały wdrożone podczas tworzenia akceleratora rozwiązania. Jeśli do grupy zasobów dodano dodatkowe zasoby, te zasoby również zostaną usunięte.
* Jeśli usuniesz grupę zasobów w Azure Portal [,](https://portal.azure.com)usuniesz tylko zasoby w tej grupie zasobów. Należy również usunąć aplikację Azure Active Directory skojarzoną z akceleratorem rozwiązania.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Czy mogę nadal korzystać z istniejących inwestycji w Azure IoT Solution Accelerators?

Tak. Każde rozwiązanie, które istnieje obecnie, będzie nadal działać w ramach subskrypcji platformy Azure, a kod źródłowy pozostanie dostępny w usłudze GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień IoT Hub aprowizuje się w ramach subskrypcji?

Domyślnie można aprowizuje [10 centrów IoT na subskrypcję.](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) Aby podnieść ten limit, możesz utworzyć bilet pomocy technicznej platformy [Azure.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) W związku z tym, ponieważ każdy akcelerator rozwiązań aprowizuje nową IoT Hub, w ramach danej subskrypcji można aprowizować tylko 10 akceleratorów rozwiązań.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień Azure Cosmos DB aprowizuje się w ramach subskrypcji?

Pięćdziesiąt. Możesz utworzyć bilet pomocy technicznej platformy [Azure,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby podnieść ten limit, ale domyślnie możesz aprowizować tylko 50 wystąpień Cosmos DB na subskrypcję.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Czy mogę utworzyć akcelerator rozwiązań, jeśli mam Microsoft Azure dla aplikacji DreamSpark?

> [!NOTE]
> Microsoft Azure dla programie DreamSpark jest teraz znany jako Microsoft Imagine dla uczniów.

Obecnie nie można utworzyć akceleratora rozwiązań z [Microsoft Azure dla konta Aplikacji DreamSpark.](https://azure.microsoft.com/pricing/member-offers/imagine/) Możesz jednak utworzyć konto bezpłatnej wersji [próbnej](https://azure.microsoft.com/free/) platformy Azure w zaledwie kilka minut, co umożliwi utworzenie akceleratora rozwiązań.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak mogę dzierżawy usługi Azure AD?

Zobacz wpis Erica Golpe'a w blogu Walkthrough of Deleting an Azure AD Tenant (Przewodnik [po usuwaniu dzierżawy usługi Azure AD).](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Wdrażanie akceleratora rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)