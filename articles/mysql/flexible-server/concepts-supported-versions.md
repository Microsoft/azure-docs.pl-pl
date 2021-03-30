---
title: Obsługiwane wersje — Azure Database for MySQL elastyczny serwer
description: Dowiedz się, które wersje serwera MySQL są obsługiwane na serwerze elastycznym Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7ad6a576262b8e722b16c81af544a9370c2b49b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242266"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Obsługiwane wersje dla Azure Database for MySQL-elastyczny serwer


> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.


Azure Database for MySQL elastyczny serwer jest obsługiwany przez program [MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu InnoDB.

Baza danych MySQL używa schematu nazewnictwa X. Y. Z. X jest wersją główną, Y jest wersją pomocniczą, a Z Z to Poprawka błędu Release. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL.

Azure Database for MySQL obecnie obsługuje następujące wersje:

## <a name="mysql-version-57"></a>Baza danych MySQL w wersji 5,7

Poprawka poprawek usterek: 5.7.29

Usługa wykonuje zautomatyzowane stosowanie poprawek podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych. Poprawki obejmują aktualizacje zabezpieczeń i oprogramowania. W przypadku aparatu MySQL uaktualnienia wersji pomocniczej są również dołączone jako część planowanej wersji konserwacji. Użytkownicy mogą konfigurować harmonogram stosowania poprawek, aby był zarządzany przez system lub definiować własny harmonogram. W harmonogramie konserwacji poprawka zostanie zastosowana i serwer może wymagać ponownego uruchomienia w ramach procesu poprawek, aby ukończyć aktualizację. Zgodnie z harmonogramem niestandardowym użytkownicy mogą przewidywalnić swój cykl poprawek i wybrać okno obsługi z minimalnym wpływem na firmę. Ogólnie rzecz biorąc usługa jest zgodna z harmonogramem wydań w ramach ciągłej integracji i wydania.

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawkami do poprawek błędów wersji. Na przykład 5.7.29 do 5.7.30.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Tworzenie aplikacji PHP w systemie Windows za pomocą programu MySQL](../../app-service/tutorial-php-mysql-app.md)<br/>
>[Tworzenie aplikacji PHP w systemie Linux przy użyciu programu MySQL](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[Tworzenie aplikacji sprężynowej opartej na języku Java za pomocą programu MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>