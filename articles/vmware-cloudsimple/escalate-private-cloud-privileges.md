---
title: Eskalacja uprawnień chmury prywatnej
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje sposób eskalacji uprawnień w chmurze prywatnej na potrzeby funkcji administracyjnych w programie vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2215bd9a60fdcf76077c3a1f2a91631dc0dbe88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895826"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalacja uprawnień vCenter w chmurze prywatnej z portalu CloudSimple

Aby uzyskać dostęp administracyjny do programu vCenter w chmurze prywatnej, można tymczasowo eskalować uprawnienia CloudSimple.  Korzystając z podniesionych uprawnień, można zainstalować rozwiązania VMware, dodać źródła tożsamości i zarządzać użytkownikami.

Nowych użytkowników można utworzyć w domenie vCenter SSO i uzyskać dostęp do programu vCenter.  Podczas tworzenia nowych użytkowników Dodaj je do wbudowanych grup CloudSimple, aby uzyskać dostęp do programu vCenter.  Aby uzyskać więcej informacji, zobacz [model uprawnień chmury prywatnej CloudSimple firmy VMware vCenter](./learn-private-cloud-permissions.md).

> [!CAUTION]
> Nie wprowadzaj żadnych zmian w konfiguracji składników zarządzania. Akcje podejmowane w ramach eskalacji uprzywilejowanego stanu mogą mieć negatywny wpływ na system lub mogą spowodować, że system stanie się niedostępny.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Podwyższanie poziomu uprawnień

1. Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** , wybierz chmurę prywatną, dla której chcesz eskalować uprawnienia.

3. W dolnej części strony Podsumowanie w obszarze **Zmień vSphere uprawnienia** kliknij pozycję **Eskalacja**.

    ![Zmień uprawnienie vSphere](media/escalate-private-cloud-privilege.png)

4. Wybierz typ użytkownika vSphere.  `CloudOwner@cloudsimple.local`Można eskalować tylko użytkownika lokalnego.

5. Wybierz przedział czasu eskalacji z listy rozwijanej. Wybierz najkrótszy okres, który pozwoli na ukończenie zadania.

6. Zaznacz pole wyboru, aby potwierdzić, że rozumiesz ryzyko.

    ![Okno dialogowe eskalacji uprawnień](media/escalate-private-cloud-privilege-dialog.png)

7. Kliknij przycisk **OK**.

8. Proces eskalacji może potrwać kilka minut. Po zakończeniu kliknij przycisk **OK**.

Eskalacja uprawnień rozpoczyna się i trwa do końca wybranego interwału.  Możesz zalogować się do programu vCenter w chmurze prywatnej, aby wykonywać zadania administracyjne.

> [!IMPORTANT]
> Tylko jeden użytkownik może mieć uprawnienia eskalacji.  Musisz anulować eskalację uprawnień użytkownika, zanim będzie można eskalować uprawnienia innego użytkownika.

> [!CAUTION]
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group   Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorzy* , a konta usług nie mogą być używane do logowania się do interfejsu użytkownika sieci Web vSphere.

## <a name="extend-privilege-escalation"></a>Rozwiń eskalację uprawnień

Jeśli potrzebujesz dodatkowego czasu na ukończenie zadań, możesz zwiększyć okres eskalacji uprawnień.  Wybierz dodatkowy interwał okresu eskalacji, który umożliwia wykonywanie zadań administracyjnych.

1. W   >  **chmurach prywatnych** zasobów w portalu CloudSimple Wybierz chmurę prywatną, dla której chcesz rozłożyć eskalację uprawnień.

2. W dolnej części karty Podsumowanie kliknij polecenie **Zwiększ eskalację uprawnień**.

    ![Rozwiń eskalację uprawnień](media/de-escalate-private-cloud-privilege.png)

3. Wybierz przedział czasu eskalacji z listy rozwijanej. Przejrzyj nową godzinę zakończenia eskalacji.

4. Kliknij przycisk **Zapisz** , aby zwiększyć interwał.

## <a name="de-escalate-privileges"></a>Anuluj eskalację uprawnień

Po zakończeniu zadań administracyjnych należy anulować eskalację uprawnień.  

1. W   >  **chmurach prywatnych** zasobów w portalu CloudSimple Wybierz chmurę prywatną, dla której chcesz cofnąć eskalację uprawnień.

2. Kliknij przycisk **Anuluj eskalację**.

3. Kliknij przycisk **OK**.

> [!IMPORTANT]
> Aby uniknąć błędów, Wyloguj się z programu vCenter i zaloguj się ponownie po usunięciu uprawnień.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj źródła tożsamości vCenter do użycia Active Directory](./set-vcenter-identity.md)
* Instalowanie rozwiązania do tworzenia kopii zapasowych na potrzeby [tworzenia kopii zapasowych maszyn wirtualnych](./backup-workloads-veeam.md)
