---
title: Używanie portalu do włączania Pulpit zdalny dla roli
titleSuffix: Azure Cloud Services
description: Jak skonfigurować aplikację usługi w chmurze platformy Azure w celu zezwalania na połączenia pulpitu zdalnego
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84696783"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Włączanie Podłączanie pulpitu zdalnego roli na platformie Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Program Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu roli działającej na platformie Azure. Połączenia Pulpit zdalny można użyć do rozwiązywania problemów i diagnozowania problemów z aplikacją, gdy jest ona uruchomiona.

Możesz włączyć Pulpit zdalny połączenie w swojej roli podczas opracowywania, dołączając moduły Pulpit zdalny w definicji usługi lub można włączyć Pulpit zdalny za pomocą rozszerzenia Pulpit zdalny. Preferowanym podejściem jest użycie rozszerzenia Pulpit zdalny, ponieważ można włączyć Pulpit zdalny nawet po wdrożeniu aplikacji bez konieczności ponownego wdrażania aplikacji.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurowanie Pulpit zdalny z Azure Portal

Azure Portal używa podejścia rozszerzenia Pulpit zdalny, aby można było włączyć Pulpit zdalny nawet po wdrożeniu aplikacji. Ustawienia **pulpit zdalny** dla usługi w chmurze umożliwiają włączenie pulpit zdalny, zmianę konta administratora lokalnego używanego do nawiązywania połączenia z maszynami wirtualnymi, certyfikatu używanego w ramach uwierzytelniania i ustawienia daty wygaśnięcia.

1. Kliknij **Cloud Services**, wybierz nazwę usługi w chmurze, a następnie wybierz pozycję **pulpit zdalny**.

    ![Pulpit zdalny usług Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Wybierz, czy chcesz włączyć Pulpit zdalny dla poszczególnych ról, czy dla wszystkich ról, a następnie zmień wartość przełącznika, aby **włączyć**.

3. Wypełnij pola wymagane dla nazwy użytkownika, hasła, wygaśnięcia i certyfikatu.

    ![Pulpit zdalny usług Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Wszystkie wystąpienia ról zostaną uruchomione ponownie przy pierwszym włączeniu Pulpit zdalny i wybierz pozycję **OK** (znacznik wyboru). Aby zapobiec ponownym uruchomieniu, należy zainstalować certyfikat użyty do zaszyfrowania hasła. Aby zapobiec ponownemu uruchomieniu, [Przekaż certyfikat dla usługi w chmurze](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) , a następnie wróć do tego okna dialogowego.

4. W obszarze **role**wybierz rolę do zaktualizowania lub wybierz **wszystkie** role.

5. Po zakończeniu aktualizacji konfiguracji wybierz pozycję **Zapisz**. Potrwa to chwilę, zanim wystąpienia roli będą gotowe do odbierania połączeń.

## <a name="remote-into-role-instances"></a>Zdalne do wystąpień roli

Po włączeniu Pulpit zdalny na rolach można zainicjować połączenie bezpośrednio z poziomu Azure Portal:

1. Kliknij pozycję **wystąpienia** , aby otworzyć ustawienia **wystąpień** .
2. Wybierz wystąpienie roli, dla którego skonfigurowano Pulpit zdalny.
3. Kliknij przycisk **Połącz** , aby pobrać plik RDP dla wystąpienia roli.

    ![Pulpit zdalny usług Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Kliknij przycisk **Otwórz** , a następnie **Połącz** się, aby uruchomić Pulpit zdalny połączenie.

>[!NOTE]
> Jeśli usługa w chmurze znajduje się za sieciowej grupy zabezpieczeń, może być konieczne utworzenie reguł zezwalających na ruch na portach **3389** i **20000**.  Pulpit zdalny używa portu **3389**.  Wystąpienia usługi w chmurze są zrównoważone obciążenia, więc nie można bezpośrednio kontrolować tego, z którym wystąpieniem ma zostać nawiązane połączenie.  Agenci *RemoteForwarder* i *RemoteAccess* zarządzają ruchem RDP i umożliwiają klientowi wysyłanie plików cookie RDP i określanie poszczególnych wystąpień do nawiązania połączenia.  Agenci *RemoteForwarder* i *RemoteAccess* wymagają, aby port **20000*** był otwarty, co może być zablokowane, jeśli masz sieciowej grupy zabezpieczeń.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Jak skonfigurować Cloud Services](cloud-services-how-to-configure-portal.md)
