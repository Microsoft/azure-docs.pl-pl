---
title: Konfiguracja protokołu TLS-Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak ustawić konfigurację protokołu TLS przy użyciu Azure Portal dla Azure Database for PostgreSQL jednego serwera
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: ed5d46bbb5a520ee28013210ba44045ad59c2cc2
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84705704"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Konfigurowanie ustawień protokołu TLS w Azure Database for PostgreSQL jednym serwerze przy użyciu Azure Portal

W tym artykule opisano, jak można skonfigurować Azure Database for PostgreSQL, aby wymusić minimalną wersję protokołu TLS dozwoloną dla połączeń i odmówić wszystkich połączeń z niższą wersją protokołu TLS niż skonfigurowana minimalna wersja protokołu TLS w celu zwiększenia bezpieczeństwa sieci.

W celu nawiązania połączenia z ich Azure Database for PostgreSQL można wymusić stosowanie protokołu TLS. Klienci mają teraz możliwość ustawienia minimalnej wersji protokołu TLS dla serwera bazy danych. Na przykład ustawienie minimalnej wersji protokołu TLS na TLS 1,0 oznacza, że serwer będzie zezwalał na połączenia od klientów przy użyciu protokołu TLS 1,0, 1,1 i 1.2 +. Zamiast tego ustawienie minimalnej wersji protokołu TLS na 1,2 + oznacza, że zezwalasz tylko na połączenia od klientów korzystających z protokołu TLS 1,2 i wszystkie połączenia z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Ustawianie konfiguracji protokołu TLS dla Azure Database for PostgreSQL-pojedynczego serwera

Wykonaj następujące kroki, aby ustawić PostgreSQL minimalną wersję protokołu TLS:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejące Azure Database for PostgreSQL.

1.  Na stronie Azure Database for PostgreSQL — pojedynczy serwer w obszarze **Ustawienia**kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Konfiguracja zabezpieczeń połączenia.

1. W polu **minimalna wersja protokołu TLS**wybierz **1,2** , aby odmówić połączeń z protokołem TLS w wersji niższej niż TLS 1,2 dla serwera PostgreSQL.

    ![Azure Database for PostgreSQL Konfiguracja protokołu TLS na jednym serwerze](./media/howto-tls-configurations/setting-tls-value.png)

1. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

1. Powiadomienie zostanie potwierdzone, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    ![Azure Database for PostgreSQL — powodzenie konfiguracji protokołu TLS pojedynczego serwera](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat tworzenia alertów dotyczących metryk](howto-alert-on-metric.md)