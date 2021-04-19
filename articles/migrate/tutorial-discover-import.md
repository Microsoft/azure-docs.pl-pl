---
title: Ocena serwerów lokalnych przy użyciu zaimportowanego pliku CSV z Azure Migrate Server Assessment
description: Opisuje sposób odnajdywania serwerów lokalnych w celu migracji na platformę Azure przy użyciu zaimportowanego pliku CSV w Azure Migrate Server Assessment
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: c142cae3e96d800488b67da613181d1a91ba5b5b
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713322"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Samouczek: ocenianie serwerów przy użyciu zaimportowanego pliku CSV

W ramach procesu migracji na platformę Azure odnajduje się lokalną inwentaryzację i obciążenia. 

W tym samouczku pokazano, jak ocenić maszyny lokalne za pomocą narzędzia Azure Migrate: Server Assessment przy użyciu zaimportowanego pliku wartości rozdzielanych przecinkami (CSV). 

Jeśli używasz pliku CSV, nie musisz skonfigurować urządzenia Azure Migrate odnajdywania i oceniania serwerów. Możesz kontrolować dane, które udostępniasz w pliku, a większość danych jest opcjonalna. Ta metoda jest przydatna, jeśli:

- Chcesz utworzyć szybką, wstępną ocenę przed wdrożeniem urządzenia.
- Nie można wdrożyć urządzenia usługi Azure Migrate w organizacji.
- Nie można udostępnić poświadczeń zezwalających na dostęp do serwerów lokalnych.
- Ograniczenia zabezpieczeń uniemożliwiają zbieranie i wysyłanie danych zebranych przez urządzenie do platformy Azure.

> [!NOTE]
> Nie można migrować serwerów zaimportowanych przy użyciu pliku CSV.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie konta platformy Azure
> * Konfigurowanie projektu usługi Azure Migrate
> * Przygotowywanie pliku CSV
> * Zaimportuj plik
> * Ocenianie serwerów

> [!NOTE]
> Samouczki pokazują najszybszą ścieżkę do wypróbowania scenariusza i używają opcji domyślnych, jeśli to możliwe. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

- Można dodać maksymalnie 20 000 serwerów w jednym pliku CSV i w Azure Migrate projektu. 
- Nazwy systemów operacyjnych określone w pliku CSV muszą zawierać obsługiwane nazwy i [być zgodne z nazwami](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć Azure Migrate projektu, potrzebne jest konto z:
- Uprawnienia współautora lub właściciela w subskrypcji platformy Azure.
- Uprawnienia do rejestrowania Azure Active Directory aplikacji.

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, we współpracy z właścicielem przypisz uprawnienia w następujący sposób:

1. W skrypcie Azure Portal "subskrypcje", a następnie w obszarze **Usługi** wybierz **pozycję Subskrypcje.**

    ![Pole wyszukiwania w celu wyszukania subskrypcji platformy Azure](./media/tutorial-discover-import/search-subscription.png)

2. Na **stronie Subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate subskrypcji. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM) Sprawdź**  >  **dostęp.**
4. W **polu Sprawdź dostęp** wyszukaj odpowiednie konto użytkownika.
5. Na **stronie Dodawanie przypisania roli** wybierz pozycję **Dodaj**.

    ![Wyszukiwanie konta użytkownika w celu sprawdzenia dostępu i przypisania roli](./media/tutorial-discover-import/azure-account-access.png)

6. W **dodaj przypisanie roli**, wybierz rolę Współautor lub Właściciel, a następnie wybierz konto (w naszym przykładzie azmigrateuser). Następnie wybierz pozycję **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-import/assign-role.png)

