---
title: Dostęp do rozwiązania VMware platformy Azure według cloudsimple — portal
description: Zawiera opis sposobu uzyskiwania dostępu do rozwiązania VMware Solution by CloudSimple z witryny Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869331"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Dostęp do rozwiązania VMware przez portal CloudSimple z portalu Azure

Logowanie jednokrotne jest obsługiwane w celu uzyskania dostępu do portalu CloudSimple. Po zalogowaniu się do witryny Azure portal, można uzyskać dostęp do portalu CloudSimple bez logowania się ponownie. Przy pierwszym wejściu do portalu CloudSimple zostanie wyświetlony monit o autoryzację aplikacji [CloudSimple Service Authorization.](#consent-to-cloudsimple-service-authorization-application)  Autoryzacja jest jednorazową akcją.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Użytkownicy z wbudowanymi rolami **Właściciel** i **Współautor** mogą uzyskiwać dostęp do portalu CloudSimple.  Role muszą być skonfigurowane w grupie zasobów, w której wdrożono usługę CloudSimple.  Role można również skonfigurować w obiekcie usługi CloudSimple.  Aby uzyskać więcej informacji na temat sprawdzania roli, zobacz [Wyświetlanie przypisań ról.](https://docs.microsoft.com/azure/role-based-access-control/check-access) Tylko użytkownicy z wbudowanymi rolami **właściciela** i **współautora** mogą uzyskać dostęp do portalu CloudSimple.  Role muszą być skonfigurowane w subskrypcji.  Aby uzyskać więcej informacji na temat sprawdzania roli, zobacz [Wyświetlanie przypisań ról.](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Jeśli używasz ról niestandardowych, rola powinna mieć ```Actions```dowolną z następujących operacji w obszarze .  Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Role niestandardowe dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Jeśli którakolwiek z operacji ```NotActions```jest częścią , użytkownik nie może uzyskać dostępu do portalu CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usługi CloudSimple**.

3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.

4. Na stronie **Przegląd** kliknij pozycję **Przejdź do portalu CloudSimple**.  Jeśli uzyskujesz dostęp do portalu CloudSimple z witryny Azure portal po raz pierwszy, zostanie wyświetlony monit o autoryzację aplikacji [CloudSimple Service Authorization.](#consent-to-cloudsimple-service-authorization-application) 

    ![Uruchom portal CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Jeśli wybierzesz operację chmury prywatnej (na przykład tworzenie lub rozwijanie chmury prywatnej) bezpośrednio z witryny Azure portal, portal CloudSimple zostanie otwarty na wskazanej stronie.

W portalu CloudSimple wybierz pozycję **Strona główna** w menu bocznym, aby wyświetlić informacje podsumowujące dotyczące chmur prywatnych. Zasoby i pojemność chmur prywatnych są wyświetlane, wraz z alertów i zadań, które wymagają uwagi. W przypadku typowych zadań kliknij nazwane ikony u góry strony.

![Strona główna](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Zgoda na aplikację autoryzacji usługi CloudSimple

Uruchamianie portalu CloudSimple z witryny Azure portal po raz pierwszy wymaga zgody aplikacji CloudSimple Autoryzacja usługi.  Wybierz **pozycję Zaakceptuj,** aby udzielić żądanych uprawnień i uzyskać dostęp do portalu CloudSimple.

![Zgoda na autoryzację usługi CloudSimple - administratorzy](media/cloudsimple-azure-consent.png)

Jeśli masz uprawnienia administratora globalnego, możesz wyrazić zgodę na organizację.  Wybierz pozycję **Wyraź zgodę w imieniu swojej organizacji**.

![Zgoda na autoryzację usługi CloudSimple — administrator globalny](media/cloudsimple-azure-consent-global-admin.png)

Jeśli twoje uprawnienia nie zezwalają na dostęp do portalu CloudSimple, skontaktuj się z globalnym administratorem dzierżawy, aby udzielić wymaganych uprawnień.  Administrator globalny może wyrazić zgodę w imieniu twojej organizacji.

![Zgoda na autoryzację usługi CloudSimple — wymaga od administratorów](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
