---
title: Konfiguracja protokołu TLS — Azure Portal — Azure Database for MariaDB
description: Dowiedz się, jak skonfigurować konfigurację protokołu TLS przy użyciu Azure Portal dla Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241988"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Konfigurowanie ustawień protokołu TLS w Azure Database for MariaDB przy użyciu Azure Portal

W tym artykule opisano, jak skonfigurować serwer Azure Database for MariaDB, aby wymusić minimalną wersję protokołu TLS dla połączeń, aby przełączać i odrzucać wszystkie połączenia z niższą wersją protokołu TLS niż skonfigurowana minimalna wersja protokołu TLS w celu zwiększenia bezpieczeństwa sieci.

W celu nawiązania połączenia z ich Azure Database for MariaDB można wymusić użycie protokołu TLS w celu ustawienia minimalnej wersji protokołu TLS dla serwera bazy danych. Na przykład ustawienie minimalnej wersji protokołu TLS na TLS 1,0 oznacza, że serwer będzie zezwalał na połączenia od klientów przy użyciu protokołu TLS 1,0, 1,1 i 1.2 +. Alternatywnie ustawienie tego ustawienia na 1,2 oznacza, że zezwalasz tylko na połączenia od klientów korzystających z protokołu TLS 1.2 + i wszystkie połączenia z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

* [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Ustawianie konfiguracji protokołu TLS dla Azure Database for MariaDB

Wykonaj następujące kroki, aby ustawić minimalną wersję protokołu TLS dla serwera MariaDB:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MariaDB.

1. Na stronie serwer MariaDB w obszarze **Ustawienia** kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Konfiguracja zabezpieczeń połączenia.

1. W polu **minimalna wersja protokołu TLS** wybierz **1,2** , aby odmówić połączeń z protokołem TLS w wersji niższej niż TLS 1,2 dla serwera MariaDB.

    ![Konfiguracja protokołu TLS Azure Database for MariaDB](./media/howto-tls-configurations/tls-configurations.png)

1. Kliknij przycisk **Zapisz** , aby zapisać zmiany.

1. Powiadomienie zostanie potwierdzone, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    ![Pomyślna Konfiguracja protokołu TLS Azure Database for MariaDB](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat tworzenia alertów dotyczących metryk](howto-alert-metric.md)