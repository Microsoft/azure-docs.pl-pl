---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d679767f1a4be3ecdcf1811662c72dbbe2df33a0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014790"
---
### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Otwieranie portów TCP w zaporze systemu Windows dla domyślnego wystąpienia aparatu bazy danych
1. Nawiąż połączenie z maszyną wirtualną przy użyciu pulpitu zdalnego. Aby uzyskać szczegółowe instrukcje dotyczące nawiązywania połączenia z maszyną wirtualną, zobacz [Otwieranie maszyny wirtualnej SQL przy użyciu pulpitu zdalnego](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md#remotedesktop).
2. Po zalogowaniu się na ekranie startowym wpisz **WF. msc**, a następnie naciśnij klawisz ENTER.
   
    ![Uruchamianie programu zapory](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. W obszarze **Zapora systemu Windows z zabezpieczeniami zaawansowanymi** w okienku po lewej stronie kliknij prawym przyciskiem myszy pozycję **Reguły ruchu przychodzącego**, a następnie kliknij pozycję **Nowa reguła** w okienku akcji.
   
    ![Nowa reguła](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. W oknie dialogowym **Kreator nowej reguły ruchu przychodzącego** w obszarze **Typ reguły** wybierz pozycję **Port**, a następnie kliknij przycisk **Dalej**.
5. W oknie dialogowym **Protokół i porty** użyj domyślnej wartości **TCP**. Następnie w polu **Określone porty lokalne** wpisz numer portu wystąpienia aparatu bazy danych (**1433** dla wystąpienia domyślnego lub wybrany numer dla portu prywatnego w kroku punktu końcowego).
   
    ![Port TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Kliknij przycisk **Dalej**.
7. W oknie dialogowym **Akcja** wybierz pozycję **Zezwalaj na połączenie**, a następnie kliknij przycisk **Dalej**.
   
    **Uwaga dotycząca zabezpieczeń**: Wybranie opcji **Zezwalaj na połączenie, jeśli jest bezpieczne** może zapewnić większe bezpieczeństwo. Wybierz tę opcję, jeśli chcesz skonfigurować dodatkowe opcje zabezpieczeń w środowisku.
   
    ![Zezwalanie na połączenia](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. W oknie dialogowym **Profil** wybierz pozycję **Publiczny**, **Prywatny** i **Domena**. Następnie kliknij przycisk **Dalej**.
   
    **Uwaga dotycząca zabezpieczeń:** Wybranie pozycji **Publiczny** umożliwia uzyskiwanie dostępu przez Internet. Jeśli to możliwe, wybierz bardzie restrykcyjny profil.
   
    ![Profil publiczny](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. W oknie dialogowym **Nazwa** wpisz nazwę i opis dla tej reguły, a następnie kliknij przycisk **Zakończ**.
   
    ![Nazwa reguły](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Otwórz dodatkowe porty dla innych składników w zależności od potrzeb. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory systemu Windows w celu zezwalania na dostęp programowi SQL Server](https://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Konfigurowanie programu SQL Server do nasłuchiwania w protokole TCP

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Konfigurowanie programu SQL Server do uwierzytelniania w trybie mieszanym
Aparat bazy danych programu SQL Server nie może korzystać z uwierzytelniania systemu Windows bez środowiska domenowego. Aby nawiązać połączenie z aparatem bazy danych z innego komputera, skonfiguruj program SQL Server do uwierzytelniania w trybie mieszanym. Uwierzytelnianie w trybie mieszanym umożliwia korzystanie z zarówno uwierzytelniania programu SQL Server, jak i uwierzytelniania systemu Windows.

> [!NOTE]
> Konfigurowanie uwierzytelniania w trybie mieszanym może nie być konieczne, jeśli skonfigurowano usługę Azure Virtual Network ze skonfigurowanym środowiskiem domenowym.
> 
> 

1. Po nawiązaniu połączenia z maszyną wirtualną wpisz na stronie startowej ciąg **SQL Server Management Studio** i kliknij wybraną ikonę.
   
    Podczas pierwszego uruchomienia program Management Studio musi utworzyć środowisko użytkowników programu Management Studio. Może to potrwać kilka chwil.
2. Program Management Studio wyświetli okno dialogowe **Łączenie z serwerem**. W polu **Nazwa serwera** wpisz nazwę maszyny wirtualnej, którą chcesz połączyć z silnikiem bazy danych przy użyciu Eksploratora obiektów. Zamiast nazwy maszyny wirtualnej możesz także użyć wartości **(lokalny)** lub jednej kropki jako **Nazwa serwera**. Wybierz pozycję **uwierzytelnianie systemu Windows**i pozostaw ***your_VM_name \ your_local_administrator*** w polu **Nazwa użytkownika** . Kliknij pozycję **Połącz**.
   
    ![Łączenie z serwerem](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. W Eksploratorze obiektów programu SQL Server Management Studio kliknij prawym przyciskiem myszy nazwę wystąpienia programu SQL Server (nazwę maszyny wirtualnej), a następnie kliknij pozycję **Właściwości**.
   
    ![Właściwości serwera](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Na stronie **Zabezpieczenia** w obszarze **Uwierzytelnianie serwera** wybierz pozycję **Tryb uwierzytelniania programu SQL Server i systemu Windows**, a następnie kliknij przycisk **OK**.
   
    ![Wybieranie trybu uwierzytelniania](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. W oknie dialogowym programu SQL Server Management Studio kliknij przycisk **OK**, aby potwierdzić konieczność ponownego uruchomienia programu SQL Server.
6. W Eksploratorze obiektów kliknij prawym przyciskiem myszy swój serwer, a następnie kliknij pozycję **Uruchom ponownie**. (Jeśli program SQL Server Agent jest uruchomiony, również należy go ponownie uruchomić).
   
    ![Ponowne uruchamianie](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. W oknie dialogowym programu SQL Server Management Studio kliknij przycisk **Tak**, aby potwierdzić zgodę na ponowne uruchomienie programu SQL Server.

### <a name="create-sql-server-authentication-logins"></a>Tworzenie identyfikatora logowania do uwierzytelniania w programie SQL Server
Aby nawiązać połączenie z aparatem bazy danych z innego komputera, należy utworzyć przynajmniej jeden identyfikator logowania do uwierzytelniania w programie SQL Server.

1. W Eksploratorze obiektów programu SQL Server Management Studio rozwiń folder wystąpienia serwera, w którym chcesz utworzyć nowy identyfikator logowania.
2. Kliknij prawym przyciskiem myszy folder **Zabezpieczenia**, wskaż pozycję **Nowy** i wybierz pozycję **Identyfikator logowania**.
   
    ![Nowy identyfikator logowania](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. W oknie dialogowym **Identyfikator logowania — Nowy** na stronie **Ogólne** wprowadź nazwę nowego użytkownika w polu **Nazwa logowania**.
4. Wybierz pozycję **Uwierzytelnianie programu SQL Server**.
5. W polu **Hasło** wprowadź hasło dla nowego użytkownika. Wprowadź to hasło ponownie w polu **Potwierdź hasło**.
6. Wybierz wymagane opcje wymuszania hasła (**Wymuszaj zasady haseł**, **Wymuszaj wygaśnięcie hasła** i **Użytkownik musi zmienić hasło przy następnym logowaniu**). Jeśli to Ty będziesz korzystać z tego identyfikatora logowania, nie musisz wymagać zmiany hasła przy następnym logowaniu.
7. Z listy **Domyślna baza danych** wybierz domyślną bazę danych dla identyfikatora logowania. Domyślne ustawienie tej opcji to **master**. Jeśli jeszcze nie utworzono bazy danych użytkownika, pozostaw dla tego ustawienia wartość **master**.
   
    ![Właściwości identyfikatora logowania](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Jeśli jest to pierwszy identyfikator logowania, jaki tworzysz, warto go wyznaczyć jako administratora programu SQL Server. Jeśli tak, na stronie **Role serwera** zaznacz opcję **sysadmin**.
   
   > [!NOTE]
   > Członkowie stałej roli serwera sysadmin mają pełną kontrolę nad aparatem bazy danych. Należy dokładnie ograniczyć członkostwo w tej roli.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Kliknij przycisk OK.

Aby uzyskać więcej informacji na temat identyfikatorów logowania programu SQL Server, zobacz [Tworzenie identyfikatora logowania](https://msdn.microsoft.com/library/aa337562.aspx).

