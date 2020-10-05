---
title: Rozwiązywanie typowych błędów — Azure Database for MySQL
description: Dowiedz się, jak rozwiązywać typowe błędy migracji napotykane przez użytkowników nowych do usługi Azure Database for MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88720251"
---
# <a name="common-errors"></a>Typowe błędy

Azure Database for MySQL to w pełni zarządzana usługa oparta na wersji społeczności MySQL. Środowisko MySQL w środowisku usługi zarządzanej może się różnić od uruchamiania programu MySQL w Twoim środowisku. W tym artykule zobaczysz niektóre typowe błędy, które użytkownicy mogą napotkać podczas migrowania lub opracowywania usługi Azure Database for MySQL po raz pierwszy.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Błędy ze względu na brak uprawnienia administratora i roli administratora systemu

Rola noprivilege i DBA nie są obsługiwane w usłudze. W związku z tym mogą wystąpić następujące typowe błędy wymienione poniżej:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>BŁĄD 1419: nie masz włączonego uprawnienia i rejestrowanie danych binarnych ( *może* być konieczne użycie mniej bezpiecznej log_bin_trust_function_creators zmiennej).

Powyższy błąd może wystąpić podczas tworzenia funkcji, wyzwalacza jak poniżej lub importowania schematu. Instrukcje języka DDL, takie jak CREATE FUNCTION lub CREATE TRIGGER, są zapisywane w dzienniku binarnym, więc replika pomocnicza może je uruchomić. Wątek SQL repliki ma pełne uprawnienia, które można wykorzystać w celu podniesienia uprawnień. Aby zapewnić ochronę przed tym zagrożeniem w przypadku serwerów z włączonym logowaniem binarnym, aparat MySQL wymaga, aby w przypadku wszystkich wymaganych przywilejów tworzenia musi mieć uprawnienia administratora. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Rozwiązanie**: Aby rozwiązać ten problem, ustaw log_bin_trust_function_creators na 1 z bloku [parametrów serwera](howto-server-parameters.md) w portalu, wykonaj instrukcje języka DDL lub zaimportuj schemat w celu utworzenia żądanych obiektów i Przywróć poprzednią wartość parametru log_bin_trust_function_creators po utworzeniu.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>BŁĄD 1227 (42000) w wierszu 101: odmowa dostępu; potrzebujesz (co najmniej jednego z tych uprawnień) dla tej operacji. Operacja nie powiodła się z ExitCode 1

Powyższy błąd może wystąpić podczas importowania pliku zrzutu lub procedury tworzenia, która zawiera [definicje.](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html) 

**Rozwiązanie**: Aby rozwiązać ten problem, administrator może udzielić uprawnień do tworzenia lub wykonywania procedur przez uruchomienie polecenia Grant, jak w następujących przykładach:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Alternatywnie można zastąpić definiujy nazwą użytkownika administracyjnego, który uruchamia proces importowania, jak pokazano poniżej.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>Następne kroki
Jeśli nie znajdziesz odpowiedzi, której szukasz, weź pod uwagę następujące kwestie:
- Opublikuj pytanie w witrynie [Microsoft Q&stronie pytania](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Wyślij wiadomość e-mail do Azure Database for MySQL zespołu [ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Ten adres e-mail nie jest aliasem pomocy technicznej.
- Skontaktuj się z pomocą techniczną platformy Azure, aby [uzyskać bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
