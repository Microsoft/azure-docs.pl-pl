---
title: Uaktualnienie wersji głównej — Azure Portal-Azure Database for MySQL — pojedynczy serwer
description: W tym artykule opisano, jak uaktualnić wersję główną dla Azure Database for MySQL-pojedynczego serwera przy użyciu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753055"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Uaktualnienie wersji głównej w Azure Database for MySQL pojedynczym serwerze przy użyciu Azure Portal

> [!IMPORTANT]
> Uaktualnienie wersji głównej dla pojedynczego serwera usługi Azure Database for MySQL jest w publicznej wersji zapoznawczej.

W tym artykule opisano, jak można uaktualnić wersję główną programu MySQL w miejscu Azure Database for MySQL jednym serwerze.

Ta funkcja umożliwi klientom wykonywanie uaktualnień w miejscu serwerów MySQL 5,6 do bazy danych MySQL 5,7 przy użyciu kliknięcia przycisku bez przenoszenia danych lub konieczności zmiany parametrów połączenia aplikacji.

> [!Note]
> * Uaktualnienie wersji głównej jest dostępne tylko w przypadku uaktualnienia wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7<br>
> * Uaktualnienie wersji głównej nie jest jeszcze obsługiwane na serwerze repliki.
> * Serwer będzie niedostępny podczas całej operacji uaktualniania. W związku z tym zaleca się przeprowadzanie uaktualnień podczas planowanego okna obsługi.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Azure Database for MySQL pojedynczy serwer](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Wykonaj uaktualnienie wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7

Wykonaj następujące kroki, aby przeprowadzić uaktualnienie wersji głównej usługi Azure Database of MySQL 5,6

> [!IMPORTANT]
> Zalecamy najpierw przeprowadzić uaktualnienie na przywróconej kopii serwera, a nie bezpośrednio uaktualnianiu produkcji. Zobacz [, jak wykonać przywracanie do punktu w czasie](howto-restore-server-portal.md#point-in-time-restore). 

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL 5,6.

2. Na stronie **Przegląd** kliknij przycisk **Uaktualnij** na pasku narzędzi.

3. W sekcji **Uaktualnij** wybierz pozycję **OK** , aby uaktualnić serwer usługi Azure Database for MySQL 5,6 do wersji 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL — Omówienie — uaktualnienie":::

4. Powiadomienie zostanie potwierdzone, że uaktualnienie zakończyło się pomyślnie.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**1. gdy ta funkcja uaktualnienia zostanie osiągnięta, ponieważ w środowisku produkcyjnym będziemy musieli uaktualnić oprogramowanie MySQL w wersji 5.6?**

Ta funkcja jest planowana przed wycofaniem z programu MySQL v 5.6. Jednak funkcja jest gotowa do produkcji i jest w pełni obsługiwana przez platformę Azure, więc należy ją uruchamiać z zachowaniem zaufania do środowiska. Zalecanym najlepszym rozwiązaniem jest wykonanie i przetestowanie go najpierw w przywróconej kopii serwera, aby można było oszacować przestoje podczas uaktualniania i przeprowadzić test zgodności aplikacji przed uruchomieniem go w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [jak wykonać przywracanie do punktu w czasie](howto-restore-server-portal.md#point-in-time-restore) , aby utworzyć kopię punktu w czasie dla serwera. 

**2. czy spowoduje to przestoje serwera, a jeśli tak, jak długo?**

Tak. serwer będzie niedostępny w trakcie procesu uaktualniania, dlatego zalecamy wykonanie tej operacji podczas planowanego okna obsługi. Szacowany czas przestoju zależy od rozmiaru bazy danych, obsługiwanego rozmiaru magazynu (alokowanej liczby operacji we/wy) i liczbie tabel w bazie danych. Czas uaktualnienia jest bezpośrednio proporcjonalny do liczby tabel na serwerze. Uaktualnienia podstawowych serwerów SKU powinny trwać dłużej niż w przypadku standardowej platformy magazynu. Aby oszacować przestoje środowiska serwera, zalecamy najpierw przeprowadzić uaktualnienie na przywróconej kopii serwera.  

**3. należy pamiętać, że nie jest jeszcze obsługiwana na serwerze repliki. Co oznacza konkretną?**

Obecnie uaktualnienie wersji głównej nie jest obsługiwane dla serwera repliki, co oznacza, że nie należy uruchamiać go dla serwerów objętych replikacją (serwera źródłowego lub repliki). Jeśli chcesz przetestować uaktualnienie serwerów związanych z replikacją przed dodaniem obsługi repliki dla funkcji uaktualniania, zalecamy wykonanie następujących czynności:

1. Podczas planowanej konserwacji [Zatrzymaj replikację i Usuń serwer repliki](howto-read-replicas-portal.md) Po przechwyceniu jego nazwy i wszystkich informacji konfiguracyjnych (ustawienia zapory, konfiguracja parametru serwera, jeśli różni się od serwera źródłowego).
2. Wykonaj uaktualnienie serwera źródłowego.
3. Zainicjuj obsługę nowego serwera repliki odczytu o tej samej nazwie i ustawieniach konfiguracji przechwytywanych w kroku 1. Nowy serwer repliki będzie automatycznie w wersji 5.7, gdy serwer źródłowy zostanie uaktualniony do wersji 5.7.

**4. co się stanie, jeśli nie zdecydujesz się na uaktualnienie naszego serwera MySQL v 5.6 przed 5 lutego 2021?**

Nadal możesz korzystać z serwera MySQL v 5.6 jako wcześniej. Na serwerze Azure **nigdy nie zostanie** wymuszone uaktualnienie. Zostaną jednak zastosowane ograniczenia udokumentowane w [zasadach dotyczących wersji Azure Database for MySQL](concepts-version-policy.md) .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zasadach dotyczących wersji Azure Database for MySQL](concepts-version-policy.md).
