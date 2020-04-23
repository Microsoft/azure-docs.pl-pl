---
title: Eskalacja uprawnień chmury prywatnej
titleSuffix: Azure VMware Solution by CloudSimple
description: W tym artykule opisano sposób eskalacji uprawnień w chmurze prywatnej dla funkcji administracyjnych w programie vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870471"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalacja uprawnień vCenter w chmurze prywatnej z portalu CloudSimple

Aby uzyskać dostęp administracyjny do usługi Private Cloud vCenter, można tymczasowo eskalować uprawnienia CloudSimple.  Korzystając z podwyższonych uprawnień, można instalować rozwiązania VMware, dodawać źródła tożsamości i zarządzać użytkownikami.

Nowi użytkownicy mogą być tworzone w domenie syt/sso vCenter i mieć dostęp do vCenter.  Podczas tworzenia nowych użytkowników, dodać je do cloudSimple wbudowanych grup dostępu do vCenter.  Aby uzyskać więcej informacji, zobacz [CloudSimple Private Cloud model uprawnień VMware vCenter](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/).

> [!CAUTION]
> Nie należy wprowadzać żadnych zmian konfiguracji dla składników zarządzania. Akcje podjęte podczas eskalacji stanu uprzywilejowanego mogą niekorzystnie wpłynąć na system lub spowodować, że system stanie się niedostępny.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Podwyższanie poziomu uprawnień

1. Uzyskaj dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **Zasoby,** wybierz chmurę prywatną, dla której chcesz eskalować uprawnienia.

3. U dołu strony Podsumowanie w obszarze **Zmienianie uprawnień vSphere**kliknij pozycję **Eskalacja**.

    ![Zmienianie uprawnień vSphere](media/escalate-private-cloud-privilege.png)

4. Wybierz typ użytkownika vSphere.  Tylko `CloudOwner@cloudsimple.local` użytkownik lokalny może być eskalowany.

5. Wybierz interwał czasu eskalacji z listy rozwijanej. Wybierz najkrótszy okres, który pozwoli Ci wykonać zadanie.

6. Zaznacz to pole wyboru, aby potwierdzić, że rozumiesz zagrożenia.

    ![Okno dialogowe uprawnień Eskalacja](media/escalate-private-cloud-privilege-dialog.png)

7. Kliknij przycisk **OK**.

8. Proces eskalacji może potrwać kilka minut. Po zakończeniu kliknij przycisk **OK**.

Eskalacja uprawnień rozpoczyna się i trwa do końca wybranego interwału.  Możesz zalogować się do sieci vCenter chmury prywatnej, aby wykonywać zadania administracyjne.

> [!IMPORTANT]
> Tylko jeden użytkownik może mieć eskalacji uprawnień.  Należy de-eskalacji uprawnień użytkownika, zanim będzie można eskalować uprawnienia innego użytkownika.

> [!CAUTION]
> Nowi użytkownicy muszą być dodani tylko do *grupy Cloud-Owner,* *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* lub *Cloud-Global-VM-Admin-Group*.  Użytkownicy dodani do grupy *Administratorzy* zostaną automatycznie usunięci.  Tylko konta usług muszą być dodawane do grupy *administratorów* i kont usług nie mogą być używane do logowania się do interfejsu użytkownika sieci web vSphere.

## <a name="extend-privilege-escalation"></a>Rozszerzanie eskalacji uprawnień

Jeśli potrzebujesz dodatkowego czasu na wykonanie zadań, możesz przedłużyć okres eskalacji uprawnień.  Wybierz dodatkowy interwał czasu eskalacji, który umożliwia wykonywanie zadań administracyjnych.

1. W **witrynie Chmury** > **prywatne** zasobów w portalu CloudSimple wybierz chmurę prywatną, dla której chcesz rozszerzyć eskalację uprawnień.

2. U dołu karty Podsumowanie kliknij pozycję **Rozszerz eskalację uprawnień**.

    ![Rozszerzanie eskalacji uprawnień](media/de-escalate-private-cloud-privilege.png)

3. Wybierz interwał czasu eskalacji z listy rozwijanej. Przejrzyj nowy czas zakończenia eskalacji.

4. Kliknij **przycisk Zapisz,** aby wydłużyć interwał.

## <a name="de-escalate-privileges"></a>Uprawnienia do deeskalacji

Po zakończeniu zadań administracyjnych należy odeskalować swoje uprawnienia.  

1. W **witrynie Chmury** > **prywatne** zasobów w portalu CloudSimple wybierz chmurę prywatną, dla której chcesz deeskalować uprawnienia.

2. Kliknij pozycję **Deeskalacji**.

3. Kliknij przycisk **OK**.

> [!IMPORTANT]
> Aby uniknąć błędów, wyloguj się z vCenter i zaloguj się ponownie po deeskalacji uprawnień.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie źródeł tożsamości vCenter do używania usługi Active Directory](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* Instalowanie rozwiązania do tworzenia kopii zapasowych na [maszynach wirtualnych z obciążeniem kopii zapasowej](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/)