---
title: Konfigurowanie łączności maszyny wirtualnej platformy Azure
titleSuffix: Azure SQL Managed Instance
description: Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL przy użyciu SQL Server Management Studio z maszyny wirtualnej platformy Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620260"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Szybki Start: Konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym przewodniku szybki start pokazano, jak skonfigurować maszynę wirtualną platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym usługi Azure SQL przy użyciu SQL Server Management Studio (SSMS). 


Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak nawiązać połączenie z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia punkt-lokacja](point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start używa zasobów utworzonych w ramach [tworzenia wystąpienia zarządzanego](instance-create-quickstart.md) jako punktu początkowego.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Tworzenie nowej podsieci sieci wirtualnej

Poniższe kroki tworzą nową podsieć w sieci wirtualnej wystąpienia zarządzanego SQL, aby maszyna wirtualna platformy Azure mogła połączyć się z wystąpieniem zarządzanym. Podsieć wystąpienia zarządzanego SQL jest przeznaczona dla wystąpień zarządzanych. W tej podsieci nie można tworzyć żadnych innych zasobów, takich jak maszyny wirtualne platformy Azure.

1. Otwórz grupę zasobów dla wystąpienia zarządzanego utworzonego w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md) — Szybki Start. Wybierz sieć wirtualną dla wystąpienia zarządzanego.

   ![Zasoby wystąpienia zarządzanego SQL](./media/connect-vm-instance-configure/resources.png)

2. Wybierz pozycję **podsieci** , a następnie wybierz pozycję **+ podsieć** , aby utworzyć nową podsieć.

   ![Podsieci wystąpienia zarządzanego SQL](./media/connect-vm-instance-configure/subnets.png)

3. Wypełnij formularz, korzystając z informacji podanych w tej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- |
   | **Nazwa** | Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming).|
   | **Zakres adresów (blok CIDR)** | Prawidłowy zakres | Wartość domyślna jest dobra dla tego przewodnika Szybki Start.|
   | **Sieciowa grupa zabezpieczeń** | Brak | Wartość domyślna jest dobra dla tego przewodnika Szybki Start.|
   | **Tabela tras** | Brak | Wartość domyślna jest dobra dla tego przewodnika Szybki Start.|
   | **Punkty końcowe usługi** | Wybrano 0 | Wartość domyślna jest dobra dla tego przewodnika Szybki Start.|
   | **Delegowanie podsieci** | Brak | Wartość domyślna jest dobra dla tego przewodnika Szybki Start.|

   ![Nowa podsieć wystąpienia zarządzanego SQL dla maszyny wirtualnej klienta](./media/connect-vm-instance-configure/new-subnet.png)

4. Wybierz **przycisk OK** , aby utworzyć tę dodatkową podsieć w sieci wirtualnej wystąpienia zarządzanego SQL.

## <a name="create-a-vm-in-the-new-subnet"></a>Tworzenie maszyny wirtualnej w nowej podsieci 

Poniższe kroki pokazują, jak utworzyć maszynę wirtualną w nowej podsieci w celu nawiązania połączenia z wystąpieniem zarządzanym SQL.

## <a name="prepare-the-azure-virtual-machine"></a>Przygotowywanie maszyny wirtualnej platformy Azure

Ponieważ wystąpienie zarządzane SQL jest umieszczane w prywatnej sieci wirtualnej, należy utworzyć maszynę wirtualną platformy Azure przy użyciu zainstalowanego narzędzia klienta SQL, takiego jak SQL Server Management Studio lub Azure Data Studio. To narzędzie umożliwia nawiązanie połączenia z wystąpieniem zarządzanym SQL i wykonywanie zapytań. W tym przewodniku Szybki start używany jest program SQL Server Management Studio.

Najprostszym sposobem utworzenia klienckiej maszyny wirtualnej przy użyciu wszystkich niezbędnych narzędzi jest użycie szablonów Azure Resource Manager.