7. W portalu wyszukaj użytkowników, a następnie w obszarze **Usługi** wybierz pozycję **Użytkownicy.**
8. W **ustawieniach użytkownika** sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie **ustawiono** wartość Tak).

    ![Sprawdź w ustawieniach użytkownika, czy użytkownicy mogą rejestrować aplikacje usługi Active Directory](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate, jeśli go nie masz.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W **przeglądzie** wybierz pozycję **Utwórz projekt**.
5. W **witrynie Create project**(Tworzenie projektu) wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W **szczegółach** projektu określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) [i rządowych.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![Pola nazwy projektu i regionu](./media/tutorial-discover-import/new-project.png)  
    > [!Note]
    > Sekcja **Konfiguracja zaawansowana** umożliwia utworzenie projektu Azure Migrate z łącznością z prywatnym punktem końcowym. [Dowiedz się więcej](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony.

Narzędzie **Azure Migrate: Server Assessment** jest domyślnie dodawane do nowego projektu.

![Strona z domyślnie dodanym narzędziem Server Assessment](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Przygotowywanie pliku CSV

Pobierz szablon CSV i dodaj do niego informacje o serwerze.

### <a name="download-the-template"></a>Pobieranie szablonu

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **Odnajdywanie maszyn** wybierz pozycję **Importuj przy użyciu pliku CSV**.
3. Wybierz pozycję **Pobierz**, aby pobrać szablon CSV. Alternatywnie możesz [pobrać go bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Pobieranie szablonu CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Dodawanie informacji o serwerze

Zbierz dane serwera i dodaj je do pliku CSV.

- Aby zebrać dane, można je wyeksportować z narzędzi używanych do zarządzania serwerem lokalnym, takich jak VMware vSphere lub baza danych zarządzania konfiguracją (CMDB).
- Aby przejrzeć przykładowe dane, pobierz nasz [przykładowy plik](https://go.microsoft.com/fwlink/?linkid=2108405).

Poniższa tabela zawiera podsumowanie pól plików do wypełnienia:

**Nazwa pola** | **Obowiązkowe** | **Szczegóły**
--- | --- | ---
**Nazwa serwera** | Yes | Zalecamy określenie w pełni kwalifikowanej nazwy domeny (FQDN).
**Adres IP** | Nie | Adres serwera.
**Rdzenie** | Yes | Liczba rdzeni procesora przydzielonych do serwera.
**Pamięć** | Yes | Całkowita ilość pamięci RAM (w MB) przydzielona do serwera.
**Nazwa systemu operacyjnego** | Yes | System operacyjny serwera. <br/> Nazwy systemów operacyjnych, które są zgodne lub zawierają nazwy z [tej](#supported-operating-system-names) listy, są rozpoznawane w trakcie oceny.
**Wersja systemu operacyjnego** | Nie | Wersja systemu operacyjnego serwera.
**Architektura systemu operacyjnego** | Nie | Architektura systemu operacyjnego serwera <br/> Prawidłowe wartości to: x64, x86, amd64, 32-bitowy lub 64-bitowy
**Liczba dysków** | Nie | Niepotrzebna, jeśli podano szczegóły dysku.
**Rozmiar dysku 1**  | Nie | Maksymalny rozmiar dysku (w GB).<br/>Możesz dodać szczegóły dla większej liczby dysków, [dodając kolumny](#add-multiple-disks) w szablonie. Można dodać maksymalnie osiem dysków.
**Operacje odczytu dysku 1** | Nie | Operacje odczytu dysku na sekundę.
**Operacje zapisu dysku 1** | Nie | Operacje zapisu dysku na sekundę.
**Przepływność odczytu dysku 1** | Nie | Dane odczytane z dysku na sekundę (w MB na sekundę).
**Przepływność zapisu dysku 1** | Nie | Dane zapisywane na dysku na sekundę (w MB na sekundę).
**Procent użycia procesora CPU** | Nie | Wartość procentowa użycia procesora CPU.
**Procent użycia pamięci** | Nie | Wartość procentowa użycia pamięci RAM.
**Łączna liczba operacji odczytu dysku** | Nie | Operacje odczytu z dysku na sekundę.
**Łączna liczba operacji zapisu na dysku** | Nie | Operacje zapisu na dysku na sekundę.
**Łączna przepływność odczytu dysków** | Nie | Dane odczytane z dysku (w MB na sekundę).
**Łączna przepływność zapisu dysków** | Nie | Dane zapisywane na dysku (w MB na sekundę).
**Przepływność ruchu przychodzącego w sieci** | Nie | Dane odebrane przez serwer (w MB na sekundę).
**Przepływność ruchu wychodzącego w sieci** | Nie | Dane przesyłane przez serwer (w MB na sekundę).
**Typ oprogramowania układowego** | Nie | Oprogramowanie układowe serwera. Wartości to „BIOS” lub „UEFI”.
**Adres MAC**| Nie | Adres MAC serwera.


### <a name="add-operating-systems"></a>Dodawanie systemów operacyjnych

Narzędzie oceny rozpoznaje określone nazwy systemów operacyjnych. Każda określona nazwa musi dokładnie odpowiadać jednemu z ciągów na [liście obsługiwanych nazw](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Dodawanie wielu dysków

Szablon zawiera domyślne pola dla pierwszego dysku. Możesz dodać podobne kolumny dla maksymalnie ośmiu dysków.

Na przykład aby określić wszystkie pola dla drugiego dysku, dodaj następujące kolumny:

- Rozmiar dysku 2
- Operacje odczytu dysku 2
- Operacje zapisu dysku 2
- Przepływność odczytu dysku 2
- Przepływność zapisu na dysku 2


## <a name="import-the-server-information"></a>Importowanie informacji o serwerze

Po dodaniu informacji do szablonu CSV zaimportuj plik CSV do programu Server Assessment.

1. W usłudze Azure Migrate, w obszarze **Odnajdywanie maszyn** przejdź do ukończonego szablonu.
2. Wybierz pozycję **Importuj**.
3. Zostanie wyświetlony stan importowania.
    - Jeśli w stanie pojawiły się ostrzeżenia, możesz je naprawić lub kontynuować bez ich rozwiązywania.
    - Aby poprawić dokładność oceny, popraw informacje o serwerze zgodnie z opisem w ostrzeżeniach.
    - Aby wyświetlić i naprawić ostrzeżenia, wybierz pozycję **Pobierz plik CSV ze szczegółami ostrzeżenia**. Ta operacja pobiera plik CSV wraz z ostrzeżeniami. Przejrzyj ostrzeżenia i rozwiąż problemy zgodnie z wymaganiami.
    - Jeśli w stanie pojawią się błędy, co powoduje, że stan importu wskazuje **Niepowodzenie**, należy naprawić te błędy przed kontynuowaniem importowania:
        1. Pobierz plik CSV, który zawiera teraz szczegóły błędu.
        1. Przejrzyj błędy i rozwiąż je w razie potrzeby. 
        1. Ponownie przekaż zmodyfikowany plik.
4. Gdy stan importowania ma wartość **Ukończono**, informacje o serwerze zostały zaimportowane. Odśwież, jeśli wydaje się, że proces importowania nie jest ukończony.

## <a name="update-server-information"></a>Aktualizowanie informacji o serwerze

Możesz zaktualizować informacje dotyczące serwera, importując ponownie dane serwera przy użyciu tej samej **nazwy serwera**. Nie można zmodyfikować pola **Nazwa serwera**. Usuwanie serwerów nie jest obecnie obsługiwane.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Aby sprawdzić, czy serwery są wyświetlane w witrynie Azure Portal po wykonaniu operacji odnajdywania:

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. Na stronie **Azure Migrate — Serwery** > **Azure Migrate: Server Assessment** wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.
3. Wybierz kartę **Na podstawie importu**.



## <a name="supported-operating-system-names"></a>Obsługiwane nazwy systemów operacyjnych

Nazwy systemów operacyjnych podane w pliku CSV muszą zawierać i być zgodne. Jeśli nie, nie będzie można ich ocenić. 

**A–H** | **I-R** | **S-T** | **U–Z**
--- | --- | --- | ---
Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>macOS X 10<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 z dodatkiem R2<br/>Windows Server 2012<br/>Windows Server 2012 z dodatkiem R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utworzono Azure Migrate projekt 
> * Odnalezione serwery używające zaimportowanego pliku CSV. Teraz uruchom ocenę migracji maszyny [wirtualnej VMware na maszyny wirtualne platformy Azure.](./tutorial-assess-vmware-azure-vm.md)