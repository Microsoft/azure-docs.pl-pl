---
title: Używanie portalu do włączania Pulpit zdalny dla roli
description: Jak skonfigurować aplikację usługi w chmurze platformy Azure w celu zezwalania na połączenia pulpitu zdalnego
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8fa0d3c0e29c53e6fe9cb32ddf02168686be1efe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743257"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Włączanie Podłączanie pulpitu zdalnego dla roli w usłudze Azure Cloud Services (wersja klasyczna)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu roli działającej na platformie Azure. Połączenia Pulpit zdalny można użyć do rozwiązywania problemów i diagnozowania problemów z aplikacją, gdy jest ona uruchomiona.

Możesz włączyć Pulpit zdalny połączenie w swojej roli podczas opracowywania, dołączając moduły Pulpit zdalny w definicji usługi lub można włączyć Pulpit zdalny za pomocą rozszerzenia Pulpit zdalny. Preferowanym podejściem jest użycie rozszerzenia Pulpit zdalny, ponieważ można włączyć Pulpit zdalny nawet po wdrożeniu aplikacji bez konieczności ponownego wdrażania aplikacji.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurowanie Pulpit zdalny z Azure Portal

Azure Portal używa podejścia rozszerzenia Pulpit zdalny, aby można było włączyć Pulpit zdalny nawet po wdrożeniu aplikacji. Ustawienia **pulpit zdalny** dla usługi w chmurze umożliwiają włączenie pulpit zdalny, zmianę konta administratora lokalnego używanego do nawiązywania połączenia z maszynami wirtualnymi, certyfikatu używanego w ramach uwierzytelniania i ustawienia daty wygaśnięcia.

1. Kliknij **Cloud Services**, wybierz nazwę usługi w chmurze, a następnie wybierz pozycję **pulpit zdalny**.

    ![obraz przedstawia pulpit zdalny usług Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Wybierz, czy chcesz włączyć Pulpit zdalny dla poszczególnych ról, czy dla wszystkich ról, a następnie zmień wartość przełącznika, aby **włączyć**.

3. Wypełnij pola wymagane dla nazwy użytkownika, hasła, wygaśnięcia i certyfikatu.

    ![Pulpit zdalny usług Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Wszystkie wystąpienia ról zostaną uruchomione ponownie przy pierwszym włączeniu Pulpit zdalny i wybierz pozycję **OK** (znacznik wyboru). Aby zapobiec ponownym uruchomieniu, należy zainstalować certyfikat użyty do zaszyfrowania hasła. Aby zapobiec ponownemu uruchomieniu, [Przekaż certyfikat dla usługi w chmurze](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) , a następnie wróć do tego okna dialogowego.

4. W obszarze **role** wybierz rolę do zaktualizowania lub wybierz **wszystkie** role.

5. Po zakończeniu aktualizacji konfiguracji wybierz pozycję **Zapisz**. Potrwa to chwilę, zanim wystąpienia roli będą gotowe do odbierania połączeń.

## <a name="remote-into-role-instances"></a>Zdalne do wystąpień roli

Po włączeniu Pulpit zdalny na rolach można zainicjować połączenie bezpośrednio z poziomu Azure Portal:

1. Kliknij pozycję **wystąpienia** , aby otworzyć ustawienia **wystąpień** .
2. Wybierz wystąpienie roli, dla którego skonfigurowano Pulpit zdalny.
3. Kliknij przycisk **Połącz** , aby pobrać plik RDP dla wystąpienia roli.

    ![Obraz pulpitu zdalnego usług Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Kliknij przycisk **Otwórz** , a następnie **Połącz** się, aby uruchomić Pulpit zdalny połączenie.

>[!NOTE]
> Jeśli usługa w chmurze znajduje się za sieciowej grupy zabezpieczeń, może być konieczne utworzenie reguł zezwalających na ruch na portach **3389** i **20000**.  Pulpit zdalny używa portu **3389**.  Wystąpienia usługi w chmurze są zrównoważone obciążenia, więc nie można bezpośrednio kontrolować tego, z którym wystąpieniem ma zostać nawiązane połączenie.  Agenci *RemoteForwarder* i *RemoteAccess* zarządzają ruchem RDP i umożliwiają klientowi wysyłanie plików cookie RDP i określanie poszczególnych wystąpień do nawiązania połączenia.  Agenci *RemoteForwarder* i *RemoteAccess* wymagają, aby port **20000*** był otwarty, co może być zablokowane, jeśli masz sieciowej grupy zabezpieczeń.

## <a name="additional-resources"></a>Dodatkowe zasoby

[Jak skonfigurować Cloud Services](cloud-services-how-to-configure-portal.md)