1. Upewnij się, że zalogowano się do Azure Portal na innej karcie przeglądarki. Następnie wybierz poniższy przycisk, aby utworzyć maszynę wirtualną klienta i zainstalować SQL Server Management Studio:

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. Wypełnij formularz, korzystając z informacji podanych w poniższej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ---------------- | ----------------- | ----------- |
   | **Subskrypcja** | Prawidłowa subskrypcja | Musi być subskrypcją, w której masz uprawnienia do tworzenia nowych zasobów. |
   | **Grupa zasobów** |Grupa zasobów określona w szybkim [tworzeniu wystąpienia zarządzanego SQL](instance-create-quickstart.md)|Ta grupa zasobów musi być tą, w której istnieje sieć wirtualna.|
   | **Lokalizacja** | Lokalizacja grupy zasobów | Ta wartość jest wypełniana na podstawie wybranej grupy zasobów. |
   | **Nazwa maszyny wirtualnej**  | Dowolna prawidłowa nazwa | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming).|
   |**Nazwa użytkownika administratora**|Dowolna prawidłowa nazwa użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming). Nie używaj nazwy „serveradmin”, gdyż jest ona zarezerwowana dla roli poziomu serwera.<br>Ta nazwa użytkownika jest używana przy każdej próbie [połączenia z maszyną wirtualną](#connect-to-the-virtual-machine).|
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>To hasło jest używane podczas [nawiązywania połączenia z maszyną wirtualną](#connect-to-the-virtual-machine).|
   | **Rozmiar maszyny wirtualnej** | Dowolny prawidłowy rozmiar | Wartość domyślna w tym szablonie **Standard_B2s** jest wystarczająca dla tego przewodnika Szybki Start. |
   | **Lokalizacja**|[resourceing (). Location].| Nie zmieniaj tej wartości. |
   | **Nazwa Virtual Network**|Sieć wirtualna, w której utworzono wystąpienie zarządzane|
   | **Nazwa podsieci**|Nazwa podsieci, która została utworzona w poprzedniej procedurze| Nie wybieraj podsieci, w której utworzono wystąpienie zarządzane.|
   | **Lokalizacja artefaktów** | [Deployment (). Properties. templateLink. URI] | Nie zmieniaj tej wartości. |
   | **token SAS lokalizacji artefaktów** | Pozostaw puste. | Nie zmieniaj tej wartości. |

   ![tworzenie maszyny wirtualnej klienta](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Jeśli została użyta Sugerowana nazwa sieci wirtualnej i domyślna podsieć podczas [tworzenia wystąpienia zarządzanego SQL](instance-create-quickstart.md), nie trzeba zmieniać ostatnich dwóch parametrów. W przeciwnym razie należy zmienić te wartości na wartości wprowadzone podczas konfigurowania środowiska sieciowego.

3. Zaznacz pole wyboru **Wyrażam zgodę na powyższe warunki i postanowienia** .
4. Wybierz pozycję **Kup** , aby wdrożyć maszynę wirtualną platformy Azure w sieci.
5. Wybierz ikonę **Powiadomienia**, aby wyświetlić stan wdrożenia.

> [!IMPORTANT]
> Nie należy kontynuować do 15 minut po utworzeniu maszyny wirtualnej, aby zapewnić czas na zainstalowanie SQL Server Management Studio skryptów po utworzeniu.

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Poniższe kroki pokazują, jak nawiązać połączenie z nowo utworzoną maszyną wirtualną przy użyciu połączenia Pulpit zdalny.

1. Po zakończeniu wdrażania przejdź do zasobu maszyny wirtualnej.

    ![Zrzut ekranu przedstawia Azure Portal na stronie Przegląd dla wybranej maszyny wirtualnej i podłączania.](./media/connect-vm-instance-configure/vm.png)  

2. Wybierz pozycję **Połącz**.

   Zostanie wyświetlony formularz Remote Desktop Protocol pliku (RDP) z publicznym adresem IP i numerem portu dla maszyny wirtualnej.

   ![Formularz RDP](./media/connect-vm-instance-configure/rdp.png)  

3. Wybierz pozycję **Pobierz plik RDP**.

   > [!NOTE]
   > Do nawiązania połączenia z maszyną wirtualną można także użyć protokołu SSH.

4. Zamknij formularz **Połącz z maszyną wirtualną** .
5. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP.
6. Po wyświetleniu monitu wybierz pozycję **Połącz**. Na komputerze Mac potrzebny jest klient RDP, taki jak [ten pulpit zdalny klienta](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) ze sklepu Mac App Store.

7. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie wybierz przycisk **OK**.

8. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz opcję **tak** lub **Kontynuuj** , aby kontynuować połączenie.

Nawiązano połączenie z maszyną wirtualną na pulpicie nawigacyjnym Menedżer serwera.

## <a name="connect-to-sql-managed-instance"></a>Łączenie z wystąpieniem zarządzanym SQL 

1. Na maszynie wirtualnej Otwórz SQL Server Management Studio.

   Otwarcie programu może potrwać kilka minut, ponieważ jest to konieczne po raz pierwszy.
2. W oknie dialogowym **łączenie z serwerem** wprowadź w pełni kwalifikowaną **nazwę hosta** dla wystąpienia zarządzanego w polu **Nazwa serwera** . Wybierz pozycję **uwierzytelnianie SQL Server**, podaj nazwę użytkownika i hasło, a następnie wybierz pozycję **Połącz**.

    ![Łączenie programu SSMS](./media/connect-vm-instance-configure/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić systemowe bazy danych oraz bazy danych użytkownika w węźle Bazy danych, a także różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server oraz Profiler systemu XEvent.

## <a name="next-steps"></a>Następne kroki

- Przewodnik Szybki Start przedstawiający sposób łączenia się z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja znajduje się w temacie [Konfigurowanie połączenia punkt-lokacja](point-to-site-p2s-configure.md).
- Aby zapoznać się z omówieniem opcji połączenia dla aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym SQL](connect-application-instance.md).
- Aby przywrócić istniejącą bazę danych SQL Server z lokalnego do wystąpienia zarządzanego, można użyć [Azure Database Migration Service do migracji](../../dms/tutorial-sql-server-to-managed-instance.md) lub [polecenia przywracania T-SQL](restore-sample-database-quickstart.md) , aby przywrócić plik kopii zapasowej bazy danych.
