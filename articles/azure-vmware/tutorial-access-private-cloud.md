---
title: Samouczek — informacje na temat uzyskiwania dostępu do chmury prywatnej
description: Dowiedz się, jak uzyskać dostęp do chmury prywatnej (Automatyczna synchronizacja) rozwiązania VMware
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fcee332818c89d9c8b00795dca9e74f68260eefd
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740192"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Samouczek: informacje o uzyskiwaniu dostępu do rozwiązania Azure VMware (Automatyczna synchronizacja) w chmurze prywatnej

W trakcie okresu zapoznawczego funkcja automatycznej synchronizacji nie pozwala na zarządzanie chmurą prywatną przy użyciu lokalnego programu vCenter. Musisz wykonać dodatkową konfigurację i połączenie z lokalnym wystąpieniem programu vCenter za pomocą pola skoku. 

W tym samouczku utworzysz maszynę wirtualną z systemem Windows dla pola skoku w grupie zasobów utworzonej w poprzednim samouczku samouczka [: Konfigurowanie sieci dla swojej chmury prywatnej VMware na platformie Azure](tutorial-configure-networking.md) i logowanie do programu vCenter. Jest to maszyna wirtualna w tej samej sieci wirtualnej, która została utworzona, i zapewnia dostęp do programu vCenter i NSX Manager. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows do użycia w celu nawiązania połączenia z programem vCenter
> * Zaloguj się do programu vCenter z maszyny wirtualnej

## <a name="create-a-new-windows-virtual-machine"></a>Tworzenie nowej maszyny wirtualnej z systemem Windows

W grupie zasobów wybierz pozycję **+ Dodaj** , a następnie wyszukaj i wybierz pozycję **Microsoft Windows 10**, a następnie kliknij pozycję **Utwórz**.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Dodawanie nowej maszyny wirtualnej z systemem Windows 10 dla serwera przesiadkowego" border="true":::

Wprowadź wymagane informacje w polach, a następnie wybierz pozycję **Przegląd + Utwórz**. Aby uzyskać więcej informacji na temat pól, zobacz poniższą tabelę.

| Pole | Wartość |
| --- | --- |
| **Subskrypcja** | Ta wartość jest już wypełniona z subskrypcją, do której należy Grupa zasobów. |
| **Grupa zasobów** | Ta wartość jest już wypełniona dla bieżącej grupy zasobów. Powinna to być grupa zasobów utworzona w poprzednim samouczku. |
| **Nazwa maszyny wirtualnej** | Wprowadź unikatową nazwę maszyny wirtualnej. |
| **Region** | Wybierz lokalizację geograficzną maszyny wirtualnej. |
| **Opcje dostępności** | Pozostaw wybraną wartość domyślną. |
| **Obraz** | Wybierz obraz maszyny wirtualnej. |
| **Rozmiar** | Pozostaw wartość domyślną rozmiar. |
| **Typ uwierzytelniania**  | Wybierz pozycję **hasło**. |
| **Uż** | Wprowadź nazwę użytkownika, aby zalogować się do maszyny wirtualnej. |
| **Hasło** | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
| **Potwierdź hasło** | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
| **Publiczne porty wejściowe** | Wybierz pozycję **Brak**. W przypadku wybrania opcji Brak można użyć [dostępu JIT](../security-center/security-center-just-in-time.md#configure-jit-access-from-an-azure-vms-page-) , aby kontrolować dostęp do maszyny wirtualnej tylko wtedy, gdy chcesz uzyskać do niej dostęp.  |

Po wprowadzeniu odpowiednich informacji kliknij przycisk **Przegląd + Utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia maszyny wirtualnej.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Tworzenie nowej maszyny wirtualnej z systemem Windows 10 dla serwera przesiadkowego" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Nawiązywanie połączenia z lokalnym programem vCenter w chmurze prywatnej

W polu skoku Zaloguj się, aby vSphere klienta przy użyciu programu VMware vCenter SSO. Zaloguj się do klienta vSphere przy użyciu nazwy użytkownika administratora chmury; Zaakceptuj zagrożenie bezpieczeństwa i Kontynuuj, gdy zobaczysz ostrzeżenie dotyczące potencjalnego zagrożenia bezpieczeństwa; Zaloguj się do programu VMware vCenter przy użyciu poświadczeń logowania jednokrotnego i sprawdź, czy interfejs użytkownika został pomyślnie wyświetlony.

W Azure Portal Wybierz chmurę prywatną, a następnie w widoku **Przegląd** wybierz pozycję **tożsamość > domyślna**. Wyświetlane są adresy URL i poświadczenia logowania dla usługi Private Cloud vCenter i NSX-T.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Private Cloud vCenter i NSX Manager" border="true":::

Przejdź do maszyny wirtualnej utworzonej w poprzednim kroku i Połącz się z maszyną wirtualną. Aby uzyskać szczegółowe instrukcje dotyczące nawiązywania połączenia z maszyną wirtualną, zobacz [nawiązywanie połączenia z maszyną wirtualną](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

Na maszynie wirtualnej z systemem Windows otwórz przeglądarkę i przejdź do adresu URL menedżera vCenter i NSX-T na dwóch kartach. Na karcie vCenter wprowadź poświadczenia `cloudadmin@vmcp.local` użytkownika z poprzedniego kroku.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Logowanie do usługi vCenter w chmurze prywatnej" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Portal vCenter" border="true":::

Na drugiej karcie przeglądarki Zaloguj się do Menedżera NSX-T.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Strona główna usługi Local Private NSX Manager" border="true":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows do użycia w celu nawiązania połączenia z programem vCenter
> * Zaloguj się do programu vCenter z maszyny wirtualnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak skalować chmurę prywatną do automatycznej synchronizacji.

> [!div class="nextstepaction"]
> [Skalowanie chmury prywatnej automatycznej synchronizacji](tutorial-scale-private-cloud.md)